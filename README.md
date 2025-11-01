#include <iostream>
#include <string>
#include <vector>
#include <cctype>
#include <iomanip>
#include <limits>

enum GradeLevel { FRESHMAN = 1, SOPHOMORE, JUNIOR, SENIOR };
typedef double GPA_t;
using StudentName = std::string;

struct StudentRecord {
    StudentName nameOriginal;
    StudentName nameFormatted;
    GradeLevel grade;
    GPA_t gpa;
};

namespace StudentUtils {
    void formatName(std::string &name) {
        for (char &ch : name)
            ch = std::toupper(static_cast<unsigned char>(ch));
    }

    std::string gradeToString(GradeLevel level) {
        switch (level) {
            case FRESHMAN: return "Freshman";
            case SOPHOMORE: return "Sophomore";
            case JUNIOR: return "Junior";
            case SENIOR: return "Senior";
            default: return "Unknown";
        }
    }

    void displayStudentInfo(const StudentRecord &student) {
        std::cout << "Student Record:\n";
        std::cout << "Name: " << student.nameFormatted << "\n";
        std::cout << "Grade Level: " << gradeToString(student.grade) << "\n";
        std::cout << "GPA: " << std::fixed << std::setprecision(2) << student.gpa << "\n";
        std::cout << "-----------------------------\n";
    }
}

int getIntInRange(const std::string &prompt, int minVal, int maxVal) {
    int value;
    while (true) {
        std::cout << prompt;
        if (std::cin >> value && value >= minVal && value <= maxVal)
            return value;
        std::cout << "Invalid choice. Please enter a number between " 
                  << minVal << " and " << maxVal << ".\n";
        std::cin.clear();
        std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
    }
}

GPA_t getValidGPA(const std::string &prompt) {
    GPA_t g;
    while (true) {
        std::cout << prompt;
        if (std::cin >> g && g >= 0.0 && g <= 4.0)
            return g;
        std::cout << "GPA must be between 0.00 and 4.00.\n";
        std::cin.clear();
        std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
    }
}

int main() {
    std::vector<StudentRecord> records;
    bool keepGoing = true;

    std::cout << "==== Student Records Manager ====\n";

    while (keepGoing) {
        StudentRecord current;
        std::cout << "Enter student name: ";
        std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
        std::getline(std::cin, current.nameOriginal);
        current.nameFormatted = current.nameOriginal;
        StudentUtils::formatName(current.nameFormatted);

        int gradeChoice = getIntInRange("Enter grade level (1=Freshman, 2=Sophomore, 3=Junior, 4=Senior): ", 1, 4);
        current.grade = static_cast<GradeLevel>(gradeChoice);
        current.gpa = getValidGPA("Enter GPA (0.00 - 4.00): ");

        records.push_back(current);

        std::cout << "\nRecord Saved.\n";
        StudentUtils::displayStudentInfo(current);

        int more = getIntInRange("Add another student? (1=Yes, 2=No): ", 1, 2);
        keepGoing = (more == 1);
    }

    if (!records.empty()) {
        for (size_t i = 0; i < records.size(); ++i)
            for (size_t j = i + 1; j < records.size(); ++j)
                if (records[j].nameFormatted < records[i].nameFormatted)
                    std::swap(records[i], records[j]);

        std::cout << "\n===== All Student Records (Alphabetical) =====\n";
        for (const auto &stu : records)
            StudentUtils::displayStudentInfo(stu);
    } else {
        std::cout << "\nNo student records entered.\n";
    }

    std::cout << "Goodbye.\n";
    return 0;
}
