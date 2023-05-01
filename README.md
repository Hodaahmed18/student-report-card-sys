#include<iostream>
#include<fstream>
#include<iomanip>
using namespace std;


//
//                   GEN_80 PROJECT S.R.C.S
//

class student
{
	int seat_no;
	char name[50];
	int p_marks, c_marks, m_marks, e_marks, cs_marks;
	double per;
	char grade;
	void calculate();	//function to calculate grade
public:
	void getdata();		//function to accept data from user
	void showdata() const;	//function to show data on screen
	void show_tabular() const;
	int retseat_no() const;
}; 


void student::calculate()
{
	per = (p_marks + c_marks + m_marks + e_marks + cs_marks) / 5.0;
	if (per >= 60)
		grade = 'A';
	else if (per >= 50)
		grade = 'B';
	else if (per >= 33)
		grade = 'C';
	else
		grade = 'F';
}

void student::getdata()
{
	cout << "\nEnter The seat number of student ";
	cin >> seat_no;
	cout << "\n\nEnter The Name of student ";
	cin.ignore();
	cin.getline(name, 50);
	cout << "\nEnter The marks in physics out of 100 : ";
	cin >> p_marks;
	cout << "\nEnter The marks in chemistry out of 100 : ";
	cin >> c_marks;
	cout << "\nEnter The marks in maths out of 100 : ";
	cin >> m_marks;
	cout << "\nEnter The marks in english out of 100 : ";
	cin >> e_marks;
	cout << "\nEnter The marks in computer science out of 100 : ";
	cin >> cs_marks;
	calculate();
}

void student::showdata() const
{
	cout << "\nSeat number of student : " << seat_no;
	cout << "\nName of student : " << name;
	cout << "\nMarks in Physics : " << p_marks;
	cout << "\nMarks in Chemistry : " << c_marks;
	cout << "\nMarks in Maths : " << m_marks;
	cout << "\nMarks in English : " << e_marks;
	cout << "\nMarks in Computer Science :" << cs_marks;
	cout << "\nPercentage of student is  :" << per;
	cout << "\nGrade of student is :" << grade;
}

void student::show_tabular() const
{
	cout << seat_no << setw(6) << " " << name << setw(10) << p_marks << setw(4) << c_marks << setw(4) << m_marks << setw(4)
		<< e_marks << setw(4) << cs_marks << setw(8) << per << setw(6) << grade << endl;
}

int  student::retseat_no() const
{
	return seat_no;
}


//
//    	function declaration
//

void write_student();	//write the record in file
void display_all();	//read all records from file
void display_sp(int);	//accept seatno and read record from file
void modify_student(int);	//accept seatno and update record of file
void delete_student(int);	//accept seatno and delete selected records from file
void class_result();	//display all records in a table from the file
void result();		//display result menu
void intro();		//display welcome screen
void entry_menu();	//display entry menu on screen


//
//    	THE MAIN FUNCTION OF PROGRAM
//


int main()
{
	char ch;
	cout.setf(ios::fixed | ios::showpoint);
	cout << setprecision(2); // program outputs decimal number to two decimal places
	intro();
	do
	{
		system("cls");
		cout << "\n\n\n\tMAIN MENU";
		cout << "\n\n\t01. RESULTS MENU";
		cout << "\n\n\t02. DATA ENTRY OR DATA EDIT MENU";
		cout << "\n\n\t03. EXIT";
		cout << "\n\n\tPlease Select Your Action (1-3) ";
		cin >> ch;
		switch (ch)
		{
		case '1': result();
			break;
		case '2': entry_menu();
			break;
		case '3':
			break;
		default:cout << "\a";
		}
	} while (ch != '3');
	return 0;
}

//
//    	function to write in file
//

void write_student()
{
	student st;
	ofstream outFile;
	outFile.open("student.dat", ios::binary | ios::app);
	st.getdata();
	outFile.write(reinterpret_cast<char*> (&st), sizeof(student));
	outFile.close();
	cout << "\n\nStudent record Has Been Created ";
	cin.ignore();
	cin.get();
}

//
//    	function to read all records from file
//

void display_all()
{
	student st;
	ifstream inFile;
	inFile.open("student.dat", ios::binary);
	if (!inFile)
	{
		cout << "File couldn't be opened!! Press any Key to continue...";
		cin.ignore();
		cin.get();
		return;
	}
	cout << "\n\n\n\t\tDISPLAY ALL OF THE RECORD!!!\n\n";
	while (inFile.read(reinterpret_cast<char*> (&st), sizeof(student)))
	{
		st.showdata();
		cout << "\n\n====================================\n";
	}
	inFile.close();
	cin.ignore();
	cin.get();
}

//
//    	function that reads a specific record from the file
//

void display_sp(int n)
{
	student st;
	ifstream inFile;
	inFile.open("student.dat", ios::binary);
	if (!inFile)
	{
		cout << "File couldn't be opened! Press any Key to continue...";
		cin.ignore();
		cin.get();
		return;
	}
	bool flag = false;
	while (inFile.read(reinterpret_cast<char*> (&st), sizeof(student)))
	{
		if (st.retseat_no() == n)
		{
			st.showdata();
			flag = true;
		}
	}
	inFile.close();
	if (flag == false)
		cout << "\n\nrecord does not exist";
	cin.ignore();
	cin.get();
}

