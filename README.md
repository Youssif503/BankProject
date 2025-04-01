#include <iostream>
#include <sstream>
#include <string>
#include <vector>
#include <fstream>
#include <iomanip>
using namespace std;

void ShowMenuList();
const string ClientsFileName = "FileClients.txt";
const char separator = '/';

struct ClientInfo
{
    string UserOFclient;
    string name;
    int PinCode;
    string Phone;
    double Accountbalance;
};

ClientInfo ConvertLineToRecord(string line) {
    ClientInfo MyClient;
    vector<string> vMyVector;
    string temp = "";

    for (int i = 0; i <line.length();i++)
    {
        if (line[i]!= separator) 
        {
            temp += line[i];
        }
        else 
        {
            vMyVector.push_back(temp);
            temp = "";
        }
    }
    vMyVector.push_back(temp);
    MyClient.UserOFclient = vMyVector[0];
    MyClient.name = vMyVector[1];
    MyClient.PinCode = stoi(vMyVector[2]);
    MyClient.Phone = vMyVector[3];
    MyClient.Accountbalance = stod(vMyVector[4]);

   
    return MyClient;
}

string ConvertRecordToLine(ClientInfo MyClient) {
    return MyClient.UserOFclient + separator + MyClient.name + separator +
        to_string(MyClient.PinCode) + separator + MyClient.Phone + separator +
        to_string(MyClient.Accountbalance);
}

bool checkUser(string name) {
    fstream MyFile(ClientsFileName, ios::in);
    string line;

    if (MyFile.is_open()) {
        while (getline(MyFile, line)) {
            string user = "";
            for (char c : line) {
                if (c != separator) {
                    user += c;
                }
                else if (user == name) {
                    MyFile.close();
                    return true;
                }
                else {
                    break;
                }
            }
        }
        MyFile.close();
    }
    return false;
}

bool checkLine(string UserAccount, string Line) {
    string test = "";
    for (char c : Line) {
        if (c != separator) {
            test += c;
        }
        else if (test == UserAccount) {
            return true;
        }
        else {
            break;
        }
    }
    return false;
}

void PrintClientINFO(string line) 
{
        ClientInfo vMyinfo = ConvertLineToRecord(line);
        if (vMyinfo.UserOFclient.empty()) {
            cerr << "Empty...." << line << endl;
            return;
        }
        cout << "===========================================\n";
        cout << "\tAccount Information\n";
        cout << "===========================================\n";
        cout << "| " << setw(8) << left << vMyinfo.UserOFclient
            << "| " << setw(15) << left << vMyinfo.name
            << "| " << setw(8) << left << vMyinfo.PinCode
            << "| " << setw(15) << left << vMyinfo.Phone
            << "| " << setw(12) << left << vMyinfo.Accountbalance << "\n\n";
}


void Find_Client() {
    system("cls");
    cout << "---------------------------------------\n";
    cout << "\t Find Client Screen \n";
    cout << "---------------------------------------\n";
    fstream MyFile(ClientsFileName, ios::in);
    string _UserClient, line;

    cout << "Enter User Account: ";
    cin >> _UserClient;

    if (MyFile.is_open()) {
        while (getline(MyFile, line)) {
            if (checkLine(_UserClient, line)) {
                PrintClientINFO(line);
            }
        }
        MyFile.close();
    }
}

void Show_Clients_List() 
{
    system("cls");
    cout << "---------------------------------------\n";
    cout << "\t Show Clients Screen \n";
    cout << "---------------------------------------\n";
    fstream MyFile(ClientsFileName, ios::in);

    string line;
    if (MyFile.is_open()) {
        while (getline(MyFile, line)) 
        {
            string ll = line;
            PrintClientINFO(ll);
        }
        MyFile.close();
        cout << "\n\n This is All Data In My File......";
        system("pause");
    }
    else {
        cout << "\n\n Check The File Mode......";
    }
}

