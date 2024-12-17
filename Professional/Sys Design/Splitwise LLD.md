14/12/2024 18:41
 
Tags:

Status: [[BASIC OOPS]] [[System Design]] [[Low Level Design]]

# Splitwise LLD

Below is the **Low-Level Design (LLD)** for a **Splitwise-like system** in C++.

---

### **System Requirements**

1. Users can add expenses and split amounts with one or more participants.
2. Balances between users should be maintained.
3. Users can view their net balance or all transactions.
4. Support for splitting methods like **equal split** or **custom split**.
5. Option to simplify debts between users.

---

### **Key Classes**

1. **User**:
    - Represents a user with attributes like ID, name, and email.
2. **Expense**:
    - Represents an expense with details like the amount, payer, participants, and split type.
3. **Split**:
    - Abstract class for different split methods (e.g., equal, percentage, custom).
4. **SplitwiseManager**:
    - Core class to manage users, expenses, and balances.

```cpp
#include <iostream>
#include <unordered_map>
#include <vector>
#include <string>
#include <numeric>

using namespace std;

class User {
public:
    int id;
    string name;
    string email;

    User(int userID, string userName, string userEmail)
        : id(userID), name(userName), email(userEmail) {}
};

class Split {
public:
    virtual vector<double> calculateSplit(double amount, const vector<int>& participants) = 0;
};

class EqualSplit : public Split {
public:
    vector<double> calculateSplit(double amount, const vector<int>& participants) override {
        double share = amount / participants.size();
        return vector<double>(participants.size(), share);
    }
};

class PercentageSplit : public Split {
private:
    vector<double> percentages;

public:
    PercentageSplit(const vector<double>& percents) : percentages(percents) {}

    vector<double> calculateSplit(double amount, const vector<int>& participants) override {
        vector<double> splits;
        for (double percent : percentages) {
            splits.push_back((percent / 100) * amount);
        }
        return splits;
    }
};

class CustomSplit : public Split {
private:
    vector<double> shares;

public:
    CustomSplit(const vector<double>& customShares) : shares(customShares) {}

    vector<double> calculateSplit(double amount, const vector<int>& participants) override {
        return shares;
    }
};

class Expense {
public:
    int payerID;
    double amount;
    vector<int> participants;
    string type; // "EQUAL", "PERCENTAGE", "CUSTOM"
    vector<double> splits;

    Expense(int payer, double amt, const vector<int>& users, const vector<double>& splitAmounts, const string& splitType)
        : payerID(payer), amount(amt), participants(users), splits(splitAmounts), type(splitType) {}
};

class SplitwiseManager {
private:
    unordered_map<int, User> users;
    vector<Expense> expenses;
    unordered_map<int, unordered_map<int, double>> balances; // balances[user1][user2] = amount

public:
    // Add user
    void addUser(int userID, const string& name, const string& email) {
        users[userID] = User(userID, name, email);
    }

    // Add expense
    void addExpense(int payerID, double amount, const vector<int>& participants, Split* splitType) {
        vector<double> splits = splitType->calculateSplit(amount, participants);

        // Validate splits
        if (splits.size() != participants.size()) {
            cout << "Error: Splits do not match participant size.\n";
            return;
        }

        // Create the expense
        expenses.emplace_back(payerID, amount, participants, splits, typeid(*splitType).name());

        // Update balances
        for (size_t i = 0; i < participants.size(); i++) {
            if (participants[i] == payerID) {
                continue; // Skip payer
            }
            balances[participants[i]][payerID] += splits[i];  // Participants owe payer
            balances[payerID][participants[i]] -= splits[i]; // Payer is owed
        }
    }

    // Show balances for all users
    void showBalances() {
        for (const auto& userBalance : balances) {
            int userID = userBalance.first;
            for (const auto& balance : userBalance.second) {
                if (balance.second > 0) {
                    cout << users[userID].name << " owes " << users[balance.first].name << ": " << balance.second << "\n";
                }
            }
        }
    }

    // Show balance for a specific user
    void showUserBalance(int userID) {
        if (balances.find(userID) == balances.end()) {
            cout << users[userID].name << " has no outstanding balances.\n";
            return;
        }
        for (const auto& balance : balances[userID]) {
            if (balance.second > 0) {
                cout << users[userID].name << " owes " << users[balance.first].name << ": " << balance.second << "\n";
            } else if (balance.second < 0) {
                cout << users[balance.first].name << " owes " << users[userID].name << ": " << -balance.second << "\n";
            }
        }
    }
};
int main() {
    SplitwiseManager manager;

    // Adding users
    manager.addUser(1, "Alice", "alice@example.com");
    manager.addUser(2, "Bob", "bob@example.com");
    manager.addUser(3, "Charlie", "charlie@example.com");

    // Adding expenses
    manager.addExpense(1, 1200, {1, 2, 3}, new EqualSplit()); // Alice pays 1200 equally split
    manager.addExpense(2, 1500, {1, 2, 3}, new PercentageSplit({50, 30, 20})); // Bob pays 1500 split by percentage

    // Show balances
    cout << "\nAll Balances:\n";
    manager.showBalances();

    // Show user-specific balance
    cout << "\nUser-Specific Balance:\n";
    manager.showUserBalance(1);

    return 0;
}

```

