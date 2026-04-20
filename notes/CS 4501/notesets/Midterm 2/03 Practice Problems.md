* _fed AI the read me file and my other solution sets to make me practice problems in the code style so I wouldn't know what they were_
# MIDTERM 2 EXAM-STYLE PRACTICE

Use these like a real exam: try them first, then click the blanks to reveal.

---

## AUCTIONS

### Problem 1: SPSB vs FPSB

Three bidders have values:
- bidder 1: `10`
- bidder 2: `7`
- bidder 3: `5`

#### (a) SPSB with truthful bidding
Who wins? What price do they pay? What is each bidder's utility?

**Answer framework**
- Winner = {{highest value bidder}}
- Price paid = {{second-highest bid}}
- Utility formula = `u_i = v_i - p_i`

**Answer**
- Winner = {{bidder 1}}
- Price = {{7}}
- Utility of bidder 1 = {{10 - 7 = 3}}
- Utility of bidder 2 = {{0}}
- Utility of bidder 3 = {{0}}

---

#### (b) Bidder 1 bids `8` instead of `10`
Does the outcome change? Is the deviation profitable?

**Key idea**
- In SPSB, changing your bid without changing whether you win usually {{does not help}}

**Answer**
- Outcome changes? {{No}}
- Winner = {{bidder 1}}
- Price = {{7}}
- Utility = {{3}}
- Profitable deviation? {{No}}

---

#### (c) FPSB
Why do bidders shade bids?

**Answer**
- In FPSB, the winner pays {{their own bid}}
- So bidding true value leaves utility {{0 if you win}}
- This creates an incentive to {{bid below value}}

---

## VCG MECHANISM

### Problem 2

There are 2 items: `A, B`

Bidders:
- bidder 1: values `{A,B}` at `100`
- bidder 2: values `{A}` at `70`
- bidder 3: values `{B}` at `50`

#### (a) Efficient allocation

**Answer**
- Value if bidder 1 gets both = {{100}}
- Value if bidder 2 gets A and bidder 3 gets B = {{70 + 50 = 120}}
- Efficient allocation = {{A to bidder 2, B to bidder 3}}

---

#### (b) VCG payment for bidder 2

**Formula**
`payment = value to others without bidder 2 - value to others with bidder 2 present`

**Compute**
- Without bidder 2, optimal allocation for others:
  - bidder 1 gets both = {{100}}
- With bidder 2 present and efficient allocation chosen:
  - others get = {{50}}  
  because bidder 3 gets `B`

**Answer**
- Payment by bidder 2 = {{100 - 50 = 50}}

---

#### (c) Interpretation

**Answer**
- Bidder 2 pays for the {{externality imposed on the other bidders}}
- Economically, bidder 2 prevents bidder 1 from getting {{the full bundle worth 100}}

---

## POSITION AUCTIONS / GSP

### Problem 3

Slots have CTRs:
- slot 1: `0.5`
- slot 2: `0.3`
- slot 3: `0.1`

Bidders:

- bidder 1: value `10`, bid `8`
- bidder 2: value `6`, bid `5`
- bidder 3: value `4`, bid `3`

#### (a) Assign slots

**Rule**
- GSP assigns slots by {{descending bid}}

**Answer**
- Slot 1 = {{bidder 1}}
- Slot 2 = {{bidder 2}}
- Slot 3 = {{bidder 3}}

---

#### (b) Payments

**Rule**
- In GSP, each bidder pays the {{next-highest bid}} per click

**Answer**
- Bidder 1 pays = {{5}}
- Bidder 2 pays = {{3}}
- Bidder 3 pays = {{0 or reserve / no lower bid, depending on convention}}

---

#### (c) Utilities

**Formula**
`utility = alpha_j (v_i - p_i)`

**Answer**
- Bidder 1 utility = {{0.5(10 - 5) = 2.5}}
- Bidder 2 utility = {{0.3(6 - 3) = 0.9}}
- Bidder 3 utility = {{0.1(4 - 0) = 0.4}}  

---

#### (d) Incentive to deviate?

**Think**
If bidder 1 drops below bidder 2, they move from CTR `0.5` to CTR `0.3`.

**Check**
- Current utility = {{2.5}}
- Utility in slot 2 if price is 3 = {{0.3(10 - 3) = 2.1}}

**Conclusion**
- Useful deviation? {{No}}

---

## MATCHING MARKETS

### Problem 4

Students: `s1, s2`  
Schools: `t1, t2`

Preferences:
- `s1: t1 > t2`
- `s2: t2 > t1`
- `t1: s2 > s1`
- `t2: s1 > s2`

#### (a) Run student-proposing DA

**Round 1**
- `s1` proposes to {{t1}}
- `s2` proposes to {{t2}}

Each school has one proposal, so each holds it.

**Answer**
- Matching = {{(s1, t1), (s2, t2)}}

---

#### (b) Is the matching stable?