void Add_New_Client() {
    system("cls");
    cout << "---------------------------------------\n";
    cout << "\t Add New Client Screen \n";
    cout << "---------------------------------------\n";
    fstream MyFile(ClientsFileName, ios::out | ios::app);
    ClientInfo NewClient;
    string _UserClient;

    cout << "Enter User Account: ";
    cin >> _UserClient;

    while (checkUser(_UserClient)) {
        cout << "\nSorry, User already exists..... try again\n";
        cout << "Enter User Account: ";
        cin >> _UserClient;
    }

    if (MyFile.is_open()) {
        cout << "Enter User Name: ";
        cin.ignore();
        getline(cin, NewClient.name);
        cout << "Enter a pin For Account: ";
        cin >> NewClient.PinCode;
        cout << "Enter A phone Number: ";
        cin >> NewClient.Phone;
        cout << "Enter Account Balance: ";
        cin >> NewClient.Accountbalance;

        MyFile << ConvertRecordToLine({ _UserClient, NewClient.name, NewClient.PinCode,
                                      NewClient.Phone, NewClient.Accountbalance }) << endl;
        MyFile.close();
        cout << "Data Was Entered Successfully......";
    }
    else {
        cout << "Check The File Mode..........\n";
    }

    char Res;
    cout << "\n\n Do You Want To Add More Clients? (y/n) ";
    cin >> Res;
    if (Res == 'y' || Res == 'Y') {
        Add_New_Client();
    }
}

void Delete_Client() {
    vector<string> vMyVector;
    fstream MyFile(ClientsFileName, ios::in);
    string line, ClientUser;

    if (MyFile.is_open()) {
        while (getline(MyFile, line)) {
            vMyVector.push_back(line);
        }
        MyFile.close();
    }

    cout << "\n\n Enter The Client User: ";
    cin >> ClientUser;

    for (string& vElement : vMyVector) {
        if (checkLine(ClientUser, vElement)) {
            PrintClientINFO(vElement);
            char choice;
            cout << "\n\n Are You Sure To Delete the account? (y/n): ";
            cin >> choice;
            if (choice == 'y' || choice == 'Y') {
                vElement = "";
            }
        }
    }

    MyFile.open(ClientsFileName, ios::out);
    if (MyFile.is_open()) {
        for (const string& Users : vMyVector) {
            if (!Users.empty()) {
                MyFile << Users << endl;
            }
        }
        MyFile.close();
    }
}

void Update_Client_Info() {
    vector<string> vMyVector;
    fstream MyFile(ClientsFileName, ios::in);
    string line, ClientUser;

    if (MyFile.is_open()) {
        while (getline(MyFile, line)) {
            vMyVector.push_back(line);
        }
        MyFile.close();
    }

    cout << "Enter The Client User: ";
    cin >> ClientUser;

    for (string& Line : vMyVector) {
        if (checkLine(ClientUser, Line)) {
            ClientInfo NewClient;
            NewClient.UserOFclient = ClientUser;
            cout << "Enter User Name: ";
            cin.ignore();
            getline(cin, NewClient.name);
            cout << "Enter a pin For Account: ";
            cin >> NewClient.PinCode;
            cout << "Enter A phone Number: ";
            cin >> NewClient.Phone;
            cout << "Enter Account Balance: ";
            cin >> NewClient.Accountbalance;
            Line = ConvertRecordToLine(NewClient);
            cout << "Data Was Entered Successfully......";
        }
    }

    MyFile.open(ClientsFileName, ios::out);
    if (MyFile.is_open()) {
        for (const string& Users : vMyVector) {
            MyFile << Users << endl;
        }
        MyFile.close();
    }
}

void addDeposit() {
    system("cls");
    cout << "---------------------------------------\n";
    cout << "\t Deposit Screen \n";
    cout << "---------------------------------------\n";
    string clientuser;
    fstream MyFile(ClientsFileName, ios::in);
    vector<string> vMyVector;

    cout << "Enter The USER (userName): ";
    cin >> clientuser;

    if (MyFile.is_open()) {
        string line;
        while (getline(MyFile, line)) {
            vMyVector.push_back(line);
        }
        MyFile.close();
    }

    for (string& vElement : vMyVector) {
        if (checkLine(clientuser, vElement)) {
            ClientInfo MyC = ConvertLineToRecord(vElement);
            int Deposit;
            cout << "\n Enter The Cash You need Added? ";
            cin >> Deposit;
            MyC.Accountbalance += Deposit;
            vElement = ConvertRecordToLine(MyC);
        }
    }

    MyFile.open(ClientsFileName, ios::out);
    if (MyFile.is_open()) {
        for (const string& Users : vMyVector) {
            MyFile << Users << endl;
        }
        MyFile.close();
    }
}

