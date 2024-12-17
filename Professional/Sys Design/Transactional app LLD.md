15/12/2024 14:08

Tags: [[BASIC OOPS]][[Low Level Design]]

Status:

# Transactional app LLD

Here is a detailed **Machine Code** and **Low-Level Design (LLD)** solution for a transactional application.

The requirements include:

1. **Tracking transactions** for multiple users (withdrawals and deposits).
2. **Deleting the last transaction**.
3. **Updating the current balance**.
4. **Handling person-to-person (P2P) payments** where balances are updated.

---

## **Low-Level Design (LLD)**

### **Key Components**

1. **User**: Represents a user with attributes like `userID`, `name`, and `balance`.
2. **Transaction**: Represents a transaction with attributes like `transactionID`, `amount`, `type` (Deposit, Withdraw, or Transfer), and `timestamp`.
3. **TransactionManager**: Manages all transactions, such as adding a transaction, deleting the last transaction, and managing balances.
4. **UserManager**: Manages user creation, user lookups, and updating balances.

---

### **Classes**

1. **User**
    
    - Attributes:
        - `userID` (Unique Identifier)
        - `name`
        - `balance`
    - Methods:
        - `getBalance()`: Return current balance.
        - `updateBalance(amount)`: Update user balance.
2. **Transaction**
    
    - Attributes:
        - `transactionID` (Unique Identifier)
        - `type` (Deposit, Withdraw, Transfer)
        - `amount`
        - `senderID` (For P2P payments)
        - `receiverID` (Optional for transfers)
        - `timestamp`
    - Methods:
        - Constructor to initialize a transaction.
3. **TransactionManager**
    
    - Attributes:
        - A **map** to store user transactions (`userID -> stack of transactions`).
        - A **global transaction counter**.
    - Methods:
        - `withdraw(userID, amount)`: Withdraw amount and update balance.
        - `deposit(userID, amount)`: Deposit amount and update balance.
        - `deleteLastTransaction(userID)`: Remove the last transaction and revert balance.
        - `transfer(senderID, receiverID, amount)`: Handle P2P transactions and update both balances.
4. **UserManager**
    
    - Attributes:
        - A **map** to store users (`userID -> User`).
    - Methods:
        - `addUser(userID, name, initialBalance)`: Create a new user.
        - `getUser(userID)`: Fetch user details.

---

## **Code Implementation (C++)**





### Examples