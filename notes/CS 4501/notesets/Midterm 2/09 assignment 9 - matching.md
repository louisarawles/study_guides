# ASSIGNMENT 9: MATCHING

## DA Comparison

Teacher-proposing:
- better for {{teachers}}
- worse for {{students}} :contentReference[oaicite:5]{index=5}

---

## Impossibility Result

- no mechanism is both:
  - {{stable}}
  - {{strategy-proof}}

---

## Proof Idea

- only two stable matchings
- one side can {{manipulate}}

---

## Complexity

- DA runs in {{O(mn)}}

Reason:
- each student proposes at most {{n times}}

---

## Lattice Property

- teacher-optimal = {{worst for students}}
- student-optimal = {{best for students}}

---

## Key Insight

- if both matchings are same → {{unique stable matching}}

# ASSIGNMENT 9: MATCHING

## Question 1(a): DA Comparison

### Prompt
Compare teacher vs student proposing DA.

---

### Result

- teachers:
  {{better off under teacher-proposing}}

- students:
  {{worse off}}

---

## Question 1(b): Impossibility

### Prompt
Prove no mechanism is both stable and strategy-proof.

---

### Key Idea

- only two stable matchings:
  {{μ and μ'}}

---

### Case 1

- choose μ → {{one agent can deviate}}

---

### Case 2

- choose μ' → {{another agent can deviate}}

---

### Conclusion

- impossible to have:
  {{both stability and strategy-proofness}} :contentReference[oaicite:3]{index=3}

---

## Question 1(c): Complexity

### Result

- DA runs in:
  {{O(mn)}}

---

### Reason

- each student proposes at most:
  {{n times}}