void Withdraw() {
    system("cls");
    cout << "---------------------------------------\n";
    cout << "\t WithDraw Screen \n";
    cout << "---------------------------------------\n";
    string clientuser;
    fstream MyFile(ClientsFileName, ios::in);
    vector<string> vMyVector;

    if (MyFile.is_open()) {
        string line;
        while (getline(MyFile, line)) {
            vMyVector.push_back(line);
        }
        MyFile.close();
    }

    cout << "Enter The USER (userName): ";
    cin >> clientuser;

    for (string& vElement : vMyVector) {
        if (checkLine(clientuser, vElement)) {
            ClientInfo MyC = ConvertLineToRecord(vElement);
            int withdraw;
            cout << "Enter The Cash You need to draw? ";
            cin >> withdraw;
            if (MyC.Accountbalance < withdraw) {
                cout << "Sorry, Your Balance is Not enough to Withdraw ";
            }
            else {
                MyC.Accountbalance -= withdraw;
                vElement = ConvertRecordToLine(MyC);
            }
        }
    }

    MyFile.open(ClientsFileName, ios::out);
    if (MyFile.is_open()) {
        for (const string& Users : vMyVector) {
            MyFile << Users << endl;
        }
        MyFile.close();
    }
}

void TotalBalances() {
    // To be implemented
    cout << "Total Balances functionality not implemented yet.\n";
}

void TransActionList() {
    system("cls");
    int num;
    cout << "===========================================\n";
    cout << "\t\tTransaction Menu Screen\n";
    cout << "===========================================\n";
    cout << "\t[1] Deposit.\n";
    cout << "\t[2] Withdraw.\n";
    cout << "\t[3] Total Balances.\n";
    cout << "\t[4] Main screen.\n";
    cout << "===========================================\n";
    cout << "What Do You Want? ";
    cin >> num;

    switch (num) {
    case 1:
        addDeposit();
        break;
    case 2:
        Withdraw();
        break;
    case 3:
        TotalBalances();
        break;
    case 4:
        ShowMenuList();
        break;
    default:
        char res;
        cout << "Number doesn't exist......\n\n";
        cout << "Do You Want To Try Again? press (y/n) ";
        cin >> res;
        if (res == 'y' || res == 'Y') {
            TransActionList();
        }
        break;
    }
}

void ShowMenuList() {
    system("cls");
    int num;
    cout << "===========================================\n";
    cout << "\t\tMain Menu Screen\n";
    cout << "===========================================\n";
    cout << "\t[1] Show Client List.\n";
    cout << "\t[2] Add New Client.\n";
    cout << "\t[3] Delete Client.\n";
    cout << "\t[4] Update Client Info.\n";
    cout << "\t[5] Find Client.\n";
    cout << "\t[6] Transaction.\n";
    cout << "\t[7] Exit.\n";
    cout << "===========================================\n";
    cout << "What Do You Want? ";
    cin >> num;

    switch (num) {
    case 1:
        Show_Clients_List();
        break;
    case 2:
        Add_New_Client();
        break;
    case 3:
        Delete_Client();
        break;
    case 4:
        Update_Client_Info();
        break;
    case 5:
        Find_Client();
        break;
    case 6:
        TransActionList();
        break;
    case 7:
        system("cls");
        break;
    default:
        char res;
        cout << "Number doesn't exist......\n\n";
        cout << "Do You Want To Try Again? press (y/n) ";
        cin >> res;
        if (res == 'y' || res == 'Y') {
            ShowMenuList();
        }
        break;
    }
}

int main()
{
    ShowMenuList();

}
