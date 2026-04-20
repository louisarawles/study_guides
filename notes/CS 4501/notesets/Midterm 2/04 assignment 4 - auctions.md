# ASSIGNMENT 4: AUCTIONS

---

## Question 2. Collusion and Shill Bids

---

### (a)

Consider an SPSB auction with bidders of value 4, 8, 10 colluding, and another bidder with bid 6.

How can bidders 1, 2, 3 collude?

---

### Strategy

- bidder 3 bids:
  {{10}}
- others bid:
  {{0}}

---

### Outcome

- winner pays:
  {{6 instead of 8}}

---

### Side Payments

- bidder 3 shares profit:
  {{with bidders 1 and 2}}

---

### Why no deviation?

- bidders 1 and 2 would need to bid:
  {{above 10 to win}}

---

### Key Insight

- collusion lowers price by:
  {{removing competition}}

---

### (b)

Now consider the FPSB auction. Is it more or less robust to collusion?

---

### Candidate Strategy

- bidder 3 bids:
  {{6 + ε}}

---

### Deviation

- bidder 2 can bid:
  {{6 + 2ε}}

---

### Result

- bidder 2 profit:
  {{≈ 2 − 2ε}}

---

### Conclusion

- FPSB is:
  {{more robust to collusion}}

---

### Key Insight

- collusion is unstable because:
  {{members can profitably deviate}}

---

### (c)

Explain why shill bidding is a problem in SPSB.

---

### Mechanism

- auctioneer inserts:
  {{fake bid just below highest bid}}

---

### Effect

- increases price to:
  {{near highest bid}}

---

### Result

- SPSB behaves like:
  {{first-price auction}}

---

### Key Insight

- equilibrium becomes:
  {{FPSB-style bid shading}}

---

## Question 3. All-Pay Auction

---

### (a)

Verify that:

s*(v_i) = 1/2 v_i²

is a Bayes-Nash equilibrium.

---

### Step 1: Winning Probability

Assume bidder 2 follows the strategy, s.t. b_2 = v_2 ^2 / 2
* Hint: {{ standard in BNE proofs, assume others play equilibrium --> show best response is same strategy}}

- P(b₁ > b₂) =
  {{P(b₁ > v₂²/2)}}

- rewrite:
  {{P(2b₁ > v₂²)}}

- result:
  {{(2b₁)^(1/2)}}

---

Hint: {{ Since v_2 ~ U(0,1): P(v_2 < x) = x, so --> P(win) = 2 \sqrt{b_1} }}

### Step 2: Expected Utility

Hint: {{ in an all pay auction, u = v * P(win) - bid }}

- u₁(b₁) =
  {{v₁ (2b₁)^(1/2) − b₁}}

---

### Step 3: First-Order Condition

- derivative = 0 ⇒
  {{b₁ = v₁² / 2}}

---

### Step 4: Second-Order Condition

- check:
  {{second derivative < 0}}

---

### Conclusion

- equilibrium strategy:
  {{s*(v_i) = v_i² / 2}}

---

### Key Insight

- bids are lower because:
  {{you pay even if you lose}}

---

### (b)

Compare with FPSB equilibrium.

---

### FPSB Strategy

- s*(v) =
  {{v / 2}}

---

### Comparison

- all-pay bids are:
  {{always lower than FPSB bids}}

---

### Key Insight

- all-pay discourages bidding because:
  {{losers still pay}}