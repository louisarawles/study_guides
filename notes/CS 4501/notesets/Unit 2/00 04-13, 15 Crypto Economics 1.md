# **04-13/15 Crypto Economics**

**04-13**
_Applications_
* Auctions
* Exchanges
* Governance
* Currency
  * Our focus today -- how to make currency on a decentralized system

_Barter_
* Coincidence of wants
* ie: Alice and Bob; A has apple, B has orange, => they can exchange 
* cycle gets more complicated when you have more than 2 people ...
* ie: Alice, Bob, and Charlie; A has apple, B has orange, C has grape; C's preferences: Orange < Grape < Apple
* ... why money is useful ...

_With Money_
* ie: 
  * (where preferences work with this example and everyone gets what they want)
  * C give A money for the apple, A give B money for the orange, B give C money for grapes  

_Gold Standard_
* Q: First gold coins were minted around 610 BC. Why gold? 
  * A: Gold keeps the same form overtime
* Later: paper currency. Countries adopt "gold standard" in the 1800s to build trust
* _Bretton Woods System_ 1946 collapsed during the Vietnam War
  * Q: Why did the gold standard end?
    * A: The government wanted to print more money, but under the gold standard they were tied to gold, so they moved to _fiat_

_Fiat Currency_
* "fiat" = "let it be done"
* _Value_: only tied to expectation that in the future you can exchange it to buy yourself something
* Origin: 11th century China

_Gold vs Fiat: Pros and Cons_
* Pros:
  * Fiat:
  * Gold:
    * Pegged, less volatile
* Cons:
  * Fiat:
    * Risks with too much money printing
    * No longer pegged, more volatile

_Digital Currency_
* Q: Why digital currency?
  * A: 
    * Transaction costs are significantly lower
    * Algorithmically controlled supply
    * Transferable "via code without intermediaries, bust costly in PoW"
    * Cheap storage
    * Division (simple additions and comparisons "a + b = c + d")
    * Hard to fake (in Cryptomania [1])
* Q: Suppose you wish to purchase an online software. How would you transfer payment?
  * A: 
    * At any time, any buyer b can generate a transaction to pay any seller any amount d of PayPal dollars
    * PayPal hears about the transaction and checks it for validity
    * If valid, PayPal confirms to the participants that the transaction occured
    * PayPal takes notes that buyer b has d fewer PayPal dollars and seller s has d more PayPal dollars (no physical money is ever moved)
    * You must have a bank account attached to your account when you register to transfer money to/from your bank account and convert PayPal dollars for real dollars

_Attacks_
* Q: What are some vulnerabilities / risks of this system PayPal
  * A: Leaks; PayPal dollars somehow change in value compared to real dollars
* _Steal money_: falsely generated transaction
  * Defense: public key cryptography
* _Privacy_: PayPal shares your spending history with the gov or others
* _Rewrite History_: PayPal could modify or delete transaction from ledger
* _Censorship_: PayPal decides to no longer authorize transactions

_Attack Solutions_
* Privacy Solution:
    * At any time, any buyer b can generate a transaction to pay any seller any amount d of PayPal dollars
    * PayPal hears about the transaction and checks it for validity
    * If valid, PayPal confirms to the participants that the transaction occured
    * PayPal takes notes that buyer b has d fewer PayPal dollars and seller s has d more PayPal dollars (no physical money is ever moved)
    _* You can make as many PayPal accounts as you like, for free. You only need a bank account attached in order to trade your PayPal dollars for real dollars_
* Protecting Against History Rewriting Solution:
  * At any time, any buyer b can generate a transaction to pay any seller any amount d of PayPal dollars
  * PayPal hears about the transaction and checks it for validity
  * If valid, PayPal confirms to the participants that the transaction occured, _and broadcasts to the entire network that this transaction occurred_
  * PayPal, _and all participants_, take notes that buyer b has d fewer PayPal dollars and seller s has d more PayPal dollars (no physical money is ever moved)
  * You can make as many PayPal accounts as you like, for free. You only need a bank account attached in order to trade your PayPal dollars for real dollars