//
//    	function that modifies record of file
//

void modify_student(int n)
{
	bool found = false;
	student st;
	fstream File;
	File.open("student.dat", ios::binary | ios::in | ios::out);
	if (!File)
	{
		cout << "File could not be open !! Press any Key...";
		cin.ignore();
		cin.get();
		return;
	}
	while (!File.eof() && found == false)
	{

		File.read(reinterpret_cast<char*> (&st), sizeof(student));
		if (st.retseat_no() == n)
		{
			st.showdata();
			cout << "\n\nPlease Enter The New Data of student" << endl;
			st.getdata();
			int pos = (-1) * static_cast<int>(sizeof(st));
			File.seekp(pos, ios::cur);
			File.write(reinterpret_cast<char*> (&st), sizeof(student));
			cout << "\n\n\t Record Updated";
			found = true;
		}
	}
	File.close();
	if (found == false)
		cout << "\n\n Record Not Found ";
	cin.ignore();
	cin.get();
}

//
//    	function that deletes the record of file
//

void delete_student(int n)
{
	student st;
	ifstream inFile;
	inFile.open("student.dat", ios::binary);
	if (!inFile)
	{
		cout << "File could not be open !! Press any Key...";
		cin.ignore();
		cin.get();
		return;
	}
	ofstream outFile;
	outFile.open("Temp.dat", ios::out);
	inFile.seekg(0, ios::beg);
	while (inFile.read(reinterpret_cast<char*> (&st), sizeof(student)))
	{
		if (st.retseat_no() != n)
		{
			outFile.write(reinterpret_cast<char*> (&st), sizeof(student));
		}
	}
	outFile.close();
	inFile.close();
	remove("student.dat");
	rename("Temp.dat", "student.dat");
	cout << "\n\n\tRecord Deleted ..";
	cin.ignore();
	cin.get();
}

//
//    	function to display all students grade report
//

void class_result()
{
	student st;
	ifstream inFile;
	inFile.open("student.dat", ios::binary);
	if (!inFile)
	{
		cout << "File could not be open !! Press any Key...";
		cin.ignore();
		cin.get();
		return;
	}
	cout << "\n\n\t\tALL STUDENTS RESULTS \n\n";
	cout << "==========================================================\n";
	cout << "ST.No |      Name       | P |  C | M | E | CS | Age |  Grade|" << endl;
	cout << "==========================================================\n";
	while (inFile.read(reinterpret_cast<char*> (&st), sizeof(student)))
	{
		st.show_tabular();
	}
	cin.ignore();
	cin.get();
	inFile.close();
}

//
//    	function to display result menu
//

void result()
{
	char ch;
	int stno;
	system("cls");
	cout << "\n\n\n\tRESULT MENU";
	cout << "\n\n\n\t1. Classes Result";
	cout << "\n\n\t2. Student's Report Card";
	cout << "\n\n\t3. Back to Main Menu";
	cout << "\n\n\n\tEnter Choice (1/2/3)? ";
	cin >> ch;
	system("cls");
	switch (ch)
	{
	case '1':	class_result(); break;
	case '2':	cout << "\n\n\tEnter Seat Number Of Student : "; cin >> stno;
		display_sp(stno); break;
	case '3':	break;
	default:	cout << "\a";
	}
}

//***************************************************************
//    	INTRODUCTION FUNCTION
//****************************************************************

void intro()
{
	cout << "\n\n\n\t\t  STUDENT";
	cout << "\n\n\t\tREPORT CARD";
	cout << "\n\n\t\t  SYSTEM";
	cout << "\n\n\n\tCREATED BY : GEN_80";
	
	cin.get();
}

//***************************************************************
//    	ENTRY / EDIT MENU FUNCTION
//****************************************************************

void entry_menu()
{
	char ch;
	int num;
	system("cls");
	cout << "\n\n\n\tENTRY MENU";
	cout << "\n\n\t1.CREATE A STUDENT RECORD";
	cout << "\n\n\t2.DISPLAY ALL THE STUDENTS RECORDS";
	cout << "\n\n\t3.SEARCH FOR A STUDENT RECORD ";
	cout << "\n\n\t4.MODIFY A STUDENT RECORD";
	cout << "\n\n\t5.DELETE A STUDENT RECORD";
	cout << "\n\n\t6.BACK TO MAIN MENU";
	cout << "\n\n\tPlease Enter Your Choice (1-6) ";
	cin >> ch;
	system("cls");
	switch (ch)
	{
	case '1':	write_student(); break;
	case '2':	display_all(); break;
	case '3':	cout << "\n\n\tPlease Enter The Seat number "; cin >> num;
		display_sp(num); break;
	case '4':	cout << "\n\n\tPlease Enter The Seat number "; cin >> num;
		modify_student(num); break;
	case '5':	cout << "\n\n\tPlease Enter The Seat number "; cin >> num;
		delete_student(num); break;
	case '6':	break;
	default:	cout << "\a"; entry_menu();
	}
}

//
//    			END OF CODE SEQUENCE
//
