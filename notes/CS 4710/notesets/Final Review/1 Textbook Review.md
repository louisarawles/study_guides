# AI Final Exam – 90-Minute Textbook Review Template

## Goal
Quickly extract **high-yield concepts, definitions, and common traps** from the textbook before active practice.

---

## ⏱️ Time Plan (Stick to This)

- Definitions & Foundations → 10 min  
- Search (Uninformed + Informed + Heuristics) → 20 min  
- Game Playing + Local Search → 10 min  
- CSP → 10 min  
- Logic → 10 min  
- Probability + Bayes + Bayes Nets → 15 min  
- ML + Deep Learning + LLMs + Trustworthy AI → 15 min  

---

# 1. Definitions & Foundations (10 min)

### What is AI?
- AI can be defined as: 
  - Acting humanly, thinking humanly
  - standard model: prioritize rational action
- Another definition:  
  - Acting rationally, thinking rationally

### Turing Test
- Definition:  thought experiment determining whether computers act humanly
- Key idea:  a computer passes the test if a human interrogator, who cannot see them (which is improved in total turing test) cannot tell whether the written responses come from a computer or a person

### Winograd Schema
- What it tests:  an AI's ability to understand nuanced language and reasoning; ie applying common sense reasoning to conclude meaning 
- Why it is hard:  have to have a contextual understanding and human like reasoning

### Common T/F Traps
- “AI requires human-like thinking” → F 
  - there are two different definitions of AI, and the standard model prioritizes rational action over thinking humanly
- “Passing the Turing Test means true intelligence” →  F
  - passing the turing test only means that an AI or computer can act humanly in written responses to questions, but it does not require any rationality 

---

# 2. Uninformed Search (10 min)
Edition 3 & 4: Sections 3.0-3.4

### BFS
- Data structure used:  
- Complete?  
- Optimal?  
- Memory usage:  

### DFS
- Data structure used:  
- Complete?  
- Optimal?  
- Memory usage:  

### Uniform Cost Search
- Expands based on:  
- Optimal when:  every branch is same cost, finds shallowest sollution
- every branch same cost

### Common Traps
- BFS uses a stack →  
- DFS always finds a solution →  
- UCS = BFS when:  

---

# 3. Informed Search + Heuristics (10 min)

Informed Search: Edition 4: Section 3.5.0-3.5.3, excluding consistency; Section 3.5.5, excluding RBFS, MA* and SMA*

Heuristics: Edition 4: Section 3.6.0-3.6.3, excluding effective depth

### Greed best-first
- Formula: f(n) =  h(n)
- Optimal if: since on each iteration it's trying to get as close to the end goal as it can, it may choose a node at one iteration that results in a larger total cost than if it had chosen a different node at that iteration, but it only expands the nodes on the solution path, so it cannot make this distinction 
- Idea:
  - Expand first node with lowest h(n)
- Complete?: only in finite state spaces
- Complexity:
  - Time: O(|V|)
  - Space: O(|V|)
  - *good heuristic can create complexity O(bm) 

### A*
- Formula: f(n) =  g(n) + h(n) = estimated cost of the best path that continues from n to a goal
  - g(n) = path cost from initial state to node n
  - h(n) = estimated cost of the shortest path from n to a goal state
- Optimal if: it has an admissible heuristic, ie a consistent heuristic; 
  - possibly with an inadmissible heuristic -- if h(n) overestimates but never by enough to choose the second best cost, then it will still return the optimal cost
- Idea: choosing the lower bound solution at each step; choose the lowest-codst node on the frontier and select that for expansion because there might be a solution whose cost is as low as f(n)
- Complete?: yes
- Complexity: number of nodes expanded can be exponential in length
  - Time: 
  - Space:

### Memory in Search
- Frontier states: nodes to expand
- Reached states: nodes visited
- Separation property: in problems where states can be removed from reached when they can be proved to no longer be needed, so only check frontier for redundant paths rather than having a reached table
- Reference counts: maintain count for each state and remove it from reached when no more ways to reach the state exist

### Memory-Conserving Search Algorithms

