## Week 15 — Spectre, Timing Attacks, Cache Sets

Consider the following C pseudocode:

    struct FileInfo {
        char filename[496];
        long size;
        long download_time;
    };

    struct FileInfo *files;
    int number_of_files;

    struct Download {
        char url[504];
        bool done_download;
        int file_index;
    };

    struct Download *downloads;
    int number_of_downloads;

    long GetDownloadSize(long download_id) {
        if (download_id > 0 && download_id < number_of_downloads) {
            struct Download *download = &downloads[download_id];
            if (download->done_download) {
                if (download->file_index >= 0 && download->file_index < number_of_files) {
                    struct File * files = &files[download->file_index];
                    return files->size;
                } else {
                    return -1;
                }
            } else {
                return -2;
            }
        } else {
            return -1;
        }
    }

For the following questions, assume that the size of both structs above is 512 bytes and that each of the if statements 
above are implemented using a conditional jump that is not taken if the condition is true.

The GetDownloadSize function is vulnerable to the Spectre attack we discussed in lecture if an attacker can control the 
value of download_id provided. In particular, the access to files->size may evict a value from the cache when it partially 
executes as a result of branch prediction (even though its other effects will be undone). 
By choosing download_id correctly and measuring which cache set the access to files->size evicts when executed 
speculatively, they attacker can learn about a value in memory they should not be able to access.

**Question 1**

Before doing the attack, the attacker should train the branch 
predictor so that download_id > 0 && download_id < number_of_downloads 
is ___.


1. predicted true
{{ ✅ yes — attacker wants the bounds check to be speculatively bypassed }}

2. predicted false
{{ ❌ would prevent speculative execution from reaching the secret-dependent access }}

3. it does not matter
{{ ❌ it matters because Spectre relies on wrong-path speculative execution }}

_Answer_: {{ predicted as true }}

**Question 2**
Before doing the attack, the attacker should train the branch predictor so that download->done_download is ___.

1. predicted true
{{ ✅ yes — attacker wants execution to continue deeper }}

2. predicted false
{{ ❌ would stop before reaching files->size }}

3. it does not matter
{{ ❌ it matters because this branch controls whether the vulnerable access executes }}


_Answer_: {{ predicted as true }}

**Question 3**
Before doing the attack, the attacker should train the branch predictor so that download->file_index >= 0 && download->file_index < number_of_files is ___.

1. predicted true
{{ ✅ yes — attacker wants speculative execution to reach files->size }}

2. predicted false
{{ ❌ would skip the secret-dependent memory access }}

3. it does not matter
{{ ❌ this branch must be mispredicted in the attacker’s favor }}


_Answer_: {{ predicted as true }}

**Question 4**
The attacker should set the value of download_id such that the value they want to learn about is the same value the code will access if tries to access ___.

1. downloads[download_id].done_download
{{ ❌ this field only controls whether execution continues; it is not used to index into files }}

2. downloads[download_id].file_index
{{ ✅ yes — this value becomes the index into files, so it controls which cache set files->size touches }}

3. files[downloads[download_id % number_of_downloads].file_index].filename
{{ ❌ modulo would keep access in-bounds, but Spectre needs out-of-bounds speculative access }}

4. files[downloads[download_id % number_of_downloads].file_index].size
{{ ❌ same modulo issue; this is not the unchecked speculative path }}

5. files[downloads[download_id].file_index].filename
{{ ❌ close, but the leaked value is read as file_index, not from filename }}

6. files[downloads[download_id].file_index].size
{{ ❌ this is the later cache access whose set reveals information, but the value being learned is the maliciously accessed file_index }}

7. files[download_id].size
{{ ❌ download_id indexes downloads, not files directly }}

_Answer_: {{ downloads[download_id].file_index }}

_Reasoning_:
{{ The attacker chooses an out-of-bounds download_id so that downloads[download_id].file_index actually reads some secret value in memory. That secret value is then used as the index into files, and the cache set touched by files[secret].size leaks information about the secret. }}

-------------------------------------------------------------------

Consider the following C function:

int hidden_x, hidden_y;

bool is_close(int x, int y) {
    if (x >= hidden_x - 1) {
        if (y >= hidden_y - 1) {
            if (x <= hidden_x + 1) {
                if (y <= hidden_y + 1) {
                    return true;
                }
            }
        }
    }
    return false;
}
Assume the compiler generates code for this function where each if statement is compiled to a conditional jump.

Consider the following observations:

![image](q15_5_6.png){size=medium}

**Question 5**

Based on the observations above, what is a probable value of hidden_x?

_Answer_: {{ 749, 750, or 751 }}

