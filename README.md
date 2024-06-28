#include <iostream>
#include <string>
#include <unordered_map>
#include <vector>
#include <limits>

// Struct for user accounts
struct Account {
    std::string username;
    std::string password;
    double balance;
    std::vector<std::string> transactionHistory;
};

// Global map to store accounts
std::unordered_map<std::string, Account> accounts;
std::string currentUser;

void registerUser();
bool loginUser();
void mainMenu();
void viewAccountDetails();
void depositFunds();
void withdrawFunds();
void transferFunds();
void viewTransactionHistory();
void logout();

int main() {
    int choice;
    while (true) {
        std::cout << "Welcome to the Banking Management System\n";
        std::cout << "1. Register\n";
        std::cout << "2. Login\n";
        std::cout << "3. Exit\n";
        std::cout << "Enter your choice: ";
        std::cin >> choice;
        std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n'); // Clear the input buffer

        switch (choice) {
            case 1:
                registerUser();
                break;
            case 2:
                if (loginUser()) {
                    mainMenu();
                }
                break;
            case 3:
                std::cout << "Thank you for using the Banking Management System.\n";
                return 0;
            default:
                std::cout << "Invalid choice. Please try again.\n";
        }
    }
}

void registerUser() {
    std::string username, password;
    std::cout << "Register a new account\n";
    std::cout << "Enter username: ";
    std::getline(std::cin, username);
    std::cout << "Enter password: ";
    std::getline(std::cin, password);

    if (accounts.find(username) != accounts.end()) {
        std::cout << "Username already exists. Please choose a different username.\n";
        return;
    }

    accounts[username] = {username, password, 0.0, {}};
    std::cout << "Account registered successfully.\n";
}

bool loginUser() {
    std::string username, password;
    std::cout << "Login to your account\n";
    std::cout << "Enter username: ";
    std::getline(std::cin, username);
    std::cout << "Enter password: ";
    std::getline(std::cin, password);

    if (accounts.find(username) != accounts.end() && accounts[username].password == password) {
        currentUser = username;
        std::cout << "Login successful.\n";
        return true;
    } else {
        std::cout << "Invalid username or password. Please try again.\n";
        return false;
    }
}

void mainMenu() {
    int choice;
    while (true) {
        std::cout << "\nMain Menu\n";
        std::cout << "1. View Account Details\n";
        std::cout << "2. Deposit Funds\n";
        std::cout << "3. Withdraw Funds\n";
        std::cout << "4. Transfer Funds\n";
        std::cout << "5. View Transaction History\n";
        std::cout << "6. Logout\n";
        std::cout << "Enter your choice: ";
        std::cin >> choice;
        std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n'); // Clear the input buffer

        switch (choice) {
            case 1:
                viewAccountDetails();
                break;
            case 2:
                depositFunds();
                break;
            case 3:
                withdrawFunds();
                break;
            case 4:
                transferFunds();
                break;
            case 5:
                viewTransactionHistory();
                break;
            case 6:
                logout();
                return;
            default:
                std::cout << "Invalid choice. Please try again.\n";
        }
    }
}

void viewAccountDetails() {
    Account &account = accounts[currentUser];
    std::cout << "\nAccount Details\n";
    std::cout << "Username: " << account.username << "\n";
    std::cout << "Balance: $" << account.balance << "\n";
}

void depositFunds() {
    double amount;
    std::cout << "Enter amount to deposit: ";
    std::cin >> amount;

    if (amount > 0) {
        accounts[currentUser].balance += amount;
        accounts[currentUser].transactionHistory.push_back("Deposited: $" + std::to_string(amount));
        std::cout << "Deposit successful. New balance: $" << accounts[currentUser].balance << "\n";
    } else {
        std::cout << "Invalid amount. Please enter a positive value.\n";
    }
}

void withdrawFunds() {
    double amount;
    std::cout << "Enter amount to withdraw: ";
    std::cin >> amount;

    if (amount > 0 && amount <= accounts[currentUser].balance) {
        accounts[currentUser].balance -= amount;
        accounts[currentUser].transactionHistory.push_back("Withdrew: $" + std::to_string(amount));
        std::cout << "Withdrawal successful. New balance: $" << accounts[currentUser].balance << "\n";
    } else {
        std::cout << "Invalid amount or insufficient funds.\n";
    }
}

void transferFunds() {
    std::string recipient;
    double amount;
    std::cout << "Enter recipient's username: ";
    std::cin.ignore();
    std::getline(std::cin, recipient);
    std::cout << "Enter amount to transfer: ";
    std::cin >> amount;

    if (accounts.find(recipient) != accounts.end() && amount > 0 && amount <= accounts[currentUser].balance) {
        accounts[currentUser].balance -= amount;
        accounts[recipient].balance += amount;
        accounts[currentUser].transactionHistory.push_back("Transferred: $" + std::to_string(amount) + " to " + recipient);
        accounts[recipient].transactionHistory.push_back("Received: $" + std::to_string(amount) + " from " + currentUser);
        std::cout << "Transfer successful. New balance: $" << accounts[currentUser].balance << "\n";
    } else {
        std::cout << "Invalid recipient, amount, or insufficient funds.\n";
    }
}

void viewTransactionHistory() {
    Account &account = accounts[currentUser];
    std::cout << "\nTransaction History\n";
    for (const std::string &record : account.transactionHistory) {
        std::cout << record << "\n";
    }
}

void logout() {
    std::cout << "Logged out successfully.\n";
    currentUser.clear();
}