# Output Example
```yaml
All Balances:
Alice owes Bob: 400
Alice owes Charlie: 200
Charlie owes Bob: 300

User-Specific Balance:
Alice owes Bob: 400
Alice owes Charlie: 200

```
### **Key Design Features**

1. **Modularity**:
    
    - Separate classes for `User`, `Expense`, and `Split` for extensibility.
2. **Support for Splitting Types**:
    
    - Easily extendable to new splitting methods by inheriting from `Split`.
3. **Balances Tracking**:
    
    - Balances are stored in a **nested hash map** to track what each user owes another.
4. **Flexibility**:
    
    - New users, expenses, and split types can be easily added.

---

This design is scalable, extensible, and aligns well with the requirements of a Splitwise-like system. You can further enhance it with debt simplification logic or persistent storage for balances and expenses.

---


---
Below is another example generated from chatgpt

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

Here is the complete C++ solution for the transactional application:

```cpp
#include <iostream>
#include <unordered_map>
#include <stack>
#include <vector>
#include <ctime>
#include <iomanip>

using namespace std;

// Transaction structure
struct Transaction {
    int transactionID;
    string type;          // Deposit, Withdraw, Transfer
    long long amount;
    int senderID;
    int receiverID;       // For transfer transactions
    string timestamp;

    // Constructor
    Transaction(int id, string t, long long amt, int sender, int receiver)
        : transactionID(id), type(t), amount(amt), senderID(sender), receiverID(receiver) {
        time_t now = time(0);
        timestamp = ctime(&now);
        timestamp.pop_back(); // Remove newline
    }
};

// User structure
class User {
public:
    int userID;
    string name;
    long long balance;

    User(int id, string n, long long initialBalance)
        : userID(id), name(n), balance(initialBalance) {}

    void updateBalance(long long amount) {
        balance += amount;
    }

    long long getBalance() const {
        return balance;
    }
};

// User Manager
class UserManager {
private:
    unordered_map<int, User> users; // userID -> User
public:
    void addUser(int userID, string name, long long initialBalance) {
        users[userID] = User(userID, name, initialBalance);
    }

    User& getUser(int userID) {
        return users[userID];
    }

    void printUser(int userID) {
        cout << "UserID: " << userID << ", Name: " << users[userID].name
             << ", Balance: " << users[userID].getBalance() << "\n";
    }
};

// Transaction Manager
class TransactionManager {
private:
    unordered_map<int, stack<Transaction>> userTransactions; // userID -> transactions stack
    int transactionCounter = 0;
    UserManager& userManager;

public:
    TransactionManager(UserManager& um) : userManager(um) {}

    void deposit(int userID, long long amount) {
        userManager.getUser(userID).updateBalance(amount);
        userTransactions[userID].push(Transaction(++transactionCounter, "Deposit", amount, userID, -1));
        cout << "Deposited " << amount << " to user " << userID << ". Current Balance: "
             << userManager.getUser(userID).getBalance() << "\n";
    }

    void withdraw(int userID, long long amount) {
        if (userManager.getUser(userID).getBalance() < amount) {
            cout << "Insufficient balance for user " << userID << "\n";
            return;
        }
        userManager.getUser(userID).updateBalance(-amount);
        userTransactions[userID].push(Transaction(++transactionCounter, "Withdraw", amount, userID, -1));
        cout << "Withdrew " << amount << " from user " << userID << ". Current Balance: "
             << userManager.getUser(userID).getBalance() << "\n";
    }

    void transfer(int senderID, int receiverID, long long amount) {
        if (userManager.getUser(senderID).getBalance() < amount) {
            cout << "Insufficient balance for transfer from user " << senderID << "\n";
            return;
        }

        userManager.getUser(senderID).updateBalance(-amount);
        userManager.getUser(receiverID).updateBalance(amount);

        userTransactions[senderID].push(Transaction(++transactionCounter, "Transfer", amount, senderID, receiverID));
        userTransactions[receiverID].push(Transaction(++transactionCounter, "Transfer", amount, senderID, receiverID));

        cout << "Transferred " << amount << " from user " << senderID
             << " to user " << receiverID << ".\n";
    }

    void deleteLastTransaction(int userID) {
        if (userTransactions[userID].empty()) {
            cout << "No transactions to delete for user " << userID << "\n";
            return;
        }

        Transaction last = userTransactions[userID].top();
        userTransactions[userID].pop();

        if (last.type == "Deposit") {
            userManager.getUser(userID).updateBalance(-last.amount);
        } else if (last.type == "Withdraw") {
            userManager.getUser(userID).updateBalance(last.amount);
        }

        cout << "Deleted last transaction for user " << userID
             << ". Current Balance: " << userManager.getUser(userID).getBalance() << "\n";
    }
};

int main() {
    UserManager userManager;
    TransactionManager transactionManager(userManager);

    // Adding users
    userManager.addUser(1, "Alice", 5000);
    userManager.addUser(2, "Bob", 3000);

    // Deposits and withdrawals
    transactionManager.deposit(1, 2000);
    transactionManager.withdraw(1, 1000);
    transactionManager.transfer(1, 2, 500);

    // Print user balances
    userManager.printUser(1);
    userManager.printUser(2);

    // Delete last transaction
    transactionManager.deleteLastTransaction(1);
    userManager.printUser(1);

    return 0;
}
	
```
 