* Protecting Against Money Freeze Solution
  * At any time, any buyer b can generate a transaction to pay any seller any amount d of PayPal dollars and broadcast it to the entire network
  * At every step, a member of the network (the leader) is randomly selected to authorize transactions. That member broadcasts all transactions they consider to be valid
  * Based on transactions that were authorized, all participants verify if the authorized transactions are valid. If so, update their perceived balances.
  * You can make as many PayPal accounts as you like, for free. You only need a bank account attached in order to trade your PayPal dollars for real dollars
* Q: what's the problem with the money freeze solution?
  * A (idea):
    * What happens if multiple members exchange at the same time, and one of them is chosen as the leader, and they can authorize the transaction
  * A:
    * _Sybil attack_
    * Occurs at: "At every step, a member of the network (the leader) is randomly selected to authorize transactions. That member broadcasts all transactions they consider to be valid"
    * Motivation: leader receives some sort of monetary reward
* Protecting Against Sybil Attack:
  * At any time, any buyer b can generate a transaction to pay any seller any amount d of PayPal dollars and broadcast it to the entire network
  * At every step, _A leader selection mechanism (ie, PoW) chooses a member of the network to authorize transactions_. That member broadcasts all transactions they consider to be valid
  * Based on transactions that were authorized, all participants verify if the authorized transactions are valid. If so, update their perceived balances.
  * You can make as many PayPal accounts as you like, for free. You only need a bank account attached in order to trade your PayPal dollars for real dollars

_The Challenge_

_Cryptographies_
* Public key encryption
* Hash Function
  * Y = H(X): **`{0,1}^1024`** --> **`{0,1}^256`**
  * Assumptions:
    * Efficient: given X, easy to compute Hash(X)
    * One-Way: given Y=H(X), it is _hard_ to find X such that H(X)=Y
    * Collision-Resistant: given (X,Y), _hard_ to find X' s.t. H(X')=Y
    * Random Oracle: given X, H(X) is uniformly random over **`{0,1}^256`**
      * ie: H(X)=Z, Z **`\in_R`** **`{0,1}^256`**

_Proof-of-Work_
* PoW(H): find an X s.t. H(X) starts with 76 zero bits
* Q: why does 
  * A: 

**04-15**
* sybil proofness
* currency is generated in the protocol (incentives)

Key Question: is bitcoin protocol incentive compatible (or strategy proof)

_Difficult Adjustment_
Objective: A new block is created every 10 minutes
Puzzle: 76 trailing zeros in the PoW
* The puzzle makes it harder to makes blocks, which enforces the protocol objective
* the 10 minutes helps with latency of internet, as it allows time for everyone to receive the broadcast message

Q: Why is the argument that better hardware would save energy false:
A: argument: better hardware can't save more energy because you would mine for the same amount of time but just mine more blocks, so the puzzle gets much more difficult

Answering Key Question: Model Mining Protocol
* do people have incentive to follow mining protocol

