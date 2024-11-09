#include <iostream>
#include <string>
#include <queue>
#include <stack>

using namespace std;

class Book {
public:
    string title;
    string author;
    bool isIssued;
    Book* next; 

    Book(string t, string a) : title(t), author(a), isIssued(false), next(nullptr) {}
};

class Library {
private:
    Book* head;      
    stack<string> issuedBooks; 
    queue<string> waitlist;   

public:
    Library() : head(nullptr) {}

    void addBook(string title, string author) {
        Book* newBook = new Book(title, author);
        if (!head) {
            head = newBook;
        } else {
            Book* temp = head;
            while (temp->next) {
                temp = temp->next;
            }
            temp->next = newBook;
        }
        cout << "Book added: " << title << " by " << author << endl;
    }

    void viewBooks() {
        cout << "Available Books: \n";
        Book* temp = head;
        while (temp) {
            cout << temp->title << " by " << temp->author;
            if (temp->isIssued) {
                cout << " (Issued)";
            }
            cout << endl;
            temp = temp->next;
        }
    }

    void issueBook(string title) {
        Book* temp = head;
        while (temp) {
            if (temp->title == title && !temp->isIssued) {
                temp->isIssued = true;
                issuedBooks.push(title); 
                cout << "Book issued: " << title << endl;
                return;
            }
            temp = temp->next;
        }
        cout << "Book not available for issue." << endl;
        cout << "Would you like to be added to the waitlist? (yes/no): ";
        string response;
        cin >> response;
        if (response == "yes") {
            string user;
            cout << "Enter your name: ";
            cin >> ws; 
            getline(cin, user);
            waitlist.push(user);
            cout << user << " added to the waitlist." << endl;
        }
    }

    void returnBook(string title) {
        Book* temp = head;
        while (temp) {
            if (temp->title == title && temp->isIssued) {
                temp->isIssued = false;
                cout << "Book returned: " << title << endl;
                issuedBooks.pop(); 
                if (!waitlist.empty()) {
                    string user = waitlist.front();
                    waitlist.pop();
                    cout << user << " notified for " << title << endl;
                }
                return;
            }
            temp = temp->next;
        }
        cout << "Book not issued or does not exist." << endl;
    }

    void viewIssuedBooks() {
        cout << "Recently Issued Books: \n";
        stack<string> tempStack = issuedBooks; 
        while (!tempStack.empty()) {
            cout << tempStack.top() << endl;
            tempStack.pop();
        }
    }

    ~Library() {
        
        Book* temp;
        while (head) {
            temp = head;
            head = head->next;
            delete temp;
        }
    }
};

int main() {
    Library library;
    int choice;
    string title, author;

    do {
        cout << "\nLibrary Management System\n";
        cout << "1. Add Book\n";
        cout << "2. View Books\n";
        cout << "3. Issue Book\n";
        cout << "4. Return Book\n";
        cout << "5. View Recently Issued Books\n";
        cout << "6. Exit\n";
        cout << "Choose an option: ";
        cin >> choice;

        switch (choice) {
            case 1:
                cout << "Enter book title: ";
                cin >> ws; // to ignore leading whitespace
                getline(cin, title);
                cout << "Enter author: ";
                getline(cin, author);
                library.addBook(title, author);
                break;
            case 2:
                library.viewBooks();
                break;
            case 3:
                cout << "Enter book title to issue: ";
                cin >> ws;
                getline(cin, title);
                library.issueBook(title);
                break;
            case 4:
                cout << "Enter book title to return: ";
                cin >> ws;
                getline(cin, title);
                library.returnBook(title);
                break;
            case 5:
                library.viewIssuedBooks();
                break;
            case 6:
                cout << "Exiting system." << endl;
                break;
            default:
                cout << "Invalid choice." << endl;
        }
    } while (choice != 6);

    return 0;
}