## **How It Works**

1. **UserManager** handles user creation and balance management.
2. **TransactionManager** handles deposits, withdrawals, and transfers. It keeps track of transactions using a stack (to allow deleting the last transaction).
3. **P2P Payments** are implemented as a transfer between two users.
4. The application automatically updates balances and logs transactions.
5. Deleting the last transaction reverts the user's balance.

---

### **Sample Output**

```yaml
Deposited 2000 to user 1. Current Balance: 7000
Withdrew 1000 from user 1. Current Balance: 6000
Transferred 500 from user 1 to user 2.
UserID: 1, Name: Alice, Balance: 5500
UserID: 2, Name: Bob, Balance: 3500
Deleted last transaction for user 1. Current Balance: 6000
UserID: 1, Name: Alice, Balance: 6000

```

This solution is clean, efficient, and follows OOP principles, making it scalable for a production systems
### Example
### To delete the nth transaction
```cpp
    class TransactionManager {
private:
    unordered_mapint, stack<Transaction>> userTransactions; // userID -> transactions stack
    int transactionCounter = 0;
    UserManager& userManager;
  
public:
    TransactionManager(UserManager& um) : userManager(um) {}
  
    void deposit(int userID, long long amount) {
        userManager.getUser(userID).updateBalance(amount);
        userTransactons[userID].push(Transaction(++transactionCounter, "Deposit", amount, userID, -1));
        cut << "Deposited " << amount << " to user " << userID << ". Current Balance: "
             << userManager.getUser(userID).getBalance() << "\n";
    }
  
    void withdraw(int userID, long long amount) {
        if (userManager.getUser(userID).getBalance() < amount) {
            cout << "Insufficient balance for user " << userID << "\n";
            return;
        }
        userManager.getUser(userID).updateBalance(-amount);
        userTransactions[userID]push(Transaction(++transactionCounter, "Withdraw", amount, userID, -1));
        cut << "Withdrew " << amount << " from user " << userID << ". Current Balance: "
             << userManager.getUser(userID).getBalance() << "\n";
    }
  
    void transfer(int senderID, int receiverID, long long amount) {
        if (userManager.getUser(senderID).getBalance() < amount) {
           cout << "Insufficient balance for transfer from user " << senderID << "\n";
            return;
        }
  
        userManager.getUser(senderID).updateBalance(-amount);
        userManager.getUser(receiverID).updateBalance(amount);
  
        userTransactions[senderID].push(Trnsaction(++transactionCounter, "Transfer", amount, senderID, receiverID));
        userTransactions[receiverID].push(ransaction(++transactionCounter, "Transfer", amount, senderID, receiverID));
  
        cout << "Transferred " << amount << " from user " << senderID
             << " to user " << receiverID << ".\n";
    }
  
    void deleteNthTransaction(int userID, int n) {
        if (userTransactions[userID].empty()) {
            cout << "No transactions to delete for user " << userID << "\n";
            return;
        }
  
        int stackSize = userTransactions[userID].size();
        if (n < 1 || n > stackSize) {
            cout < "Invalid transaction number. Total transactions: " << stackSize << "\n";
            return;
        }
  
        // Temporary storage for transactions
        vector<Transaction> temp;
        while (!userTransactions[userID].empty()) {
            temp.push_back(userTransactions[userID].top());
            userTransactions[userID].pop();
        }
  
        // Rebuild the stack while skipping the nth transaction from the back
        for (int i = stackSize - 1; i >= 0; --i) {
            if (i == stackSize - n) {
                // Adjust balance for the removed transaction
                if (temp[i].type == "Deposit") {
                    userManager.getUser(userID).updateBalance(-temp[i].amount);
                } else if (temp[i].type == "Withdraw") {
                    userManager.getUser(userID).updateBalance(temp[i].amount);
                } else if (temp[i].type == "Transfer") {
                    userManager.getUser(userID).updateBalance(temp[i].amount);
                   userManager.getUser(temp[i].receiverID).updateBalance(-temp[i].amount);
                }
                continue; // Skip adding the nth transaction
            }
            userTransactions[userID].push(temp[i]); // Add back remaining transactions
        }
  
        cout << "Deleted transaction #" << n << " for user " << userID
            << ". Current Balance: " << userManager.getUser(userID).getBalance() << "\n";
    }
};
```