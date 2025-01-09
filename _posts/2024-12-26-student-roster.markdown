---
layout: post
title:  Student Roster
categories: code
comments: true
excerpt: "C++ application for storing and retrieving student details" 
---
This is a simple application that was good practice for learning the concepts of object oriented programming by developing classes and class methods. This application parses lists of student data and creates student objects which are stored in an array. The application defines "getter" and "setter" methods to manipulate student data.

<h2>Languages</h2>

- C++

<h2>Concepts</h2>

- Object oriented programming methods

<h2>Main Source File</h2>
{% highlight ruby %}

#include <iostream>
#include <string>
#include "degree.h"
#include "roster.h"
using namespace std;

int main() {

	const string studentData[] = {
		"A1,John,Smith,John1989@gm ail.com,20,30,35,40,SECURITY",
		"A2,Suzan,Erickson,Erickson_1990@gmailcom,19,50,30,40,NETWORK",
		"A3,Jack,Napoli,The_lawyer99yahoo.com,19,20,40,33,SOFTWARE",
		"A4,Erin,Black,Erin.black@comcast.net,22,50,58,40,SECURITY",
		"A5,Thomas,Kroll,tlkroll@pm.me,41,14,15,20,SOFTWARE"
	};

	const int numStudents = 5;
	Roster classRoster;

	for (int i = 0; i < numStudents; ++i) {
		classRoster.parse(studentData[i]);
	}

	cout << "Application created for C++ course" << endl << endl << "Student: Thomas Kroll" << endl <<
		"Student ID #: 999999" << endl << "Programming Language: C++ 14 " << endl << endl;

	cout << "All students in class roster:" << endl << endl;
	classRoster.printAll();
	cout << endl;

	cout << "Invalid emails:" << endl << endl;
	classRoster.printInvalidEmails();
	cout << endl;

	for(int i = 0; i < 5; ++i){
		string currStudent = classRoster.classRosterArray[i]->getID();
		cout << "Student " << currStudent << " has spent an average of ";
		classRoster.printAverageDaysInCourse(currStudent);
		cout << " days per class over three classes." << endl;
	}
	cout << endl;

	classRoster.printByDegreeProgram(SOFTWARE);
	cout << endl;

	classRoster.remove("A3");
	cout << endl;

	cout << "Updated class roster after removing A3:" << endl << endl;
	classRoster.printAll();
	cout << endl;

	cout << "Attempt to remove A3 after already removed:" << endl << endl;
	classRoster.remove("A3");
	cout << endl << endl;
}

{% endhighlight %}

<h2>Roster Class</h2>
{% highlight ruby %}

#pragma once
#include "roster.h"
#include "degree.h"
#include <iostream>
using namespace std;

void Roster::parse(string row) {
	int rhs = row.find(',');
	string sID = row.substr(0, rhs);

	int lhs = rhs + 1;
	rhs = row.find(',', lhs);
	string sFN = row.substr(lhs, rhs - lhs);

	lhs = rhs + 1;
	rhs = row.find(',', lhs);
	string sLN = row.substr(lhs, rhs - lhs);

	lhs = rhs + 1;
	rhs = row.find(',', lhs);
	string sEmail = row.substr(lhs, rhs - lhs);

	lhs = rhs + 1;
	rhs = row.find(',', lhs);
	int sAge = stoi(row.substr(lhs, rhs - lhs));

	lhs = rhs + 1;
	rhs = row.find(',', lhs);
	int days1 = stoi(row.substr(lhs, rhs - lhs));

	lhs = rhs + 1;
	rhs = row.find(',', lhs);
	int days2 = stoi(row.substr(lhs, rhs - lhs));

	lhs = rhs + 1;
	rhs = row.find(',', lhs);
	int days3 = stoi(row.substr(lhs, rhs - lhs));

	char lastChar = row.back();
	DegreeProgram degree = UNDECIDED;

	switch (lastChar)
	{
	case 'Y':
		degree = SECURITY;
		break;
	case 'K':
		degree = NETWORK;
		break;
	case 'E':
		degree = SOFTWARE;
		break;
	default:
		degree = UNDECIDED;
		break;
	}
	add(sID, sFN, sLN, sEmail, sAge, days1, days2, days3, degree);
}

void Roster::add(string studentID, string studentFirstName, string studentLastName, string studentEmail, int studentAge, 
	int days1, int days2, int days3, DegreeProgram degreeProgram) {

	int days[3] = { days1, days2, days3 };

	classRosterArray[++lastIndex] = new Student(studentID, studentFirstName, studentLastName, studentEmail, studentAge,
		days, degreeProgram);
}

void Roster::printAll() {
	for (int i = 0; i <= Roster::lastIndex; ++i) {
		classRosterArray[i]->print();
	}
}

