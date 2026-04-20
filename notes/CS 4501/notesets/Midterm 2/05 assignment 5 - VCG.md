# ASSIGNMENT 5: VCG MECHANISM

## Reverse Auction (VCG)

### Setup
- sellers have costs c_i

### Allocation rule
- choose seller with {{lowest reported cost}}

### Payment rule
- winner receives {{second-lowest cost}} :contentReference[oaicite:1]{index=1}

---

## Combinatorial Auction Example

Values:
- bidder 1: {A,B} = 100
- bidder 2: A = 70
- bidder 3: B = 50

### Efficient allocation
- assign A → {{bidder 2}}
- assign B → {{bidder 3}}

---

### Payments

- bidder 2 pays {{50}}
- bidder 3 pays {{30}}

Interpretation:
- they pay the {{externality imposed on others}}

---

## Public Project Insight

If payments cover cost C:
- either total value = {{C}}
- or exactly {{one agent has non-zero value}}

---

## Key VCG Formula

- payment = {{others' value without i − others' value with i}}

---

## Core Property

VCG is strategy-proof because:
- agents maximize {{true utility regardless of report}}

# ASSIGNMENT 5: VCG MECHANISM

## Question 1(a): Reverse Auction

### Prompt
Describe VCG for reverse auction.

---

### Allocation

- choose:
  {{seller with lowest reported cost}}

---

### Payment

- winner receives:
  {{second-lowest cost}} :contentReference[oaicite:1]{index=1}

---

## Question 1(b): Combinatorial VCG

### Prompt
Find allocation and payments.

---

### Allocation

- efficient allocation:
  {{A → bidder 2, B → bidder 3}}

---

### Payments

- bidder 2 pays:
  {{50}}

- bidder 3 pays:
  {{30}}

---

### Interpretation

- payments equal:
  {{externality imposed on others}}

---

## Question 1(c): Public Project

### Prompt
When do VCG payments cover cost?

---

### Cases

- total value = {{C}}
- OR one agent has {{nonzero value}}

---

## Question 1(e): Strategy-proofness

### Prompt
Why is VCG strategy-proof?

---

### Key Step

- define menu:
  {{B_i(v_{-i}) independent of i’s report}}

---

### Conclusion

- VCG selects:
  {{allocation maximizing total value}}

- therefore:
  {{truth-telling maximizes utility}}