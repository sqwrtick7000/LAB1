#include <iostream>
#include <string>
#include <iomanip>

using namespace std;

enum ClassProfile { GENERAL, MATH, HUMANITIES, SCIENCE };

union PhoneData {
    long long rawNumber;
    char shortCode[8]; 
};

struct StudentFlags {
    unsigned int isMonitor : 1;  
    unsigned int hasMedal : 1;    
    unsigned int isAthlete : 1;   
};

struct Grades {
    int math;
    int physics;
    int russian;
    int literature;
};

struct Student {
    string surname;      
    string name;         
    string patronymic;   
    int gradeLevel;      
    PhoneData phone;     
    Grades grades;       
    ClassProfile profile;
    StudentFlags flags;  
};

void printHeader() {
    cout << "\n" << setw(3) << "ID" << " | "
         << setw(15) << "Фамилия" << " | "
         << setw(10) << "Имя" << " | "
         << setw(5) << "Кл." << " | "
         << setw(15) << "Номер тел." << " | "
         << setw(15) << "Профиль" << " | "
         << "М|Ф|Р|Л" << " | "
         << "Статус" << endl;
    cout << string(85, '-') << endl;
}

void printRow(Student* ptr, int id) {
    Student* s = ptr + id; 
    
    cout << setw(3) << id << " | "
         << setw(8) << (*s).surname << " | "
         << setw(7) << (*s).name << " | "
         << setw(3) << (*s).gradeLevel << " | "
         << setw(10) << (*s).phone.shortCode << " | " ;
         

    switch((*s).profile) {
        case MATH: cout << setw(15) << "Математик"; break;
        case HUMANITIES: cout << setw(15) << "Гуманитарий"; break;
        case SCIENCE: cout << setw(15) << "Естеств."; break;
        default: cout << setw(15) << "Общий"; break;
    }

    cout << " | " << (*s).grades.math << "|" << (*s).grades.physics << "|" 
         << (*s).grades.russian << "|" << (*s).grades.literature << " | "
         << ((*s).flags.isMonitor ? "Староста " : "")
         << ((*s).flags.hasMedal ? "Медаль " : "") << endl;
}

int main() {
    setlocale(LC_ALL, "RU");
    int count = 0;
    Student* school = nullptr; 
    int choice;

    while (true) {
        cout << "\n--- БАЗА ДАННЫХ: ШКОЛЬНИКИ ---\n"
             << "1. Добавить учеников\n"
             << "2. Вывести всех\n"
             << "3. Сортировка по фамилии\n"
             << "4. Поиск по фамилии\n"
             << "5. Редактировать данные\n"
             << "6. Удалить ученика\n"
             << "7. Выход\n"
             << "Ваш выбор: ";
        cin >> choice;

        if (choice == 7) {
            delete[] school;
            break;
        }

        switch (choice) {
            case 1: { 
                int n;
                cout << "Сколько учеников добавить? ";
                cin >> n;

                Student* temp = new Student[count + n];
                for (int i = 0; i < count; i++) *(temp + i) = *(school + i);
                
                delete[] school;
                school = temp;

                for (int i = count; i < count + n; i++) {
                    Student* s = school + i;
                    cout << "Фамилия: "; cin >> s->surname;
                    cout << "Имя: "; cin >> s->name;
                    do {
                        cout << "Класс (1-11): ";
                        cin >> s->gradeLevel;
                        if (s->gradeLevel < 1 || s->gradeLevel > 11) {
                            cout << "Ошибка! В школе только 11 классов. Повторите ввод.\n";
                        }
                    } while (s->gradeLevel < 1 || s->gradeLevel > 11);
                    
                    int p;
                    cout << "Профиль (0-Общий, 1-Мат, 2-Гум, 3-Ест): "; cin >> p;
                    s->profile = (ClassProfile)p;

                    cout << "Оценки (Мат Физ Рус Лит): ";
                    do{
                     cin >> s->grades.math >> s->grades.physics >> s->grades.russian >> s->grades.literature;
                     if (s->grades.math>10 || s->grades.physics>10 || s->grades.russian>10 || s->grades.literature>10)
                      cout<<"Введите верные оценки"<<endl;
                    } while (s->grades.math>10 || s->grades.physics>10 || s->grades.russian>10 || s->grades.literature>10);
                    cout << "Номер телефона (только цифры): "; cin >> s->phone.shortCode;

                    int f1, f2;
                    cout << "Староста? (1/0): "; cin >> f1;
                    cout << "Медалист? (1/0): "; cin >> f2;
                    s->flags.isMonitor = f1;
                    s->flags.hasMedal = f2;
                    s->flags.isAthlete = 0;
                }
                count += n;
                break;
            }

            case 2: {
                if (!school) cout << "База пуста.\n";
                else {
                    printHeader();
                    for (int i = 0; i < count; i++) printRow(school, i);
                }
                break;
            }

            case 3: { 
                for (int i = 0; i < count - 1; i++) {
                    for (int j = 0; j < count - i - 1; j++) {
                        if ((school + j)->surname > (school + j + 1)->surname) {
                            Student temp = *(school + j);
                            *(school + j) = *(school + j + 1);
                            *(school + j + 1) = temp;
                        }
                    }
                }
                cout << "Сортировка завершена.\n";
                break;
            }

            case 4: { 
                string target;
                cout << "Введите фамилию для поиска: "; cin >> target;
                printHeader();
                for (int i = 0; i < count; i++) {
                    if ((school + i)->surname == target) printRow(school, i);
                }
                break;
            }

            case 6: { 
                int id;
                cout << "Введите ID для удаления: "; cin >> id;
                if (id >= 0 && id < count) {
                    Student* temp = new Student[count - 1];
                    for (int i = 0, j = 0; i < count; i++) {
                        if (i != id) *(temp + (j++)) = *(school + i);
                    }
                    delete[] school;
                    school = temp;
                    count--;
                    cout << "Ученик исключен из базы.\n";
                }
                break;1:
            }
        }
    }
    return 0;
}
#include <iostream>
#include <string>
using namespace std;

int main() {
    setlocale(LC_ALL, "RU" );
    int n;
    cin >> n;

    string team1 = "", team2 = "";
    int goals1 = 0, goals2 = 0;
    for (int i = 0; i < n; i++) {
        string team;
        cin >> team;

        if (team1 == "") {
            team1 = team;
            goals1++;
        }
        else if (team == team1) {
            goals1++;
        }
        else if (team2 == "") {
            team2 = team;
            goals2++;
        }
        else {
            goals2++;
        }
    }
    if (goals1 > goals2) {
        cout << team1 << endl;
    }
    else {
        cout << team2 << endl;
    }

    return 0;
}