_Reasoning_:
{{ Basically execution time jumps up here then sort of drops, so probably the value; Execution time jumps when x = 750 or x = 1000, meaning the first branch x >= hidden_x - 1 is probably true there. But when x = 625, time stays low, meaning the first branch is probably false. So hidden_x is probably around 750. }}

**Question 6**

Based on the observations above, what is a probable value of hidden_y?


_Answer_: {{ anything from 501 to 626 }}

_Reasoning_:
{{ Basically same thought process as before; once x is large enough to pass the first condition, timing becomes longer when y = 625, 750, or 1000, but shorter when y = 500. That suggests the second branch y >= hidden_y - 1 begins passing somewhere after 500 and no later than 625. }}

-------------------------------------------------------------------

**Question 7**

* _NOTE_: you can assume that the cache set is already full since the problem is basically saying the access evicts from cache set 54, implying the set had no free way available so bringing in this block caused a replacement

Code:

    array[148 * mystery] += 1;

Cache info:


cache size = {{ 128 KiB = 131072 bytes }}


associativity = {{ 2-way }}


block size = {{ 64 bytes }}


array element size = {{ 4 bytes }}


array[0] address = {{ 0x123400 }}


array[0] set index = {{ 0xd0 = 208 }}


target evicted set = {{ 54 }}



1. Step 1: number of sets
sets = cache size / (block size × associativity)
{{ 131072 / (64 × 2) = 1024 sets }}
So index bits = {{ 10 }}.

2. Step 2: offset bits
Block size = 64 bytes, so:
{{ 6 offset bits }}

3. Step 3: address accessed
array[148 * mystery]
Each array element is 4 bytes, so byte offset is:
{{ 148 × mystery × 4 = 592m }}

4. Step 4: convert bytes to cache blocks
Each block is 64 bytes:
{{ floor(592m / 64) = floor(148m / 16) }}

5. Step 5: set equation
Start set:
{{ 0xd0 = 208 }}
Target set:
{{ 54 }}
So:
(208 + floor(148m / 16)) mod 1024 = 54
Therefore:
{{ floor(148m / 16) = 870 + 1024K }}

6. Step 6: one working value
Try K = 7:
{{ floor(148 × 869 / 16) = floor(8038.25) = 8038 }}
Then:
{{ 208 + 8038 = 8246 }}
And:
{{ 8246 mod 1024 = 54 }}

_Answer_: {{ 869 }}

_Reasoning_:
{{ mystery = 869 makes array[148 * mystery] land in cache set 54. }}

I also think it's helpful to see this in code for a more systematic approach:

    from cmath import exp
    from math import floor

    # QUESTION 7:
    # Consider the following C code:
    #
    # array[148 * mystery] += 1;
    # Suppose we discover that running the above code evicts from cache set 54 in a 2-way, 128KiB (131072 byte) cache with 64-byte blocks and an LRU replacement policy, and:
    #
    # the system does not use virtual memory;
    # array is an array of 4-byte integers; and
    # array[0] is located at address 0x123400 (which maps to tag 0x12, set index 0xd0, offset 0 in the cache)
    # What is a possible value of mystery?

    # base address at array[0]
    base = 0x123400

    # target cache set
    target_set = 54

    # page offset size --> necessary for shifting address before applying the mask
    # block_size = 2 ** po_bits
    # = po_bits = log_2(block size)
        # po_bits = log_2(64) = 6
    po_bits = 6

    # number of sets
        # num_sets = 2 ** indx_bits
        #   13107 byte cache, 64-byte blocks, 2-way sets
        #   num_blocks = total cache size / block size
        #       num_blocks = 13107 / 64 = 2048
        #   num_sets = num_blocks / # ways
        #       num_sets = 2048 / 2 = 1024
    num_sets = 1024

    # mask
    # num_sets-1 = the mask we will need to make to isolate the index
    mask = 1023 # 0x3ff

    # NOTE: since you know the hex values of the address 0x123400 and set index 0xd0,
    # you could also just translate them both to binary and if theres only one option where the set index could be,
    # then you can just count the number of bits it takes up but this probs won't always work

    # initialize mystery val = 0
    mystery = 0
    # hypothetically could use variables to keep track of possible mystery values found if the first one don't work
        for mystery in range(5000):
            # address (floored bc of rounding) is:
                # the array index, 148 * mystery,
                # * 4 -- to account for it being a 4-byte integer array (rather than a 1-byte char array)
                # + base, since array[0] starts at 0x123400
            addr = floor(148 * mystery * 4) + base
            # shift out the po bits, mask out the upper tag bits
            set_index = (addr >> po_bits) & mask

    if set_index == target_set: # 0b110110
        print("Equal!")
        print("mystery:", mystery, "\naddr:", hex(addr), "\nset:", set_index)
        break