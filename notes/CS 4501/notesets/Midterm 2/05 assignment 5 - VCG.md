# ASSIGNMENT 5: VCG MECHANISM

---

## Question 1. VCG Mechanisms

---

### (a) Reverse Auction

Consider a reverse auction where sellers report costs.

Describe the VCG allocation and payment rule.

---

### Allocation Rule

- choose seller with:
  {{lowest reported cost}}

---

### Payment Rule

- winner receives:
  {{second-lowest reported cost}}

---

### Key Insight

- reverse VCG = 
  {{“second-price” for costs}}

---

---

### (b) Combinatorial Auction

There are two items A and B and three bidders:

- bidder 1: v({A,B}) = 100  
- bidder 2: v(A) = 70  
- bidder 3: v(B) = 50  

Find the efficient allocation and VCG payments.

---

### Efficient Allocation

- option 1: bidder 1 gets both → value = {{100}}
- option 2: bidder 2 gets A, bidder 3 gets B → value = {{120}}

---

### Allocation

- A → {{bidder 2}}
- B → {{bidder 3}}

---

### Payment for Bidder 2

- remove bidder 2:
  best allocation = {{bidder 1 gets both → value 100}}

- with bidder 2:
  others get = {{50}}

---

- payment:
  {{100 − 50 = 50}}

---

### Payment for Bidder 3

- remove bidder 3:
  best allocation = {{bidder 1 gets both → value 100}}

- with bidder 3:
  others get = {{70}}

---

- payment:
  {{100 − 70 = 30}}

---

### Key Insight

- each bidder pays:
  {{externality imposed on others}}

---

---

### (c) Public Project Problem
![image 1](05-1c.png){size=medium}
Consider a public project with cost C.

Under what conditions do VCG payments exactly cover the cost?

---

### Cases

- total value = {{C}}

OR

- exactly {{one agent has positive value}}

---

### Key Insight

- VCG may:
  {{fail to balance budget}}

---

---

### (d) Menu Interpretation

Explain the “menu” interpretation of VCG.

---

### Key Idea

- define:
  {{B_i(v_{-i}) = outcomes + payments independent of i}}

---

### Meaning

- agent chooses:
  {{best outcome from menu}}

---

### Key Insight

- VCG reduces to:
  {{utility maximization over fixed choices}}

---

---

### (e) Strategy-Proofness of VCG

Prove that VCG is strategy-proof.

---

### Step 1

- utility:
  {{u_i = v_i(outcome) − payment}}

---

### Step 2

- payment depends only on:
  {{others’ reports}}

---

### Step 3

- mechanism chooses:
  {{allocation maximizing total value}}

---

### Conclusion

- best strategy:
  {{truthful reporting}}

---

### Key Insight

- agents cannot:
  {{manipulate prices directly}}

---

---

### (f) Individual Rationality

Explain why VCG is individually rational.

---

### Condition

- agent can always get:
  {{zero utility by not participating}}

---

### Conclusion

- VCG guarantees:
  {{non-negative utility}}

---

---

### (g) Budget Balance

Is VCG budget balanced?

---

### Answer

- in general:
  {{NO}}

---

### Reason

- payments may:
  {{not sum to total cost}}

---

### Key Insight

- VCG trades off:
  {{efficiency vs budget balance}}

---

---

## Summary / Key Recall

- allocation:
  {{maximize total value}}

- payment:
  {{externality imposed}}

- strategy-proof:
  {{truth-telling optimal}}

- NOT guaranteed:
  {{budget balance}}