void Roster::printByDegreeProgram(DegreeProgram degreeProgram) {
	cout << "Students pursuing " << programStrings[degreeProgram] << " degree:" << endl << endl;
	for (int i = 0; i <= Roster::lastIndex; ++i) {
		if (Roster::classRosterArray[i]->getProgram() == degreeProgram) {
			classRosterArray[i]->print();	
		}
	}
}

void Roster::printInvalidEmails() {
	bool invalidEntries = false;
	for (int i = 0; i <= Roster::lastIndex; ++i) {
		string sEmail = (classRosterArray[i]->getEmail());
		if ((sEmail.find('@') == string::npos || sEmail.find('.') == string::npos) || sEmail.find(' ') != string::npos) {
			invalidEntries = true;
			cout << sEmail << " is invalid" << endl;
		}
	}
	if (!invalidEntries) {
		cout << "No invalid entries" << endl;
	}
}

void Roster::printAverageDaysInCourse(string studentID) {
	for (int i = 0; i <= Roster::lastIndex; ++i) {
		if (classRosterArray[i]->getID() == studentID) {
			cout << (classRosterArray[i]->getDays()[0] + classRosterArray[i]->getDays()[1] + classRosterArray[i]->getDays()[2]) / 3;
		}
	}
}

void Roster::remove(string studentID) {
	bool studentFound = false;
	for (int i = 0; i <= Roster::lastIndex; ++i) {
		if (classRosterArray[i]->getID() == studentID) {
			studentFound = true;
			if (i < numStudents - 1) {
				Student* temp = classRosterArray[i];
				classRosterArray[i] = classRosterArray[numStudents - 1];
				classRosterArray[numStudents - 1] = temp;
			}
			Roster::lastIndex--;
			cout << studentID << " removed from roster." << endl;
		}
	}
	if (!studentFound) {
		cout << "Student ID not found." << endl;
	}
}

Roster::~Roster(){
	cout << "Destructor called - freeing memory..." << endl;
	for (int i = 0; i < numStudents; ++i) {
		cout << "Removing student " << i + 1 << endl;
		delete classRosterArray[i];
		classRosterArray[i] = nullptr;
	}
}

{% endhighlight %}

<h2>Student Class</h2>
{% highlight ruby %}


#pragma once
#include "student.h"
#include <iostream>
using namespace std;

Student::Student() {
	this->studentID = "";
	this->studentFirstName = "";
	this->studentLastName = "";
	this->studentEmail = "";
	this->studentAge = 0;
	for (int i = 0; i < gradesArraySize; ++i) {
		this->daysInCourse[i] = 0;
	}
	this->degreeProgram = DegreeProgram::UNDECIDED;
}

Student::Student(string studentID, string studentFirstName, string studentLastName, string studentEmail,
	int studentAge, int daysInCourse[], DegreeProgram degreeProgram) {
	this->studentID = studentID;
	this->studentFirstName = studentFirstName;
	this->studentLastName = studentLastName;
	this->studentEmail = studentEmail;
	this->studentAge = studentAge;
	for (int i = 0; i < gradesArraySize; ++i) {
		this->daysInCourse[i] = daysInCourse[i];
	}
	this->degreeProgram = degreeProgram;
}

//Student::~Student() {}

string Student::getID() { return this->studentID; }
string Student::getFirstName() { return this->studentFirstName; }
string Student::getLastName() { return this->studentLastName; }
string Student::getEmail() { return this->studentEmail; }
int Student::getAge() { return this->studentAge; }
int* Student::getDays() { return this->daysInCourse; }
DegreeProgram Student::getProgram() { return this->degreeProgram; }

void Student::setID(string ID) { this->studentID = ID; }
void Student::setFirstName(string firstName) { this->studentFirstName = firstName; }
void Student::setLastName(string lastName) { this->studentLastName = lastName; }
void Student::setEmail(string email) { this->studentEmail = email; }
void Student::setAge(int age) { this->studentAge = age; }
void Student::setDays(int days[]) {
	for (int i = 0; i < gradesArraySize; ++i) {
		this->daysInCourse[i] = days[i];
	}
}
void Student::setProgram(DegreeProgram program) { this->degreeProgram = program; }

void Student::print() {
	cout << this->getID() << "\t";
	cout << this->getFirstName() << " ";
	cout << this->getLastName() << "\t";
	cout << this->getEmail() << "\t";
	cout << this->getAge() << "\t";
	cout << this->getDays()[0] << ", "; 
	cout << this->getDays()[1] << ", ";
	cout << this->getDays()[2] << "\t";
	cout << programStrings[this->getProgram()] << "\n";
}


{% endhighlight %}

{% if page.comments %} 

<br><br>
<p>
<div id="disqus_thread"></div>
<script>
    (function() { 
        var d = document, s = d.createElement('script');
        
        s.src = 'https://tlkroll.disqus.com/embed.js';
        
        s.setAttribute('data-timestamp', +new Date());
        (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript" rel="nofollow">comments powered by Disqus.</a></noscript>
</p>

{% endif %} 