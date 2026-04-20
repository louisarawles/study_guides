# ASSIGNMENT 8: COMBINATORIAL AUCTIONS

## OR Language

- expresses {{superadditive valuations only}} :contentReference[oaicite:4]{index=4}

---

## XOR-of-OR → OR*

Key idea:
- use {{dummy items}} to prevent overlap

---

## Dummy Item Count

- roughly {{O(k²ℓ²)}} dummy items

---

## Expressiveness

- XOR-of-OR is {{fully expressive}}

---

## Winner Determination

- NP-hard in general

---

## Tractable Cases

- small bundles
- structured bids

---

## Key Insight

- tradeoff: {{expressiveness vs tractability}}

# ASSIGNMENT 8: COMBINATORIAL AUCTIONS

## Question 1(a): OR Language

### Prompt
Show OR is expressive for superadditive valuations.

---

### Construction

- create atom for:
  {{every subset S}}

- value:
  {{equal to valuation of S}}

---

### Key Property

- OR picks:
  {{max sum of disjoint atoms}}

---

### Conclusion

- OR expresses:
  {{all superadditive valuations}} :contentReference[oaicite:2]{index=2}

---

### Why NOT others?

- for disjoint S₁, S₂:
  {{value(S₁ ∪ S₂) ≥ value(S₁) + value(S₂)}}

---

### Conclusion

- OR cannot express:
  {{non-superadditive valuations}}

---

## Question 1(b): XOR-of-OR → OR*

### Prompt
Convert XOR-of-OR to OR*.

---

### Key Trick

- introduce:
  {{dummy items}}

---

### Purpose

- prevents:
  {{atoms from different clauses being chosen together}}

---

### Conclusion

- dummy items enforce:
  {{mutual exclusivity}}

---

## Question 1(c): Dummy Item Count

### Result

- number of dummy items:
  {{O(k²ℓ²)}}

---

## Question 1(d): Expressiveness

### Answer

- XOR-of-OR is:
  {{fully expressive}}

---

## Question 2(a): Cyclic Structure

### Prompt
Show bids cannot satisfy cyclic structure.

---

### Key Idea

- ordering must be:
  {{consistent across bidders}}

---

### Conflict

- dummy items require:
  {{incompatible placements}}

---

### Conclusion

- cyclic structure:
  {{cannot hold}}

---

## Question 2(b): Tractability (XS)

### Key Idea

- convert to:
  {{OR* with small bundles}}

---

### Conclusion

- WDP is:
  {{tractable}}

---

## Question 2(c): Tractability (OXS)

### Key Idea

- each atom:
  {{≤ 2 items}}

---

### Property

- satisfies:
  {{pair structure}}

---

### Conclusion

- WDP:
  {{efficiently solvable}}

---

## Core Insight

- combinatorial auctions trade off:
  {{expressiveness vs computational tractability}}