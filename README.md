# Employee-Management-System
Employee Management System in c++
#include <iostream>
#include <fstream>
#include <string>
using namespace std;

struct Employee {
    int id;
    string name;
    double salary;
    Employee* next;
};

void insertRecord(Employee*& head);
void searchRecord(Employee* head);
void editRecord(Employee* head);
void deleteRecord(Employee*& head);
void countEmployees(Employee* head);
void showAllRecords(Employee* head);
void saveToFile(Employee* head);
void loadFromFile(Employee*& head);
bool signup();
bool login();

int main() {
    Employee* head = NULL; 

    while (true) {
        cout << "Welcome to the Employee Management System\n";
        cout << "1. Login\n";
        cout << "2. Signup\n";
        cout << "3. Exit\n";
        cout << "Enter your choice: ";
        int choice;
        cin >> choice;

        if (choice == 1) {
            if (login()) {
                break; 
            } else {
                cout << "Login failed! Please try again.\n";
            }
        } else if (choice == 2) {
            if (signup()) {
                cout << "Signup successful! You can now log in.\n";
            } else {
                cout << "Signup failed! Username might already be taken.\n";
            }
        } else if (choice == 3) {
            return 0; 
        } else {
            cout << "Invalid choice! Try again.\n";
        }
    }

    loadFromFile(head);

    int choice;
    do {
        cout << "\nEmployee Management System\n";
        cout << "1. Insert Record\n";
        cout << "2. Search Record\n";
        cout << "3. Edit Record\n";
        cout << "4. Delete Record\n";
        cout << "5. Count Employees\n";
        cout << "6. Show All Records\n";
        cout << "7. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {
            case 1: insertRecord(head); break;
            case 2: searchRecord(head); break;
            case 3: editRecord(head); break;
            case 4: deleteRecord(head); break;
            case 5: countEmployees(head); break;
            case 6: showAllRecords(head); break;
            case 7: saveToFile(head); cout << "Exiting... Records saved.\n"; break;
            default: cout << "Invalid choice! Try again.\n";
        }
    } while (choice != 7);

    return 0;
}

bool signup() {
    string username, password;
    cout << "Enter a username: ";
    cin >> username;
    cout << "Enter a password: ";
    cin >> password;

    ifstream userFile("emps.txt");
    string line;
    while (getline(userFile, line)) {
        if (line == username) {
            userFile.close();
            return false; 
        }
        getline(userFile, line); 
    }
    userFile.close();

    ofstream userFileOut("emps.txt", ios::app);
    userFileOut << username << "\n" << password << "\n";
    userFileOut.close();
    return true;
}

bool login() {
    string username, password;
    cout << "Enter your username: ";
    cin >> username;
    cout << "Enter your password: ";
    cin >> password;

    ifstream userFile("emps.txt");
    string line;
    while (getline(userFile, line)) {
        if (line == username) {
            getline(userFile, line); 
            if (line == password) {
                userFile.close();
                return true; 
            } else {
                userFile.close();
                return false;
            }
        }
        getline(userFile, line);
    }
    userFile.close();
    return false; 
}

void insertRecord(Employee*& head) {
    Employee* newEmployee = new Employee;
    cout << "Enter Employee ID: ";
    cin >> newEmployee->id;
    cin.ignore();
    cout << "Enter Employee Name: ";
    getline(cin, newEmployee->name);
    cout << "Enter Employee Salary: ";
    cin >> newEmployee->salary;

    newEmployee->next = head;
    head = newEmployee;

    cout << "Record added successfully!\n";
}

void searchRecord(Employee* head) {
    int id;
    cout << "Enter Employee ID to search: ";
    cin >> id;

    Employee* temp = head;
    while (temp) {
        if (temp->id == id) {
            cout << "Employee Found!\n";
            cout << "ID: " << temp->id << ", Name: " << temp->name << ", Salary: " << temp->salary << endl;
            return;
        }
        temp = temp->next;
    }
    cout << "Employee not found.\n";
}

void editRecord(Employee* head) {
    int id;
    cout << "Enter Employee ID to edit: ";
    cin >> id;

    Employee* temp = head;
    while (temp) {
        if (temp->id == id) {
            cin.ignore();
            cout << "Enter new Name: ";
            getline(cin, temp->name);
            cout << "Enter new Salary: ";
            cin >> temp->salary;
            cout << "Record updated successfully!\n";
            return;
        }
        temp = temp->next;
    }
    cout << "Employee not found.\n";
}

void deleteRecord(Employee*& head) {
    int id;
    cout << "Enter Employee ID to delete: ";
    cin >> id;

    Employee* temp = head;
    Employee* prev = NULL;

    while (temp) {
        if (temp->id == id) {
            if (prev) {
                prev->next = temp->next;
            } else {
                head = temp->next;
            }
            delete temp;
            cout << "Record deleted successfully!\n";
            return;
        }
        prev = temp;
        temp = temp->next;
    }
    cout << "Employee not found.\n";
}

void countEmployees(Employee* head) {
    int count = 0;
    Employee* temp = head;

    while (temp) {
        count++;
        temp = temp->next;
    }
    cout << "Total Employees: " << count << endl;
}

void showAllRecords(Employee* head) {
    if (!head) {
        cout << "No records found.\n";
        return;
    }

    Employee* temp = head;
    cout << "Employee Records:\n";
    while (temp) {
        cout << "ID: " << temp->id << ", Name: " << temp->name << ", Salary: " << temp->salary << endl;
        temp = temp->next;
    }
}

void saveToFile(Employee* head) {
    ofstream file("employees.txt");
    if (!file) {
        cout << "Error saving records to file.\n";
        return;
    }

    Employee* temp = head;
    while (temp) {
        file << temp->id << "\n" << temp->name << "\n" << temp->salary << "\n";
        temp = temp->next;
    }
    file.close();
}

void loadFromFile(Employee*& head) {
    ifstream file("employees.txt");
    if (!file) {
        return;
    }

    while (!file.eof()) {
        Employee* newEmployee = new Employee;
        file >> newEmployee->id;
        file.ignore();
        getline(file, newEmployee->name);
        file >> newEmployee->salary;
        file.ignore();

        if (file.fail()) {
            delete newEmployee;
            break;
        }

        newEmployee->next = head;
        head = newEmployee;
    }
    file.close();
}
