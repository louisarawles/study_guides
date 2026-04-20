**MIDTERM 2 STUDY GUIDE**

Chapter 6: Auctions
* don't need to know 6.6, 6.7

_Know REALLY Well_
Core Ideas
* Quasi-linear utility:
  * u_i = v_i - p_i
* allocative efficiency: item --> highest value bidder
* Dominant strategy equilibrium (DSE)
* Bayes-Nash Equilibrium (BNE)

Auction Types
* SPSB
  * truthful bidding is dominant
  * allocative efficiency
  * agents place higher bids in SPSB than in FPSB
* FPSB
  * not truthful --> bid shading
  * BNE strategy:
    * b(v)<v
* Revenue Equivalence:
  * dif auctions --> same expected revenue
    * IF
      * same allocation rule
      * same bidder distribution

Framework:
1. identify auction type
2. write utility
3. ask:
   4. does bid affect price
5. conclude
   6. truthful v. shading

Chapter 7: Mechanism Design
VCG
* allocation: 
  * maximize total value
* payment:
  * t_i = externality imposed

Framework VCG
1. remove agent i
2. compute optimal without them
3. compute optimal with them
4. payment = difference

Single-Parameter Domains
* allocation must be _monotone_
* payment = _critical value_

Chapter 8: Alg. Mechanism Design
Core Ideas:
* Approx algs
* Greedy algs
* VCG + approximation --> NOT truthful
  * ie: Knapsack auction
    * greedy by value/weight
    * not optimal --> approximation

Chapter 10: Internet Advertising

Position Auction Model
* Slots have CTR: \alph_j
* Value:
  * \alph_j(v_i - p_i)

VCG Auction
* truthful
* efficient

GSP Auction
* not truthful
* uses rank by bid
* NE != truthful

MUST KNOW:
* Balanced bidding
* Envy-free equilibrium

Framework GSP
1. sort bids
2. assign slots
3. price = next bid
4. check:
   5. utility deviations
   6. envy

Chapter 11: Combinatorial Auctions (CAs)
Core Ideas:
* superadditive v. subadditive
* bidding languages:
  * OR
  * XOR
  * OR*

WDP
* NP-hard
* use: integer programming 
* key: tradeoff: expressiveness v. tractability

Chapter 12: Matching Markets
Core Ideas:
* stable matching 
* blocking pairs

Algorithm: deferred acceptance (DA)
1. propose
2. accept best
3. reject rest

MUST KNOW:
* student-optimal v teacher-optimal
* stability
* strategyproofness (only one side)
  * intuition: no mechanism is stable AND strategyproof

Framework (proof)
1. construct preferences
2. show: only 2 stable matchings

Chapter 15: Information Elicitation

Scoring Rules
* Log scoring:
  * E=pln(q)+(1−p)ln(1−q)
  * asymmetric
  * harsh penalties
* Quadratic scoring:
  * s(q,o)=2q_o−∑q2
  * symmetric
  * smoother
* Strictly proper --> truth maximized expected payoff

Peer Prediction
* idea: no ground truth --> use other agents
* key: payments depend on: P(other report | your report)

Chapter 16: Prediction Markets

Types:
* CDA (continuous double auction)
* call market
* automated market maker (AMM)

Market Scoring Rule:
* Cost function:
  * C(x)=ln(e^{x_0} + e^{x_1})
* Key: prices = probabilities
