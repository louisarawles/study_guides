# ASSIGNMENT 6: ALGORITHMIC MECHANISM DESIGN

## Payment Identity

Formula:
- t_i = {{w_i q_i(x) − ∫ q_i(z) dz}}

---

## Monotonicity

Definition:
- if agent reports higher value → gets {{weakly more allocation}}

---

## Key Insight

- faster agent ⇒ {{gets more work}}

---

## LexOpt Mechanism

Assignment:
- choose {{min-makespan allocation}}

---

## Greedy Auction

### Key failure

Bidder 4 deviation:
- bids {{10}}
- payment becomes {{> value}}

Conclusion:
- deviation is {{NOT useful}} :contentReference[oaicite:2]{index=2}

---

## Core Idea

- Greedy + payments ≠ always truthful

# ASSIGNMENT 6: ALGORITHMIC MECHANISM DESIGN

## Question 1(a): Monotonicity

### Prompt
Explain why monotonicity requires that if an agent reports a faster speed, it receives at least as much work.

---

### Key Definitions

- report:
  {{w_i = -r_i}}

- faster speed ⇒
  {{higher w_i}}

---

### Monotonicity Condition

- allocation q_i(x(w_i)) must be:
  {{non-decreasing in w_i}}

---

### Conclusion

- faster agent ⇒
  {{assigned weakly more work}} :contentReference[oaicite:0]{index=0}

---

## Question 1(a)(ii): LexOpt Assignment

### Prompt
Find assignment and payment for agent 2.

---

### Assignment

- optimal allocation:
  {{({A, C}, {B})}}

---

### Payment Formula

- t_i =
  {{w_i q_i(x) − ∫ q_i(z) dz}}

---

### Key Insight

- payment equals:
  {{area under allocation curve}}

---

### Final Result

- payment:
  {{5}}

- cost:
  {{2}}

- utility:
  {{positive}}

---

## Question 2(a): Greedy Auction Deviation

### Prompt
Is it useful for bidder 4 to bid 10?

---

### Ranking

- sort by:
  {{value / size}}

---

### After deviation

- bidder 4 moves:
  {{higher in ranking}}

---

### Payment

- must pay:
  {{≈ 7.5}}

---

### Value

- true value:
  {{7}}

---

### Conclusion

- deviation:
  {{NOT profitable because payment > value}} :contentReference[oaicite:1]{index=1}

---

## Question 2(b): VCG + Greedy

### Prompt
Run VCG-based mechanism and check deviation.

---

### Key Idea

- greedy allocation:
  {{not efficient}}

---

### Result

- VCG + greedy:
  {{not strategy-proof}}

---

### Insight

- approximation + VCG ⇒
  {{truthfulness breaks}}