## Beam Search
- Saves memory by: limiting the size of the frontier
- k-approach: keep only k nodes with the best f-scores
- \delta-approach: keep every node whos f-score is within \delta of the best f-score
- Complete?: no
- Optimal?: suboptimal -- for many problems can find good near-optimal solutions

## IDA*
- Saves memory by: limits number of nodes stored because it only stores the current path by using a cutoff threshold and exploring all possible paths within it, then increasing the threshold to the smallest possible amount then repeat
- Approach: the cutoff value is the smallest f-cost of any node that exceed the cutoff of the previous iteration
- Complete?: yes IF finite branching factor and step costs are positive
- Optimal?: with an admissible heuristic; if the optimal solution has cost C*, then there can be no more than C* iterations, but for a problem where every node has a different f-cost, each new contour might contain only one new node, and the number of iterations could equal the number of states

### Heuristic (h(n))
- Definition:  estimated cost of the cheapest path from the state at node n to a goal state
  - Idea: a heuristic is an accurate path length for a simplified version of the search space
- Admissible means: optimistic; an admissible heuristic is one that never overestimates the cost to reach a goal
- Consistency means: a heuristic is consistent if, for every node n and every successor n' of n generated by an action a, we have h(n) <= c(n,a,n') + h(n')
  - It's a form of: triangle inequality
  - because: that stipulates that a side of a triangle cannot be longer than the sum of the other two sides
  - Significance: never have to re-add a state to the frontier because the added states will be on the optimal path
- Dominance: using informed search strategy, ie A*, a heuristic h1 dominates h2 if h1 will never expand more nodes than h2

### Heuristic Functions
- Manhattan distance: h(n) = sum of horizontal and vertical distances

### Effect of Heuristic Accuracy on Performance - Effective branching factor b* 
  - If b*=1 → well-designed heuristic, reasonable computational cost

### Generating Heuristics

## Generating Heuristics - Relaxed Problems
- Definition: a problem with fewer restrictions on the action space than the original problem
- A relaxed problem's state-space graph is {{ a supergraph }} of the original state space because {{ the removal of restrictions creates added edges in the graph }}
- Given a search problem and using the relaxed problem to generate a heuristic, the process is: solve the relaxed problem, use the cost of the optimal solution as a heuristic for the original problem
- Idea: because the relaxed problem is a supergraph of the original graph, its state-space graph includes all edges in the original graph and then some, meaning its optimal solution will be the lowest possible cost solution for the subgraph

## Generating Heuristics - Subproblems
- The cost of the {{ subproblem }} is a {{ lower bound }} on the cost of the complete problem
  - ie, the optimal cost of the complete problem will be at least as good as that of the subproblem
- Pattern databases - idea: use dynamic programming to store exact solution costs for every possible subproblem instance
  - Database construction: {{ bottom-up }} search
  - Useful when: expect to be asked to solve many problems
  - Combine heuristics by: taking the maximum value
  - {{ more }} (more/less) accurate than manhattan distance, and generates {{ fewer }} (more/fewer) nodes 
- Disjoint pattern databases - idea: make other tiles entirely disappear to remove overlapping moves, solve for the number of moves involving the nodes in the individual subproblems (rather than each of their total costs), then sum the two costs --> sum = lower bound on cost of the complete problem

### Key Cases
- If h(n) = 0 → A* becomes: g(n), or the cost of the path from the initial state to node n

### Common Traps
- Admissible = exact →  F; it = optimistic; admissible only means that the cost of the optimal solution will not be less than the outputted solution
- Better heuristic expands {{ fewer }} nodes (more/fewer)

---

# 4. Constraint Satisfaction (10 min)

Edition 4: Sections 5.0-5.3.2, 5.3.4, and 5.5

### CSP
- 3 Components:
  1. set of variables
  2. set of each variable's domain of allowable values
  3. set of constraints specifying allowable combinations of values
- Key property: commutativity, ie the order of application of a given set of actions doesn't matter
- Constraint consists of: a pair (scope,rel) where scope is tuple of variables that participate in the constraint and rel is a relation that defines the values that those variables can take on
- Consistent v. complete assignment: consistent = an assignment upholds all constraints; complete = every variable has an assignment, solution = consistent, complete assignment
- A partial solution is a partial assignment where some variables are have non-consistent assignments: {{ F }} -> {{because partial assigment = some variables unassigned, partial solution = partial assignment is consistent }}
- Hardness: {{ NP-complete }}
- Precedence constraints: one task must occur before another, meaning T2 starts after the time to complete all tasks preceding T1 and the duration to complete T1 (d1), giving T1 + d1 <= T2
- Disjunctive constraint: either one comes first or the other does (ie, XOR)

## discrete domains
- Linear constraints: each variable appears only in linear form, ie T1 + d1 <= T2
  - For infinite domains, use implicit constraints like T1 + d1 <= T2, aka linear constraints
- Use solution algorithm for nonlinear constraints is decidable: {{ F }} -> {{ the problem is undecidable on integer variables }}

## continuous domains
- Linear programming: constraints are linear equalities or inequalities
  - Complexity: polynomial in the number of variables

## Constraints
- Unary: restricts value of a single variable (ie, gus won't tolerate the snack scooby snacks; also the initial specification of the domain of the variable)
- Binary: relates 2 variables, ie Francie won't tolerate the same snack as Gus
- Binary CSP has {{ only unary and binary }} constraints
- Global: arbitrary number of variables involved; can design more efficient inference algorithms than if using primitive constraints

### Local Consistency
- Given graph G, 
  - nodes = variables
  - edges = binary constraint
  - idea: inconsistent edges eliminated

## Node Consistency
- Definition: 
  - (single variable) - all values in the variables domain satisfy the variable's unary constraints
  - (graph) - all variables in graph are node consistent

## Arc Consistency
- Definition: 
  - (single variable) - every value in a variable's domain satisfies the variable's binary constraints, ie satisfy binary constraint on arc (Xi, Xj)
  - (graph) - all variables arc-consistent with every other variable
- Tightens down domains (unary constraints) using the arcs (binary constraints)

## Path Consistency
- Tightens {{ binary }} constraints by using {{ implicit }} constraints that are inferred by looking at triples of variables

## K-consistency
- Definition: A CSP is k-consistent if, for any set of k-1 variables and for any consistent assignment to those variables, a consistent value can always be assigned to any kth variable
- 1-consistency :: {{ node-consistency }}
- 2-consistency :: {{ arc consistency }}
- 3-consistency :: {{ path consistency }} on binary constraint graphs
- Strongly k-consistent: {{ k-consistent for all k-n values of k-n goes all the way down to 1 consistent
- Complexity: 
  - Time: O(n)
  - Space: O(n^k)

## bounds consistency
- Definition: given a CSP, if for every variable X, and for both the lower-bound and upper-bound values of X, there exists some value of Y that satisfies the constraint b/w X and Y for every variable Y

### Backtracking
- Type of search:  DFS --> explores one assignment as far as possible, if it hits a conflict, it backtracks and tries another option

### Heuristics
- MRV 
  - chooses: most constrained value, the variable with the fewest "legal" values; ie the variable that is most likely to cause a failure soon, thereby pruning the search tree
  - use-case: choosing non-initial variables
- Degree 
  - chooses: variable that is involved in the largest number of constraints on other unassigned variables
  - use-case: initial choice 
- Least-constraining-value
  - chooses: given a variable choice, the value that rules out the fewest choices for the neighboring variables in the constraint graph
- Variable selection is fail- {{ first }} and value selection is fail- {{ last }}
- Forward checking does: infers new domain reductions on the neighboring variables
  - process: whenever a variable X is assigned, it establishes arc consistency for it: for each unassigned variable Y that is connected to X by a constraint, delete from Y's domain any value that is inconsistent with the value chosen for X

### Constraint Learning
- Idea: finding a min set of variables (called "no-good") from the conflict set that causes the problem, then recording the no-good

### Structure of Problems to find solutions quickly
- Independent subproblems 
  - Find connected components to ascertain independence
- Directional arc consistency = ie, tree-structured CSP

### Tree-CSP-Solver
- Solving a tree-structured CSP:
  1. pick any variable to be the root
  2. perform topological sort, ie choose an ordering of the variables such that each variable appears after its parent in the tree
  3. make arcs consistent (bottom-up); ie applying arc consistency once per edge
  4. assign values (top-down) (guaranteed to work because of step 2)
  - Complexity:
    - Time: O(nd^2)
- implication: reduce other general constraint graphs to trees somehow to use the efficient algorithm

### Cutset conditioning
- Idea: assign values to some variables so that remaining variables form a tree; might involve retrying each possible value for the fixed assignments
- Cycle cutset: {{ the subset S of the CSP's variables s.t. the constraint graph becomes a tree after removal of S }}
- Complexity: (where cycle cutset has size c)
  - Time: O(d^c * (n-c)d^2)

### Tree decomposition
- Definition: a transformation of the original graph into a tree where each node in the tree consists of a set of variables
- Requirements:
  1. every variable in OG prob appears in at least one of the tree nodes
  2. if 2 variables are connected by a constraint in the og prob, they must appear together (along with the constraint) in at least one of the tree nodes
  3. if a variable appears in 2 nodes in the tree, it must appear in every node along the path connecting those nodes
- Efficient if: {{ d remains small }}
- Complexity:
  - Time: O(nd^{w+1})
  - Space: exponential in w

### Value Symmetry
- Idea: {{ for d values, d! solutions formed for every consistent solution }}
- Symmetry-breaking constraint: 
  - goal: {{break symmetry in assignments to reduce search space by a factor of d! }}

### Common Traps
- Forward checking guarantees solution →  {{ F; it doesn't detect all inconsistencies because it doesn't look ahead far enough }}
- MRV picks largest domain → {{ F; picks smallest domain }}

### Concept Practice
- Can CSPs be solve in linear time? --> only when a CSP is directional arc-consistent; any tree-structured CSP can be solved in time linear in the number of variables 
- Can CSPs be solved in polynomial time? --> if they have a constraint graph of bounded tree width
- When should you use cutset over tree decomposition and vis-versa? --> {{ use cutset conditioning when needing to execute in linear memory, and tree decomposition in favor of time }}

---

# 5. Local Search (5 min)

Edition 3 & 4: Section 4.1


### Simulated Annealing
- Key idea:  

### Common Traps
- Always finds global optimum →  

---

# 6. Game Playing (10 min) 

Edition 3 & 4: Sections 6.0-6.2.2, 6.3.0-6.3.2, 6.5

### Minimax
- Assumes opponent is:  
- Goal:  

### Alpha-Beta Pruning
- Purpose:  
- Changes final result?  

### Common Traps
- Minimax assumes randomness →  
- Alpha-beta changes outcome →  

---

# 7. Propositional Logic (10 min)

Edition 3 & 4: Sections 7.0-7.4.3

### Key Concepts
- Entailment means:  
- Model means:  

### Logical Forms
- Implication:  
- Contrapositive:  

### Common Traps
- If A → B then B → A →  

---

# 8. Probability & Bayes (10 min)

Quantifying uncertainty: Edition 4: Section 12.1-12.6 (except “probability density functions”)

### Probability Theory
- Definition: deal with degrees of belief by summarizing uncertainty
- Ontological commitments: world is composed of facts that do or do not hold in any particular case
  - {{ same for logic and probability theory }}
- Epistemological commitments: a logical agent believes each sentence to be true or false or has no opinion, whereas a probabilistic agent may have a numerical degree of belief b/w 0 (certainly false) and 1 (certainly true)
- Possible world: an assignment of values to all of the random variables under consideration
- Sample space is: the set of all possible worlds, where possible worlds are mutually exclusive and exhaustive (ie, 2 cannot both be the case and one must be the case)
- Model: associate probability with each possible world
- Unconditional / priors refer to: degrees of belief in propositions in the absence of any other information
- Conditional / posterior: ie given some other evidence

### Utility theory
- Definition: every state has a degree of usefulness, or utility, to an agent that the agent will prefer states with higher utility
- Idea: represents preferences and allows agent to reason qualitatively with them

### Decision theory
- Definition: an agent is rational iff it chooses the action that yields the highest expected utility over all possible outcomes of the action, ie principle of max expected utility
- = probability theory + utility theory

### Conditional Probability
- Definition: degree of belief conditioned on all observed evidence
- Mathematically: P(a|b) = P(a ^ b) / P(b)
  - By the product rule we get: P(a ^ b) = P(a|b)P(b)

### Representations
- Factored: possible world represented by a set of variable/value pairs
- Structured: 

### Bernoulli
- Definition: 

### Distribution
- Probability: assignment of a probability for each possible value of the random variable
- Categorical: same as probability distrib but with a finite, discrete range
- Joint probability: distributions on multiple variables for all combinations of the values
- Full joint probability: probability model is completely determined by the joint distribution for all of the random variables

### Axioms
- Idea: imply certain relationships among the degrees of belief that can be accorded to logically related propositions
- Kolmogorov's axioms: inclusion-exclusion principle, 0 <= P(w) <= 1 for every w and \sum_{w \in \omega} P(w) = 1 where \omega refers to the sample space and w refers to elements of the space

### Inclusion-exclusion principle
- Rule: the cases where a holds, together with the cases where b holds, certainly cover all the cases where a or b holds; but summing the two sets of cases counts their intersection twice so subtract P(a ^ b)
- Formula for: probability of a disjunction
- P(a \/ b) = P(a)+P(b) - P(a^b) :

### Independence
- Definition:  

### Bayes Rule
- Idea: update your belief about A (hypothesis) after seeing B (evidence)
- P(A|B) = P(B|A)P(A) / P(B)
- P(A) = prior (belief before evidence)
- P(B|A) = likelihood (how likely evidence if A is true)
- P(B) = normalizer (total probability of the evidence)
- P(A|B) = posterior (updated belief)

::contentReference[oaicite:0]{index=0}


- Meaning:  

### Common Traps
- P(A|B) = P(B|A) →  
- Independent = mutually exclusive →  

---

# 9. Bayesian Networks (5 min)

Bayesian networks: Edition 4: Section 13.0-13.2.1 (except “d-separation”), 13.3–13.3.2


### Structure
- Nodes represent:  
- Edges represent:  

### Key Idea
- Each node depends on:  

### Common Traps
- Fully connected network required →  

---

# 10. Machine Learning Basics (10 min)

Edition 4: Section 19.0-19.3.4, 19.6

### Terms
- little prior knowledge: start from scratch and learn from the data
- transfer learning: knowledge from one domain is transferred to a new domain
- induction: going from a specific set of observations to a general rule
- deduction: guaranteed to be correct if the premises are correct
- factored representation: vector of attribute values
- classification: learning problem with an output that is one of a finite set of values
- regression: the output is a number
- supervised learning: agent observes input-output pairs and learns a function that maps from input to output
  - outputs are labels
- unsupervised learning: agent learns patterns in the input without any explicit feedback
  - ie, clustering: detecting potential useful clusters of input examples
- RL: agent learns from a series of reinforcements: rewards and punishments


### Supervised Learning
- Draw hypothesis h from hypothesis space OR can say h is a model of the data drawn from a model class OR a function drawn from a function class
- Ground truth: output yi, the true answer we are asking our model to predict
- Process: choose hypothesis space --> choose hypothesis within hypothesis space
- Exploratory data analysis: examining data w statistical tests and visualizations to get a feel for the data and some insight into what hypothesis space might be appropriate
- Consistent hypothesis: h st each xi in the training set has h(xi)=yi
- Best-fit function: each h(xi) is close to yi
- True measure of a hypothesis is: how well it handles input it has not yet seen
- h {{ generalizes well }} if it accurately predicts the outputs of the {{ test set, (xi,yi) }}

## Choosing a Hypothesis h* (SL)
- Choice: most probable h* given the data:
  - h* = argmax_{h \in H} P(h|data)
  - = argmax_{h \in H} P(data\h)P(h)
- Tradeoff b/w: expressiveness of a hypothesis space and the computational complexity of finding a good hypothesis within that space

### Overfitting
- Definition: function pays too much attention to the particular data set it is trained on, causing it to perform poorly on unseen data
- Increased likelihood when: number of attributes grows
- Decreased likelihood when: increase number of training examples
- In univariate versus multi LR --> {{ worry about overfitting only in multi }}

### Underfitting
- Definition: a hypothesis fails to find a pattern in the data

### Bias
- Definition: tendency of a predictive hypothesis to deviate from the expected value when averaged over different training sets
  - often caused by restrictions imposed by the hypothesis space

### Variance
- Definition: the amount of change in the hypothesis due to fluctuation in the training data

### Bias vs Variance
- Low-Bias: hypothesis that fits the training data well
- Low-Variance: simpler hypotheses that may generalize better
- Idea (Ockham's Razor): among competing explanations, prefer the simplest one that fits the data

### Decision Trees
- Definition: representation of a function that maps a vector of attribute values to a single output value
- Expressive if: the domain is discrete/finite, but the model class itself is expressive
- Applying Ockham's Razor, fully expressive can --> overfit

### Decision Tree Expressiveness  
- Any function in propositional logic can be expressed as a decision tree

### Learn-Decision-Tree Algorithm
- Strategy: greedy D&C; always test the most important attribute first, then recursively solve the smaller subproblems that are defined by the possible results of the test
- Idea: looks at the examples, not at the correct function, to make a hypothesis consistent with all the examples and simpler than the original tree
- Commonly makes mistakes with unseen examples, but can correct them by providing more examples

### Decision Tree Pruning
- Idea: eliminate nodes that are not clearly relevant through noise detection
- Use: combats overfitting
- Indicator of irrelevance: it splits examples into subsets st each subset has roughly the same proportion of positive examples as the whole set, p/(p+n), and so the information gain will be close to 0
  - ie, use a significance test to determine information gain threshold
- Prune using X^2 statistics (X^2 pruning)

### Significance Test
- Process: assume no underlying pattern --> analyze actual data --> calculate extent to which data deviate from a perfect absence of pattern --> if degree of deviation is statistically unlikely (ie, 5% probability or less), conclude good evidence for a pattern

### Evaluating Learning Algorithms
- Use: Learning curve (aka, happy graph)
- Process: split examples randomly into training and test sets --> learn hypothesis h with the training set --> measure h's accuracy with the test set

### Entropy
- Definition: measure of uncertainty of a random variable
- Importance: fundamental quantity in information theory
- Basis for: the notion of information gain, which is used to measure importance
- Entropy: H(V) =\sum_k P(vk) log2 1/P(vk) = -\sum_k P(vk) log2 P(vk) :
- Entropy of a Boolean random variable that is true w probability q: B(q)=-(qlog2 q+(1-q) log2(1-q))
- Entropy of the output variable on the whole set: H(Output) = B(p/(p+n))
- Expected entropy remaining after testing attribute A is: Remainder(A) = \sum_{k=1}^d (pk + nk) / (p+n) B(pk/(pk+nk))
- Information gain from attribute test on A is: the expected reduction in entropy
  - Gain(A) = B(p/(p+n)) - Remainder(A)

### Early Stopping
- Definition: combine X^2 pruning and information gain to have the decision tree algorithm stop generating nodes when there is no good attribute to split on rather than generating nodes and afterwards pruning them away
- Problem: prevents us from recognizing situations where there is no one good attribute, but there are combinations of attributes that are informative
  - Generate and then prune alternatively handles this correctly

## Linear Regression and Classification

### Linear Functions
- Definition: 
- Inputs: continuous-valued

### Weights
- Purpose: minimize a loss
- Weight space: space defined by all possible settings of the weights

### Loss Function
- All L2 loss functions are {{ convex }} which implies {{ no local minima }}
- Use chain rule (where i use 6 to refer to partial derivs): 6g(f(x))/6x = g'(f(x)) 6f(x)/6x

### Univariate Linear Regression
- Definition: fitting a straight line
- Coefficients = weights
- Weight-space = 2D
- Function" h_w(x) = w_1x + w_0
- How? find values of the weights that minimize the empirical loss
- Loss function: squared-error loss function L2, summed over all the training examples

### Gradient Descent
- Definition: minimizes loss that doesn't depend on solving to find zeroes of the derivatives, and can be applied to any loss function, no matter how complex
- Process: choose starting point in weight space --> compute estimate of the gradient --> move small amount in the steepest downhill direction --> repeat until converge on a point in weight space with (local) minimum loss
- In multi linear regression, achieves: unique minimum of the loss function

#### Batch Gradient Descent
- Definition: learning rule for univariate linear regression where updates use the sum of the derivatives
- Guaranteed convergence to global minimum? --> {{ yes }}
- Time: {{ slow -- sum over all N training examples for every step, possibly with many steps }}
- Epoch: {{ step that covers all the training examples }}
- Loss surface: convex

#### Stochastic Gradient Descent (aka online gradient descent)
- Idea: randomly selects small number of training examples at each step, and updates according to 6/6wi Loss(w) = 2(y-hw(x)) 6/6wi (y-(w1x+w0))
- Time: {{ faster }}
- Reduces computation by selecting a: {{ minibatch of m out of the N examples }}
- If you reduce the amount of computation by a factor of 100 by using m=100 and N=10,000, then you increase standard error by a factor of {{ 10 }} because {{ standard error of the estimated mean gradient is proportional to the square root of the number of examples }}
- Guaranteed convergence to global minimum? {{ not strictly because it can oscillate around the minimum without settling down }}
- Can you guarantee convergence? {{ yes with a schedule of decreasing the learning rate, as in simulated annealing }}
- If loss surface is not convex, it can: {{ effectively find good local minima close to global minimum }}

### Multi Linear Regression
- Definition: regression for n-element vector examples, h is the matrix product of the transpose of the weights and the input vector
- Fix outstanding intercept term by: inventing dummy input attribute that's defined as always equal to 1
- Data matrix: matrix of inputs w one n-dimensional example per row
- Prevent overfitting through: {{ regularization }}

### Pseudoinverse / normal equation
- Pseudoinverse: (X^T X)^{-1} X^T
- Normal equation: equates minimum-loss weight vector to the pseudoinverse of the data matrix

### Regularization
- Definition: minimizes total cost of a hypothesis, counting the empirical loss and the complexity of the hypothesis
- For linear functions, complexity can be specified as: {{ a function of the weights }}
- Process: find point in box/circle (L1/L2) that is closest to the minimum 

#### L1 Regularization
- Definition: box regularization; minimizes sum of the absolute values
- Advantage: 
  - produces a sparse model (ie, sets many weights to zero, declaring those attributes irrelevant (much like Learn-Decision-Tree does))
  - less likely to overfit
- How does it produce a sparse model? ellipses hits the tangent line of the box at an axis, meaning some weights are equal to 0
- Number of examples required to find a good h irrelevant features is: {{ logarithmic }}

#### L2 Regularization
- Definition: minimize sum of squares
- Number of examples required to find a good h in the number of irrelevant features is: {{ linear }}

#### Choosing between L1 and L2
- Choose L1 when: axes are not interchangeable
- Choose L2 when: axes are interchangeable since the circle is rotationally invariant

### Linear Classification
- Decision boundary is: line / surface separating two classes; set of points where the model is undecided
- Linear separator: linear decision boundary for linearly separable data
- Threshold function: turns a continuous score into a class label

### Perceptron Learning Rule
- Definition: simple update rule that converges to a solution (aka a linear separator that classifies the data) provided the data are linearly separable
- (Similar to update rule for linear regression)
- Guaranteed convergence? --> {{ in practical application, sort of, but as a rule, yes but if not initially for fixed learning rate \alph, then for \alph decaying as O(1/t) for iteration number t }}

### Training Curve
- Measures: classifier performance on a fixed training set as the learning process proceeds one update at a time on that training set

### Softening Threshold Functions
- Definition: {{ approximation of the hard threshold with a continuous, differentiable function }}
- Resolves:
  - Non-differentiable, discontinuous hypothesis function 
  - Complete binary predictions of 1 or 0 (rather than some clear examples and some unclear borderline cases)

### Logistic Regression
- Process definition: fitting weights of a model to minimize loss on a data set by forming a soft boundary in the input space, outputting a probability of belonging to the class labeled 1

#### Logistic Function
- Used to replace hard threshold function
- Logistic(z) = 1 / (1+e^{-z})

### Learning Rate
- Definition: step size in gradient descent


### Common Traps
- Low training error = good model →  F, evaluate model with test set
- More data always helps →  F, can cause overfitting

---

# 11. Deep Learning Basics

DL Basics: Edition 4: Section 22.1-22.2

### Neural Networks
- Purpose: 
- Generalized idea: computation graph or dataflow graph; a circuit in which each node represents an elementary computation
- DL Process: construct computation graph --> adjust weights to fit the data

### Parameter Learning
- Definition: network learns by adjusting values of the parameters (inputs to nodes) so that the network as a whole fits the training data

### Node
- = unit
- What it does: 1. calculated {{ the weighted sum of the inputs from predecessor nodes }} --> 2. applies a {{ nonlinear function (nonlinear activation function) to produce its output }}

### Nonlinear activation function
- Definition: weighted sum of the inputs to unit j
- Must be nonlinear because: allows large networks to represent arbitrary functions, and if it were not, any composition of units would still represent a linear function
- Common ones:
  - Sigmoid
  - ReLU (aka, rectified linear unit)
  - Softplus (smooth version of ReLU; its derivative = sigmoid)
  - tanh (scaled and shifted sigmoid)
- Monotonically nondecreasing

### Universal Approximation
- States that: a network w just 2 layers of computational units, the first nonlinear and the second linear, can approximate any continuous function to an arbitrary degree of accuracy
- Means that: big networks can implement a lookup table for continuous functions

### Dummy Unit
- = extra input to each unit fixed to +1 with weight w_{0,j}
- Purpose in networks: {{ allows total weighted input to unit j be nonzero even when the outputs of the preceding layer are all zero }}

### Feedforward Networks
- Definition: network with connections only in one direction
- Graph: directed acyclic with designated input and output nodes
- Gradient computations always have the same structure as the underlying computation graph

#### Boolean Circuits
- Inputs: 0 and 1

### Recurrent Networks
- Definition: feeds intermediate or final outputs back into its own inputs
- Signa values form: {{ a dynamical system that has internal state or memory }}

### Gradient Descent in DL
- Weights leading into units in the output layer are: ones that produces the output of the network
  - Their gradient calculation is: same as typical gradient descent
- Weights leading into units in the hidden layer weights are: not directly connected to the outputs, have a more complex loss computation 

### Vanishing Gradient
- Definition: error signals are extinguished altogether as they are propagated back through the network

### Automatic Differentiation
- Systematic application of calculus to get gradients for any numeric program

## Weight w_{ij}
- (previous layer) j -- w_ij --> i (current layer)

### Back-propagation
- Idea: reverse mode differentiation, aka applying the chain rule "from the outside in"
- If we define \delta (ie, perceived error) for both the previous and current layers, then the gradient just becomes \delta_i, the perceived error at the current layer, * x_1, aka the information along the path from the previous layer back to the current layer

### End-to-end Learning
- Definition: complex computational system for a task can be composed from several trainable subsystems, then the entire system is trained in an end-to-end fashion from input/output pairs

### LLMs
- What they do:  

### Trustworthy AI
- Key concerns:  

### Common Traps
- LLMs truly understand language →  
- AI is unbiased →

---

# 11. Deep Learning for NLP

DL for NLP: Edition 4: Section 25.1-25.5


### Neural Networks
- Purpose:  

### LLMs
- What they do:  

### Trustworthy AI
- Key concerns:  

### Common Traps
- LLMs truly understand language →  
- AI is unbiased →  

---

# ✅ Final Summary (Last 5 minutes)

### 5 Things I Still Don’t Fully Understand
1.  
2.  
3.  
4.  
5.  

---

### 5 Concepts Most Likely to Appear as Tricky T/F
1.  
2.  
3.  
4.  
5.  

---

# 🔥 How to Use This

- Move FAST (don’t get stuck)  
- Fill with short phrases only  
- Skip confusing parts and come back later  
- Focus on patterns, not deep derivations  