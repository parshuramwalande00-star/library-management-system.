# library-management-system
#include <iostream>
#include <vector>
#include <memory>
#include <fstream>
#include <string>
using namespace std;

// Base Class
class MediaItem {
protected:
    int id;
    string title;
    bool checkedOut;

public:
    MediaItem(int i, string t)
        : id(i), title(t), checkedOut(false) {}

    virtual ~MediaItem() {}

    int getId() const {
        return id;
    }

    string getTitle() const {
        return title;
    }

    bool isCheckedOut() const {
        return checkedOut;
    }

    void checkout() {
        checkedOut = true;
    }

    void returnItem() {
        checkedOut = false;
    }

    // Virtual functions demonstrate polymorphism
    virtual void display() const = 0;

    virtual int getLoanDays() const = 0;

    virtual double calculateFine(int overdueDays) const = 0;
};


// Derived Class: Book
class Book : public MediaItem {
private:
    string author;

public:
    Book(int i, string t, string a)
        : MediaItem(i, t), author(a) {}

    void display() const override {
        cout << "\nType: Book";
        cout << "\nID: " << id;
        cout << "\nTitle: " << title;
        cout << "\nAuthor: " << author;
        cout << "\nStatus: "
             << (checkedOut ? "Checked Out" : "Available")
             << endl;
    }

    int getLoanDays() const override {
        return 14;
    }

    double calculateFine(int overdueDays) const override {
        return overdueDays * 2.0;
    }
};


// Derived Class: Journal
class Journal : public MediaItem {
private:
    string issue;

public:
    Journal(int i, string t, string is)
        : MediaItem(i, t), issue(is) {}

    void display() const override {
        cout << "\nType: Journal";
        cout << "\nID: " << id;
        cout << "\nTitle: " << title;
        cout << "\nIssue: " << issue;
        cout << "\nStatus: "
             << (checkedOut ? "Checked Out" : "Available")
             << endl;
    }

    int getLoanDays() const override {
        return 7;
    }

    double calculateFine(int overdueDays) const override {
        return overdueDays * 5.0;
    }
};


// Library Class
class Library {
private:
    vector<unique_ptr<MediaItem>> catalog;

public:

    // Add items
    void addItems() {
        catalog.push_back(
            make_unique<Book>(
                101,
                "C++ Programming",
                "Bjarne Stroustrup"
            )
        );

        catalog.push_back(
            make_unique<Book>(
                102,
                "Data Structures",
                "Mark Allen"
            )
        );

        catalog.push_back(
            make_unique<Journal>(
                201,
                "IEEE Computer Journal",
                "Vol-10"
            )
        );

        catalog.push_back(
            make_unique<Journal>(
                202,
                "Electrical Engineering Journal",
                "Vol-5"
            )
        );
    }

    // Display catalog
    void displayCatalog() const {
        cout << "\n========== LIBRARY CATALOG ==========\n";

        for (const auto& item : catalog) {
            item->display();
            cout << "------------------------------------\n";
        }
    }

    // Find item
    MediaItem* findItem(int id) {
        for (auto& item : catalog) {
            if (item->getId() == id)
                return item.get();
        }

        return nullptr;
    }

    // Checkout
    void checkoutItem() {
        int id;

        cout << "\nEnter Item ID to checkout: ";
        cin >> id;

        MediaItem* item = findItem(id);

        if (item == nullptr) {
            cout << "Item not found!\n";
            return;
        }

        if (item->isCheckedOut()) {
            cout << "Item is already checked out!\n";
            return;
        }

        item->checkout();

        cout << "Item checked out successfully!\n";
        cout << "Loan period: "
             << item->getLoanDays()
             << " days.\n";
    }

    // Return item and calculate fine
    void returnItem() {
        int id;
        int overdueDays;

        cout << "\nEnter Item ID to return: ";
        cin >> id;

        MediaItem* item = findItem(id);

        if (item == nullptr) {
            cout << "Item not found!\n";
            return;
        }

        if (!item->isCheckedOut()) {
            cout << "Item is not currently checked out.\n";
            return;
        }

        cout << "Enter number of overdue days: ";
        cin >> overdueDays;

        if (overdueDays < 0)
            overdueDays = 0;

        double fine = item->calculateFine(overdueDays);

        item->returnItem();

        cout << "\nItem returned successfully!\n";
        cout << "Fine: Rs. " << fine << endl;
    }

    // Save catalog to file
    void saveToFile() const {
        ofstream file("library.txt");

        if (!file) {
            cout << "Error opening file!\n";
            return;
        }

        for (const auto& item : catalog) {
            file << item->getId() << "|"
                 << item->getTitle() << "|"
                 << item->isCheckedOut() << "\n";
        }

        file.close();

        cout << "Library catalog saved to file.\n";
    }
};


int main() {

    Library library;

    // Add sample books and journals
    library.addItems();

    int choice;

    do {
        cout << "\n====================================\n";
        cout << "       LIBRARY MANAGEMENT SYSTEM\n";
        cout << "====================================\n";
        cout << "1. Display Library Catalog\n";
        cout << "2. Checkout Item\n";
        cout << "3. Return Item\n";
        cout << "4. Save Catalog\n";
        cout << "5. Exit\n";
        cout << "====================================\n";

        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {

        case 1:
            library.displayCatalog();
            break;

        case 2:
            library.checkoutItem();
            break;

        case 3:
            library.returnItem();
            break;

        case 4:
            library.saveToFile();
            break;

        case 5:
            library.saveToFile();
            cout << "\nThank you for using the Library Management System!\n";
            break;

        default:
            cout << "Invalid choice! Try again.\n";
        }

    } while (choice != 5);

    return 0;
}