# Formulas

1. **utility**: u_i = v_i - p_i
2. **BNE**
   * expected payment (aka utility): 
     * **all pay**: u_i(b_i) = v_i*Pr(b_i > b_i') - b_i
     * **FPSB**: t(v) = b(v) * P(win)
   * (in a uniform distribution, ie U(0,1), and b_i' = max b_{-i}) -- Pr(b_i > b_i') = ... = b_i > b_i' --> b_i = b_i'
   * optimal bid: s*(v_i) (ie, v_i*(n-1)/n)
   * interim allocation: x_i^*(v_i) = P(win)
   * interim payment: t_i^*(v_i) = bid * P(win)
   * expected revenue: \integ t_i^*(v)db
   * _assignment 4_
3. **VCG payments**: 
   * general form: t_i = externality imposed on others
   * equivalent formula: ![image 1](vcg_payments.png){size=medium}
   * assignment 5
4. **Self-Report Independent Menu**:
   * Menu to agent i as set: ![image 1](self%20report%20indep%20menu%20function.png){size=medium}
   * Where a^{-i}: ![image 1](allocation%20in%20alternatives%20for%20menus.png){size=medium}
   * Formula to show self-report independent functions are agent optimizing: ![image 1](rule%20for%20agent%20optimizing%20proof.png){size=medium}
4. **payment identity (single-parameter domains):** ![image 1](payment%20identity.png){size=medium}
   * assignment 6
5. **GSP / position auctions**
   * Expected utility: ![image 1](gsp_position%20auctions%20expected%20utility.png){size=small}
     * \alph_j = click-through rate
     * assignment 7
   * VCG payment in position auctions: ![image 1](vcg%20payment%20in%20position%20aucts.png){size=medium}
      * Ex pattern: ![image 1](ex%20vcg%20payments%20in%20position%20aucts.png){size=medium}
6. **eBay / second price style price rule:** ![image 1](eBay_second%20price%20style%20price%20rule.png){size=medium}
7. **scoring rules:**
   * log scoring: eBay / second price style price rule: ![image 1](log%20scoring.png){size=medium}
   * quadratic scoring: ![image 1](quadratic%20scoring.png){size=medium}
   * assignment 10
8. **conditional probability (peer prediction):** ![image 1](conditional%20prob%20peer_predict.png){size=medium}
   * assignment 10
9. **market scoring rule (prediction markets):**
   * cost function: ![image 1](marketing%20scoring%20rule%20predict_markets.png){size=medium}
   * price: ![image 1](price-market%20scoring%20rule%20predict%20marks.png){size=medium}
10. **greedy / knapsack ranking:** ![image 1](greed_knapsack%20ranking.png){size=small}
