**04-13 Crypto Economics**

_Applications_
* Auctions
* Exchanges
* Governance
* Currency
  * Our focus today -- how to make currency on a decentralized system

_Barter_
* Coincidence of wants
* ie: Alice and Bob; A has apple, B has orange, => they can exchange 
* cycle gets more complicated when you have more than 2 people ...
* ie: Alice, Bob, and Charlie; A has apple, B has orange, C has grape; C's preferences: Orange < Grape < Apple
* ... why money is useful ...

_With Money_
* ie: 
  * (where preferences work with this example and everyone gets what they want)
  * C give A money for the apple, A give B money for the orange, B give C money for grapes  

_Gold Standard_
* Q: First gold coins were minted around 610 BC. Why gold? 
  * A: Gold keeps the same form overtime
* Later: paper currency. Countries adopt "gold standard" in the 1800s to build trust
* _Bretton Woods System_ 1946 collapsed during the Vietnam War
  * Q: Why did the gold standard end?
    * A: The government wanted to print more money, but under the gold standard they were tied to gold, so they moved to _fiat_

_Fiat Currency_
* "fiat" = "let it be done"
* _Value_: only tied to expectation that in the future you can exchange it to buy yourself something
* Origin: 11th century China

_Gold vs Fiat: Pros and Cons_
* Pros:
  * Fiat:
  * Gold:
    * Pegged, less volatile
* Cons:
  * Fiat:
    * Risks with too much money printing
    * No longer pegged, more volatile

_Digital Currency_
* Q: Why digital currency?
  * A: 
    * Transaction costs are significantly lower
    * Algorithmically controlled supply
    * Transferable "via code without intermediaries, bust costly in PoW"
    * Cheap storage
    * Division (simple additions and comparisons "a + b = c + d")
    * Hard to fake (in Cryptomania [1])
* Q: Suppose you wish to purchase an online software. How would you transfer payment?
  * A: 
    * At any time, any buyer b can generate a transaction to pay any seller any amount d of PayPal dollars
    * PayPal hears about the transaction and checks it for validity
    * If valid, PayPal confirms to the participants that the transaction occured
    * PayPal takes notes that buyer b has d fewer PayPal dollars and seller s has d more PayPal dollars (no physical money is ever moved)
    * You must have a bank account attached to your account when you register to transfer money to/from your bank account and convert PayPal dollars for real dollars

_Attacks_
* Q: What are some vulnerabilities / risks of this system PayPal
  * A: Leaks; PayPal dollars somehow change in value compared to real dollars
* _Steal money_: falsely generated transaction
  * Defense: public key cryptography
* _Privacy_: PayPal shares your spending history with the gov or others
* _Rewrite History_: PayPal could modify or delete transaction from ledger
* _Censorship_: PayPal decides to no longer authorize transactions

_Attack Solutions_
* Privacy Solution:
    * At any time, any buyer b can generate a transaction to pay any seller any amount d of PayPal dollars
    * PayPal hears about the transaction and checks it for validity
    * If valid, PayPal confirms to the participants that the transaction occured
    * PayPal takes notes that buyer b has d fewer PayPal dollars and seller s has d more PayPal dollars (no physical money is ever moved)
    _* You can make as many PayPal accounts as you like, for free. You only need a bank account attached in order to trade your PayPal dollars for real dollars_
* Protecting Against History Rewriting Solution:
  * At any time, any buyer b can generate a transaction to pay any seller any amount d of PayPal dollars
  * PayPal hears about the transaction and checks it for validity
  * If valid, PayPal confirms to the participants that the transaction occured, _and broadcasts to the entire network that this transaction occurred_
  * PayPal, _and all participants_, take notes that buyer b has d fewer PayPal dollars and seller s has d more PayPal dollars (no physical money is ever moved)
  * You can make as many PayPal accounts as you like, for free. You only need a bank account attached in order to trade your PayPal dollars for real dollars
* Protecting Against Money Freeze Solution
  * At any time, any buyer b can generate a transaction to pay any seller any amount d of PayPal dollars and broadcast it to the entire network
  * At every step, a member of the network (the leader) is randomly selected to authorize transactions. That member broadcasts all transactions they consider to be valid
  * Based on transactions that were authorized, all participants verify if the authorized transactions are valid. If so, update their perceived balances.
  * You can make as many PayPal accounts as you like, for free. You only need a bank account attached in order to trade your PayPal dollars for real dollars
* Q: what's the problem with the money freeze solution?
  * A (idea):
    * What happens if multiple members exchange at the same time, and one of them is chosen as the leader, and they can authorize the transaction
  * A:
    * _Sybil attack_
    * Occurs at: "At every step, a member of the network (the leader) is randomly selected to authorize transactions. That member broadcasts all transactions they consider to be valid"
    * Motivation: leader receives some sort of monetary reward
* Protecting Against Sybil Attack:
  * At any time, any buyer b can generate a transaction to pay any seller any amount d of PayPal dollars and broadcast it to the entire network
  * At every step, _A leader selection mechanism (ie, PoW) chooses a member of the network to authorize transactions_. That member broadcasts all transactions they consider to be valid
  * Based on transactions that were authorized, all participants verify if the authorized transactions are valid. If so, update their perceived balances.
  * You can make as many PayPal accounts as you like, for free. You only need a bank account attached in order to trade your PayPal dollars for real dollars

_The Challenge_

_Cryptographies_
* Public key encryption
* Hash Function
  * Y = H(X): **`{0,1}^1024`** --> **`{0,1}^256`**
  * Assumptions:
    * Efficient: given X, easy to compute Hash(X)
    * One-Way: given Y=H(X), it is _hard_ to find X such that H(X)=Y
    * Collision-Resistant: given (X,Y), _hard_ to find X' s.t. H(X')=Y
    * Random Oracle: given X, H(X) is uniformly random over **`{0,1}^256`**
      * ie: H(X)=Z, Z **`\in_R`** **`{0,1}^256`**

_Proof-of-Work_
* PoW(H): find an X s.t. H(X) starts with 76 zero bits
* Q: why does 
  * A: 