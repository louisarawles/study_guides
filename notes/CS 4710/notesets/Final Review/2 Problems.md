# CSP

### Sudoku

- variables: {{ each empty square is a variable }} and each variable represents the digit in one cell, ie {{ X_{1,1}, X_{1,2}, ..., X_{9,9} }}
- domains: each variable has possible values {{ {1, 2, 3, 4, 5, 6, 7, 8, 9} }}
  - if a cell is filled in, its domain is {{ just that number }}
- constraints:
  1. row constraint: every number 1-9 appears once in each row
  2. column constraint: every number 1-9 appears once in each column
  3. box constraint: every number 1-9 appears once in each 3x3 box

Solution:

1. initialize domains: for every empty cell, remove values already appearing in its row column and box
2. apply constraint propagation: look for domains with only one value
3. use MRV (minimum remaining values): pick the unassigned variable with the smallest domain
4. try a value and backtrack if needed