**Check blocking pairs**
- Does `s1` prefer `t2` to `t1`? {{No}}
- Does `s2` prefer `t1` to `t2`? {{No}}

**Conclusion**
- Stable? {{Yes}}

---

#### (c) Student-optimal or school-optimal?

**Answer**
- This is the result of {{student-proposing DA}}
- Therefore it is {{student-optimal among stable matchings}}

---

## SCORING RULES

### Problem 5

True belief:
`p = 0.7`

Reported probability:
`q = 0.5`

#### (a) Expected score under log scoring

**Formula**
`E = (1-p)ln(1-q) + p ln(q)`

**Substitute**
`E = (0.3)ln(0.5) + (0.7)ln(0.5)`

**Answer**
- `E = {{ln(0.5)}}`
- approximately {{-0.693}}

---

#### (b) Expected score if truthful (`q = 0.7`)

**Substitute**
`E = (0.3)ln(0.3) + (0.7)ln(0.7)`

**Answer**
- Expected score = {{(0.3)ln(0.3) + (0.7)ln(0.7)}}
- approximately {{-0.611}}

---

#### (c) Which is better?

**Answer**
- Better report = {{q = 0.7}}
- Why? Because the log scoring rule is {{strictly proper}}

---

## PEER PREDICTION

### Problem 6

Suppose:
- `P(X_2 = 1 | X_1 = 1) = 0.6`
- `P(X_2 = 1 | X_1 = 0) = 0.3`

#### (a) Compute `P(X_2 = 0 | X_1 = 1)`

**Answer**
- `P(X_2 = 0 | X_1 = 1) = {{1 - 0.6 = 0.4}}`

---

#### (b) Quadratic scoring payment for reporting `1` when other agent reports `1`

**Formula**
`s(q,o) = 2q_o - \sum q^2`

If report implies belief vector `(0.4, 0.6)`, then:

- score = `2(0.6) - ((0.4)^2 + (0.6)^2)`

**Answer**
- score = {{1.2 - (0.16 + 0.36)}}
- score = {{1.2 - 0.52 = 0.68}}

---

## ALGORITHMIC MECHANISM DESIGN

### Problem 7

Knapsack capacity = `5`

Bidders:
- bidder 1: value `10`, size `2`
- bidder 2: value `12`, size `3`
- bidder 3: value `8`, size `2`

#### (a) Compute value/size ratios

**Answer**
- bidder 1 ratio = {{10/2 = 5}}
- bidder 2 ratio = {{12/3 = 4}}
- bidder 3 ratio = {{8/2 = 4}}

---

#### (b) Run greedy allocation

**Order by ratio**
- first = {{bidder 1}}
- then = {{bidder 2 and bidder 3 tied}}

Try filling capacity:
- bidder 1 uses size {{2}}
- remaining capacity = {{3}}
- bidder 2 fits exactly, so chosen next = {{bidder 2}}

**Answer**
- Greedy allocation = {{bidders 1 and 2}}
- Total value = {{22}}

---

#### (c) Is it optimal?

Check other feasible allocations:
- bidders 1 and 3 value = {{18}}
- bidders 2 and 3 value = {{20}}
- bidder 2 alone value = {{12}}

**Conclusion**
- Optimal? {{Yes, here greedy is optimal}}

---

## CONCEPTUAL SHORT ANSWER

### Problem 8(a)
Why is VCG strategy-proof?

**Answer**
- Because each agent pays the {{externality they impose on others}}
- Their report affects the {{allocation}}, but payment is designed so truth-telling is {{optimal}}

---

### Problem 8(b)
Why is GSP not strategy-proof?

**Answer**
- Because a bidder’s payment depends on the {{next bid / position tradeoff}}
- So bidders may gain by {{shading their bids}}

---

### Problem 8(c)
Why can no mechanism be both stable and strategy-proof in two-sided matching?

**Answer**
- Because with two-sided preferences, any stable mechanism can be {{manipulated by one side on some inputs}}
- Stability and full strategy-proofness are {{incompatible}}

---

## MINI FORMULA CHECKLIST

- Utility: `u_i = {{v_i - p_i}}`
- GSP utility: `{{alpha_j (v_i - p_i)}}`
- VCG payment: `{{others' value without i - others' value with i}}`
- Log scoring: `{{(1-p)ln(1-q) + p ln(q)}}`
- Quadratic scoring: `{{2q_o - \sum q^2}}`

---

## SELF-TEST SPEED ROUND

Fill these in before clicking:

1. In SPSB, truthful bidding is {{dominant}}.
2. In FPSB, bidders usually {{shade}}.
3. VCG chooses the allocation maximizing {{total value}}.
4. In GSP, slots are assigned by {{bid rank}}.
5. A matching is unstable if there exists a {{blocking pair}}.
6. Log and quadratic scoring rules are {{strictly proper}}.
7. In prediction markets, prices often represent {{probabilities}}.