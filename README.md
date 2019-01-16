#include "pch.h"
#include <iostream>
#include <conio.h>
#include <string>
#include <fstream>
#include <windows.h>
#include <vector>
#include <iterator>
#include <thread>
using namespace std;

string key = "123456";
string path = "Zametki.txt";
char * buffer = new char[256];

void BUFFER()
{
	fstream fs;
	fs.open("Code.txt", ios::in);
	while (!fs.eof())
	{
		fs.get(buffer, 256, '\n');
		fs >> *buffer;
	}
}

string key_Corrector(string key,const string msg)
{
	if (msg.length() > key.length()) {
		for (int i = 0; msg.length() != key.length(); i++) {
			key = key + key[i];
		}
	}
	return key;
}

void coderator(string key, string  msg)
{
	key = key_Corrector(key, msg);

	for (int i = 0; i < msg.length(); i++) 
	{
		int k = (int)key[i] - 48;
		int index = string(buffer).find(msg[i]) + k;
		if (index < string(buffer).size()) {
			msg[i] = buffer[index];
		}
		else {
			msg[i] = buffer[index - string(buffer).size()];
		}
	}
	
	ofstream fout;
	fout.open(path, ofstream::app);
	fout << msg << "\n";
	fout.close();
	
}

string uncoderator(string key, string msg)
{
	key = key_Corrector(key, msg);

	for (int i = 0; i < msg.length(); i++) {
		int k = (int)key[i] - 48;
		int index = string(buffer).find(msg[i]) - k;
		if (index >= 0) {
			msg[i] = buffer[index];
		}
		else {
			index = -index;
			msg[i] = buffer[string(buffer).size() - index];
		}
	}
	return msg;
}

int Menu() {
	system("cls");
	cout << "\t\t**ЗАМЕТКИ**\n\n";
	cout << "1. Добавить запись\n";
	cout << "2. Отобразить все записи(Расшифровать, удалить)\n";
	cout << "3. Выйти\n\n";
	cout << "Введите цифру (1-3): ";
	int choice;

	while (true)
	{
		while (!(cin >> choice) || (cin.peek() != '\n'))
		{
			cin.clear();
			while (cin.get() != '\n');
			cout << "Ошибка!" << endl;
			cout << "Введите цифру (1-3): ";
		}

		if (choice < 1 || choice > 3)
		{
			cout << "Вы должны ввести цифру от 1 до 5!" << endl;	
			cout << "Введите цифру (1-3): ";
		}
		else break;
	}

	return choice;
}

int Del()
{
	vector<string> vec;
	ifstream fin(path);
	string str;
	while (getline(fin, str)) vec.push_back(str);
	fin.close();
	int max_size = vec.size() - 1;

	int choice;
	while (true)
	{
		while (!(cin >> choice) || (cin.peek() != '\n'))
		{
			cin.clear();
			while (cin.get() != '\n');
			cout << "Ошибка!" << endl;
			cout << "Введи номер заметки: ";
		}
		
		if (choice > max_size)
		{
			cout << "Превышен диапозон!" << endl;
			cout << "Введи номер заметки: ";
			continue;
		}
		
		break;
	}
	return choice;
}

void remove_line(size_t index)
{
	vector<string> vec;
	ifstream fin(path);
	string str;
	while (getline(fin, str)) vec.push_back(str);
	fin.close();
	vec.erase(vec.begin() + index);
	ofstream fout(path);
	copy(vec.begin(), vec.end(),ostream_iterator<string>(fout, "\n"));
	fout.close();
}

void Print_Notes()
{
	ifstream fin;
	fin.open(path);
	vector<string> vec;  
	string msg;

	while (!fin.eof())
	{
		msg = "";
		getline(fin, msg);
		fin >> msg;
		vec.push_back(msg);
	}
	fin.close();
	for (int i = 0; i < vec.size() - 1; i++) cout << i + 1 << ")" << vec[i] << endl;

	cout << "\n\n";
	cout << "1. Расшифровать" << endl;
	cout << "2. Удалить запись" << endl;
	cout << "3. Вернуться назад" << endl;
	int choice;

	while (true)
	{
		cout << "Введите цифру (1-3): ";

		while (!(cin >> choice) || (cin.peek() != '\n'))
		{
			cin.clear();
			while (cin.get() != '\n');
			cout << "Ошибка!" << endl;
			cout << "Введите цифру (1-3): ";
		}

		if (choice == 1 || choice == 2 || choice == 3) break;

	}

	if (choice == 1)
	{
		system("cls");
		cout << "Расшифрованные заметки:\n\n";
		for (int i = 0; i < vec.size() - 1; i++)
		{
			cout << i+1 << ")" << uncoderator(key, vec[i]) << endl;
		}
	}

	if (choice == 2)
	{
		system("cls");
		for (int i = 0; i < vec.size() - 1; i++)
		{
			cout << i + 1 << ")" << vec[i] << endl;
		}

		cout << endl << endl << "Введи номер заметки, которую хочешь удалить: ";
		remove_line(Del());
		system("cls");
		cout << "Запись удалена!" << endl;
		this_thread::sleep_for(chrono::milliseconds(1000));
		return;
	}

	if (choice == 3) return;

	cout << "\nНажмите любую клавишу чтобы вернуться в клавное меню...\n";
	_getch();
}


bool Exit()
{
	system("cls");
	cout << "Вы точно хотите выйти?" << endl;
	cout << "1. Да" << endl;
	cout << "2. Нет" << endl << endl;
	int choice;

	while (true)
	{
		cout << "Введите цифру (1-2): ";

		while (!(cin >> choice) || (cin.peek() != '\n'))
		{
			cin.clear();
			while (cin.get() != '\n');
			cout << "Ошибка!" << endl;
			cout << "Введите цифру (1-2): ";	
		}

		if (choice == 1) break;
		if (choice == 2) break;
		else cout << "Вы должны ввести цифру от 1 до 2!" << endl;
		
	}

	system("cls");
	if (choice == 1) return true;
	else if (choice == 2) return false;
}

int main() {
	setlocale(0, "ru");
	SetConsoleCP(1251);
	SetConsoleOutputCP(1251);
	BUFFER();
	string msg;	

	for(;;)
	{	
		switch (Menu())
		{
		     case 1:
				 system("cls");
				 cin.clear();
				 while (cin.get() != '\n');
				 cout << "Введите запись:\n";
				 getline(cin, msg);
				 coderator(key, msg);
				 system("cls");
				 cout << "Запись добавлена!";
				 this_thread::sleep_for(chrono::seconds(1));
				 break;

			 case 2:
				system("cls");
			    cout << "Ваши записи:\n\n";
				Print_Notes();
				break;
			 
		     case 3:
				 if (Exit()) { delete[] buffer; return 0; }
			     break;
		}
	}
}
