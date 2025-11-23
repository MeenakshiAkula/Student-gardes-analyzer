# Student-gardes-analyzer
The Student Grades Analyzer is a C++ mini–project built using Data Structures &amp; Algorithms (DSA) concepts to efficiently process and evaluate student performance. It allows users to input student details, calculate grades, analyze overall performance, and display results in a structured format.
#include <iostream>
#include <vector>
#include <map>
#include <algorithm>
#include <iomanip>
using namespace std;

struct Subject {
    string name;
    int marks;
    int credits;
    string grade;
    int gradePoint;
};

map<string, int> gradeMap = {
    {"O", 10}, {"A+", 9}, {"A", 8}, {"B+", 7},
    {"B", 6}, {"C", 5}, {"F", 0}
};

string getGrade(int marks) {
    if (marks >= 91) return "O";
    else if (marks >= 81) return "A+";
    else if (marks >= 71) return "A";
    else if (marks >= 61) return "B+";
    else if (marks >= 51) return "B";
    else if (marks >= 41) return "C";
    else return "F";
}

struct Student {
    int roll_no;
    string name;
    vector<Subject> subjects;

    double averageGradePoint() const {
        if (subjects.empty()) return 0.0;
        double totalPoints = 0, totalCredits = 0;
        for (const Subject& sub : subjects) {
            totalPoints += sub.gradePoint * sub.credits;
            totalCredits += sub.credits;
        }
        return totalCredits > 0 ? totalPoints / totalCredits : 0.0;
    }
};

map<int, Student> students;

void addStudent() {
    Student s;
    cout << "Enter roll number: ";
    cin >> s.roll_no;
    if (students.count(s.roll_no)) {
        cout << "Student with this roll number already exists.\n";
        return;
    }
    cout << "Enter name: ";
    cin.ignore();
    getline(cin, s.name);
    int n;
    cout << "Enter number of subjects: ";
    cin >> n;
    s.subjects.resize(n);
    for (int i = 0; i < n; ++i) {
        Subject sub;
        cout << "Subject " << i + 1 << " name: ";
        cin.ignore();
        getline(cin, sub.name);
        cout << "Enter marks (0-100): ";
        cin >> sub.marks;
        cout << "Enter credits: ";
        cin >> sub.credits;
        sub.grade = getGrade(sub.marks);
        sub.gradePoint = gradeMap[sub.grade];
        s.subjects[i] = sub;
    }
    students[s.roll_no] = s;
    cout << "Student added successfully.\n";
}

void updateGrades() {
    int roll;
    cout << "Enter roll number: ";
    cin >> roll;
    if (!students.count(roll)) {
        cout << "Student not found.\n";
        return;
    }
    int n;
    cout << "Enter new number of subjects: ";
    cin >> n;
    students[roll].subjects.resize(n);
    for (int i = 0; i < n; ++i) {
        Subject sub;
        cout << "Subject " << i + 1 << " name: ";
        cin.ignore();
        getline(cin, sub.name);
        cout << "Enter marks (0-100): ";
        cin >> sub.marks;
        cout << "Enter credits: ";
        cin >> sub.credits;
        sub.grade = getGrade(sub.marks);
        sub.gradePoint = gradeMap[sub.grade];
        students[roll].subjects[i] = sub;
    }
    cout << "Grades updated.\n";
}

void deleteStudent() {
    int roll;
    cout << "Enter roll number: ";
    cin >> roll;
    if (students.erase(roll))
        cout << "Student deleted.\n";
    else
        cout << "Student not found.\n";
}

void searchStudent() {
    int roll;
    cout << "Enter roll number: ";
    cin >> roll;
    if (!students.count(roll)) {
        cout << "Student not found.\n";
        return;
    }
    Student& s = students[roll];
    cout << "Roll No: " << s.roll_no << "\nName: " << s.name << "\n";
    cout << "Subjects:\n";
    cout << setw(15) << "Subject" << setw(10) << "Marks" << setw(10) << "Credit"
         << setw(10) << "Grade" << setw(15) << "Grade Point\n";
    for (const Subject& sub : s.subjects) {
        cout << setw(15) << sub.name << setw(10) << sub.marks
             << setw(10) << sub.credits << setw(10) << sub.grade
             << setw(15) << sub.gradePoint << "\n";
    }
    cout << fixed << setprecision(2);
    cout << "Average Grade Point: " << s.averageGradePoint() << "\n";
}

void displayAll() {
    if (students.empty()) {
        cout << "No students to display.\n";
        return;
    }

    vector<Student> all;
    for (auto& p : students) all.push_back(p.second);

    sort(all.begin(), all.end(), [](const Student& a, const Student& b) {
        return a.name < b.name;
    });

    cout << "\n--- All Students Details ---\n";
    for (const Student& s : all) {
        cout << "\nRoll No: " << s.roll_no << ", Name: " << s.name << "\n";
        cout << setw(15) << "Subject" << setw(10) << "Marks"
             << setw(10) << "Credit" << setw(10) << "Grade"
             << setw(15) << "Grade Point\n";
        for (const Subject& sub : s.subjects) {
            cout << setw(15) << sub.name << setw(10) << sub.marks
                 << setw(10) << sub.credits << setw(10) << sub.grade
                 << setw(15) << sub.gradePoint << "\n";
        }
        cout << fixed << setprecision(2);
        cout << "Average Grade Point: " << s.averageGradePoint() << "\n";
    }
}


void showStatistics() {
    if (students.empty()) {
        cout << "No students available.\n";
        return;
    }
    double total = 0, highest = -1e9, lowest = 1e9;
    for (auto& p : students) {
        double avg = p.second.averageGradePoint();
        total += avg;
        highest = max(highest, avg);
        lowest = min(lowest, avg);
    }
    cout << fixed << setprecision(2);
    cout << "Average of averages: " << total / students.size() << "\n";
    cout << "Highest average: " << highest << "\n";
    cout << "Lowest average: " << lowest << "\n";
}

void showMenu() {
    cout << "\n--- Student Grades Analyzer ---\n";
    cout << "1. Add Student\n";
    cout << "2. Update Student Grades\n";
    cout << "3. Delete Student\n";
    cout << "4. Search Student\n";
    cout << "5. Display All Students\n";
    cout << "6. Show Statistics\n";
    cout << "7. Exit\n";
    cout << "Enter your choice: ";
}

int main() {
    int choice;
    do {
        showMenu();
        cin >> choice;
        switch (choice) {
            case 1: addStudent(); break;
            case 2: updateGrades(); break;
            case 3: deleteStudent(); break;
            case 4: searchStudent(); break;
            case 5: displayAll(); break;
            case 6: showStatistics(); break;
            case 7: cout << "Exiting...\n"; break;
            default: cout << "Invalid choice. Try again.\n";
        }
    } while (choice != 7);
    return 0;
}