_Mining Game_
* Block (Q, B', id, x)
  * Q: transactions
  * B': ancestor
  * id: id of the miner
  * x: nonce
* A block B is valid if:
  * B' is valid
  * All transactions are valid (aka Q contains only valid transactions)
  * Hash of the block contains the target( H(Q, B', id, x) < target(PoW) )

Local View
* G: public blockchain
* insert graph of blocks in blockchain

Player **P_i**
* **G_p_i**: view of player i
* has views of B5 and B4, but no one else has a view of those blocks, which is key to why and how he's going to be strategic

Game:
* M: set of miners/players
* **x_i**: is mining power of miners
  * ie, how many hashes you can compute per second
  * \sum x_i = 1
* Each time step t, nature samples from dist x and the sampled miner gets to create a block (ie, the leader)
  * Whenever you get chosen to be the leader, you can choose what block you point to next
* The leader creates a block v pointing to some block k \in G_i
* Any miner i can announce any private blocks to make them become public
  * Private blocks are initially hidden, and someone can make any of their private blocks to become public
* Q: why, when you're making a decision to make a private block public, do you have to make the block point into a specific block, the ancestor block? 
* A: because the pointer goes to the ancestor block, which is part of the hash, so without it, you don't get PoW, so you have to commit to the ancestor when going public
  * In proof of stake (PoS), you don't have to commit in the same way, but here in this example we assume that the block already has an ancestor which you must commit to
* Play the game for capital T rounds

Payoff__{P_i}: E[ lim__{T -> \inf} # player i / # Total] = x_i
* trying to maximize your market cap or your rate of return
![revenue of pool and others](04-15%2001%20revenue.png){size=small}
![total revenue/payoff of pool](04-15%2002%20revenue%202.png){size=small}

_Longest Chain Rule_
* You must point your block to the longest chain
* You should make your blocks public at the time of creation
* Q: what should your payoff be if everyone is doing this?
* A: your computing power, x_i

_Selfish Mining (Powerful)_
Assumption: the network breaks ties in favor of the adversary
Strategy:
1. Mine on top of the longest chain, but keep it private
2. If anyone announces a block B, and you have B' with the same height as B, you announce B

* Follow what designer told you in first steps of protocol, but deviate in second bullet by not making blocks public, and instead revealing blocks only when the network announces a block and your blocks are the same height

EX:

init:
          o
        / 
       o
      : \
      o  o (a)
      :
      o
network announces a, so you announce (c):
          o
         / 
        o
      /  \
     o(c) o(a)
     :
     o
* Network will ignore block a, pruning a, linking onto c
          o
        / 
       o
      / \
     o   o
     : \
     o  o

_THM_: if the network use LC and the adversary uses (Strong Selfish Mining) the adversary's payoff is \alph / (1 - \alph) where \alph is their mining power
* note, \alph is a probability
Proof:
![state machine w transition frequencies](04-15%2003%20state%20machine%20w%20transition%20frequencies.png){size=small}

   -1-\alph->    --\alph-->  --\alph-->   --\alph-->
  '----------- 0            1           2            ...
                 <-1-\alph-   <-1-\alph-  <-1-\alph-

* po, p1, p2, p1 = \alph * po + (1 - \alph)p2

State 0: adversary has no hidden blocks
State i: adversary has i hidden blocks
* whenever at state i, find a block, now at state i+1

_Stationary Distribution:_
* p is a stationary distrib if pM = p where M is the transition probability of the markov chain

_THM_: payoff of i = ( \sum_{s} p_s * \sum_s' M(s,s')R_i(s,s') ) / ( \sum_i \sum_s p_s * \sum M(s,s')R_i(s,s') )
* if your strategy has stationary distribution, you can simplify the limit to this equation
* where R_i(s,s') is the number of blocks player i adds to the longest chain when moving from s to s'

_THM_: if c satisfies E[(1-c)R_1(x_\tao)-cR_2(x_\tao)] = o, then c = payoff of the adversary
* 1. counting how many blocks computing before time resets, 2. how many blocks others computing before time resets, and if you can find a c where expected value is zero, then c is exactly the expected payoff of the adversary
* probably simpler than the prev theorem because don't need to find the stationary distribution, you just have to understand what the \tao looks like

* x_0 = 0, x_1, ... , the sequence of states visited.
* Let \tao >= 1 be the first time where X_\tao = 0.
* R_1(x_\tao): how many blocks adversary adds to the longest chain until time \tao
* R_2(x_\tao): # blocks others add

EX:
* x_1=0, \tao=1
  * (1-c)*0-c*1
* x_1=1, \tao=?
  * (same graph as before, but now 1 is poing into 0, and 0 doesnt have the self-cycle or the \alph going into 1)
  * im not sure why but we know cR_2(x_\tao) is 0, so only concerned about the value of the second term
  * E[\gam] = (1-\alph)*0 + \alph(1 + E[\gam] + E[\gam])
    * = \alph / (1-2\alph)
      * the first part, you're at 1, and you move one to the left, you just have 1-\alph and no more moves
      * the second part, still at 1, you move the number of times till you return to 1
      * confused, but you have to match the number of moves you take and that's why you get E[\gam] twice in the second part
  * Now, we have
    * = (1-2)(-c) + \alph(1-c)(1+\alph/(1-2\alph)) = 0
      * x_1 = 0: (1-2)(-c)
      * x_1 = 1: \alph(1-c)(1+\alph/(1-2\alph))