# Sample questions

## Classes and inheritance

### Question 1:

The below diagram illustrates the relation between the classes and their methods and fields, the architecture of the program is often presented by such a diagram. 

![01_widow_layout](./_images/class_diagram.png)

based on the given diagram write a program that presents a banking system. Create instances of the classes in main as convenient and call its public methods with appropriate parameters.

### Answer 1:



```cpp
#include <iostream>
#include <string>
#include <vector>

class Account {
protected:
    std::string accountNumber;
    double balance;

public:
    Account(const std::string& accountNumber, double balance)
        : accountNumber(accountNumber), balance(balance) {}

    void deposit(double amount) {
        balance += amount;
        std::cout << "Deposit successful. New balance: " << balance << std::endl;
    }

    void withdraw(double amount) {
        if (balance >= amount) {
            balance -= amount;
            std::cout << "Withdrawal successful. New balance: " << balance << std::endl;
        }
        else {
            std::cout << "Insufficient funds. Balance: " << balance << std::endl;
        }
    }

    double getBalance() const {
        return balance;
    }

    const std::string& getAccountNumber() const {
        return accountNumber;
    }
};

class CheckingAccount : public Account {
public:
    CheckingAccount(const std::string& accountNumber, double balance)
        : Account(accountNumber, balance) {}

    void withdraw(double amount) {
        // Charge a $1.50 transaction fee for each withdrawal
        double totalAmount = amount + 1.5;
        if (balance >= totalAmount) {
            balance -= totalAmount;
            std::cout << "Withdrawal successful. New balance: " << balance << std::endl;
        }
        else {
            std::cout << "Insufficient funds. Balance: " << balance << std::endl;
        }
    }
};

class SavingsAccount : public Account {
protected:
    double interestRate;

public:
    SavingsAccount(const std::string& accountNumber, double balance, double interestRate)
        : Account(accountNumber, balance), interestRate(interestRate) {}

    void addInterest() {
        double interest = balance * interestRate / 100;
        balance += interest;
        std::cout << "Interest added. New balance: " << balance << std::endl;
    }

    double getInterestRate() const {
        return interestRate;
    }
};

class Client {
private:
    std::string name;
    std::vector<Account*> accounts;

public:
    Client(const std::string& name)
        : name(name) {}

    void addAccount(Account* account) {
        accounts.push_back(account);
    }

    void printAccounts() const {
        std::cout << "Accounts for " << name << ":\n";
        for (const auto& account : accounts) {
            std::cout << account->getAccountNumber() << " - $" << account->getBalance() << "\n";
        }
    }

    const std::string& getName() const {
        return name;
    }
};

class Bank {
private:
    std::vector<Client*> clients;

public:
    void addClient(Client* client) {
        clients.push_back(client);
    }

    void printClients() const {
        std::cout << "Clients:\n";
        for (const auto& client : clients) {
            std::cout << client->getName() << "\n";
            client->printAccounts();
            std::cout << "\n";
        }
    }
};

int main() {
    Bank bank;

    // Create some clients and accounts
    Client john("John Smith");
    CheckingAccount johnChecking("123456", 1000.0);
    SavingsAccount johnSavings("654321", 5000.0, 2.5);
    john.addAccount(&johnChecking);
    john.addAccount(&johnSavings);
    bank.addClient(&john);

    Client jane("Jane Doe");
    SavingsAccount janeS
```

