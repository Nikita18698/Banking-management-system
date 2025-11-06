# Banking-management-system
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>

// Structure for storing account information
struct Account
{
    int acc_no;
    char name[50];
    float balance;
};

// Global variables
struct Account user;
int totalTransactions = 0;

// Function declarations
void createAccount();
void depositMoney();
void withdrawMoney();
void transferMoney();
void checkBalance();
void transactionHistory();
void saveTransaction(const char *detail);
void menu();
void divider();

// Function to print a line divider
void divider()
{
    for (int i = 0; i < 50; i++)
        printf("-");
    printf("\n");
}

// Main function
int main()
{
    int choice;

    printf("***** WELCOME TO BANK MANAGEMENT SYSTEM *****\n");
    divider();
    createAccount();

    while (1)
    {
        menu();
        printf("\nEnter your choice: ");
        scanf("%d", &choice);

        switch (choice)
        {
        case 1:
            depositMoney();
            break;
        case 2:
            withdrawMoney();
            break;
        case 3:
            transferMoney();
            break;
        case 4:
            checkBalance();
            break;
        case 5:
            transactionHistory();
            break;
        case 6:
            printf("\nThank you for banking with us!\n");
            divider();
            exit(0);
        default:
            printf("Invalid choice! Please try again.\n");
        }

        printf("\nPress Enter to continue...");
        getchar();
        getchar(); // waits for Enter key
    }

    return 0;
}

// Create new account
void createAccount()
{
    FILE *fptr = fopen("Account.txt", "w");
    if (fptr == NULL)
    {
        printf("Error creating account file!\n");
        exit(1);
    }

    printf("\nEnter your name: ");
    scanf(" %[^\n]", user.name);
    printf("Enter new account number: ");
    scanf("%d", &user.acc_no);
    user.balance = 10000; // initial balance

    fprintf(fptr, "Account Holder: %s\nAccount Number: %d\nInitial Balance: %.2f\n",
            user.name, user.acc_no, user.balance);
    fclose(fptr);

    printf("\nAccount created successfully!\n");
    printf("Initial Balance: ₹%.2f\n", user.balance);
}

// Deposit money
void depositMoney()
{
    float amount;
    time_t now;
    time(&now);

    printf("\n***** DEPOSIT MONEY *****\n");
    divider();
    printf("Enter amount to deposit: ");
    scanf("%f", &amount);

    if (amount <= 0)
    {
        printf("Invalid amount!\n");
        return;
    }

    user.balance += amount;
    printf("₹%.2f deposited successfully.\n", amount);
    printf("Updated Balance: ₹%.2f\n", user.balance);

    char detail[100];
    sprintf(detail, "Deposited: ₹%.2f on %s", amount, ctime(&now));
    saveTransaction(detail);
    totalTransactions++;
}

// Withdraw money
void withdrawMoney()
{
    float amount;
    time_t now;
    time(&now);

    printf("\n***** WITHDRAW MONEY *****\n");
    divider();
    printf("Enter amount to withdraw: ");
    scanf("%f", &amount);

    if (amount <= 0)
    {
        printf("Invalid amount!\n");
        return;
    }
    else if (amount > user.balance)
    {
        printf("Insufficient balance!\n");
        return;
    }

    user.balance -= amount;
    printf("₹%.2f withdrawn successfully.\n", amount);
    printf("Updated Balance: ₹%.2f\n", user.balance);

    char detail[100];
    sprintf(detail, "Withdrawn: ₹%.2f on %s", amount, ctime(&now));
    saveTransaction(detail);
    totalTransactions++;
}

// Transfer money
void transferMoney()
{
    float amount;
    int recv_acc;
    time_t now;
    time(&now);

    printf("\n***** TRANSFER MONEY *****\n");
    divider();
    printf("Enter recipient account number: ");
    scanf("%d", &recv_acc);
    printf("Enter amount to transfer: ");
    scanf("%f", &amount);

    if (amount <= 0)
    {
        printf("Invalid amount!\n");
        return;
    }
    else if (amount > user.balance)
    {
        printf("Insufficient balance!\n");
        return;
    }

    user.balance -= amount;
    printf("₹%.2f transferred successfully to Account No. %d.\n", amount, recv_acc);
    printf("Updated Balance: ₹%.2f\n", user.balance);

    char detail[150];
    sprintf(detail, "Transferred: ₹%.2f to Account %d on %s", amount, recv_acc, ctime(&now));
    saveTransaction(detail);
    totalTransactions++;
}

// Check balance
void checkBalance()
{
    printf("\n***** ACCOUNT DETAILS *****\n");
    divider();
    printf("Account Holder: %s\n", user.name);
    printf("Account Number: %d\n", user.acc_no);
    printf("Current Balance: ₹%.2f\n", user.balance);
    printf("Total Transactions: %d\n", totalTransactions);
    divider();
}

// Show transaction history
void transactionHistory()
{
    FILE *fptr = fopen("Account.txt", "r");
    if (fptr == NULL)
    {
        printf("No transaction record found!\n");
        return;
    }

    printf("\n***** TRANSACTION HISTORY *****\n");
    divider();

    char line[200];
    while (fgets(line, sizeof(line), fptr))
    {
        printf("%s", line);
    }

    fclose(fptr);
    divider();
}

// Save each transaction in file
void saveTransaction(const char *detail)
{
    FILE *fptr = fopen("Account.txt", "a");
    if (fptr == NULL)
    {
        printf("Error saving transaction!\n");
        return;
    }
    fprintf(fptr, "%s\n", detail);
    fclose(fptr);
}

// Menu options
void menu()
{
    divider();
    printf("\tBANK MENU\n");
    divider();
    printf("1. Deposit Money\n");
    printf("2. Withdraw Money\n");
    printf("3. Transfer Money\n");
    printf("4. Check Balance / Account Details\n");
    printf("5. Transaction History\n");
    printf("6. Exit\n");
    divider();
}
