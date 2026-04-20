# ASSIGNMENT 4: AUCTIONS

## eBay Auction Design

### Why is eBay NOT strategy-proof?

Key idea:
- In eBay, winner pays {{second-highest bid + ε}}

This differs from SPSB because:
- True SPSB pays {{exact second-highest bid}}

---

### Incentive to shade bids

Example:
- v₁ = 10, v₂ = 7, ε = 0.50

Truthful outcome:
- price = {{7.50}}
- utility = {{10 - 7.5 = 2.5}}

Shaded bid:
- bid = {{7.49}}
- price = {{7.49}}
- utility = {{10 - 7.49 = 2.51}}

Conclusion:
- bidder benefits from {{shading bid slightly below (b₂ + ε)}} :contentReference[oaicite:0]{index=0}

---

## eBay Price Formula

- price = {{min(b₁, b₂ + ε)}}
- ask price = {{p + ε}}

---

## Key Insight

- eBay is NOT strategy-proof because:
  {{winner pays strictly more than second-highest bid}}

---

## Collusion in SPSB

### Strategy

Coalition:
- values: 4, 8, 10
- outside bidder: 6

Collusive strategy:
- strongest bidder bids {{10}}
- others bid {{0}}

---

### Outcome

- winner pays {{6}}
- coalition redistributes surplus

---

### Why no deviation?

- smaller bidders cannot win anyway → {{no incentive to deviate}}

# ASSIGNMENT 4: AUCTIONS

## Question 1(a): eBay Design

### Prompt
Explain how a bidder can benefit from bidding less than their true value in eBay.

---

### Key Recall

- eBay price rule:
  {{p_t = min(b_(1), b_(2) + ε)}}

- Difference from SPSB:
  {{winner pays second price + ε, not exactly second price}}

---

### Example

- v₁ = 10, v₂ = 7, ε = 0.50

Truthful:
- price = {{7.50}}
- utility = {{10 - 7.50 = 2.5}}

Shaded bid:
- bid = {{7.49}}
- price = {{7.49}}
- utility = {{10 - 7.49 = 2.51}}

---

### Conclusion

- Incentive:
  {{bidder can gain by bidding slightly below b₂ + ε}}

- Why:
  {{price depends on own bid, breaking strategy-proofness}} :contentReference[oaicite:0]{index=0}

---

## Question 1(b): Auction State Tracking

### Prompt
Track price, ask price, and winner after each bid.

---

### Core Formulas

- price:
  {{p_t = min(b_(1), b_(2) + ε)}}

- ask price:
  {{p_ask = p_t + ε}}

---

### Final Outcome

- Winner:
  {{bidder 4}}

- Final price:
  {{28.99}}

---

## Question 2: Collusion

### Prompt
How can bidders collude in SPSB?

---

### Strategy

- strongest bidder bids:
  {{true value}}

- others bid:
  {{0}}

---

### Outcome

- winner pays:
  {{outside bidder’s value}}

---

### Key Insight

- collusion lowers price because:
  {{competition is removed}}