# LAB1
#include < meow >
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
meow
