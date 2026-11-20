CS121 Final project proposal 

## Purpose
This project is to show what I learned in CS121 including object oriented programming with inheritance, polymorphism, and encapsulation, an interface, an abstract class, a data structure I wrote myself (a linked list), and saving data with object serialization.

## Overview
Clinic manager is a text based program that runs a small doctor's office. It keeps track of patient, doctors, and appointments. A staff member logs in to add patients and doctors, book appointments, and view the schedule. A nurse logs in to check patients in when they arrive and add a note from the visit. A patience logs in to see their own appointments, book a new one, or cancel one. All the data is saved to a file when the program exits and loaded back when it starts, so nothing is lost between runs.i

## Intended users
**Staff**: the receptionist who manages the patient and doctor lists and the schedule.
**Nurse**: checks patients in and records a note for each visit.
**Patient**: a person who comes to the clinic. Can only see and change their own appointments.

## Problems it solves
- Keeps patient and doctor information in one place
- Lets staff see every appointment in order
- Lets the nurse mark who has arrived and what happened at the visit
- Lets patients book and cancel without calling the office
- Does not lose data when the program closes

## Technology
Java, command line only. Uses an interface (HasMenu), an abstract class (Person), an ArrayList for patients and doctors, a linked liust written by hand for appointments, and object serialization for saving. No outside libraries. Build with a makefile.

## Use case
```
Clinic Menu

0) Exit
1) Login as staff
2) Login as nurse
3) Login as patient

Action: 1
User name: staff
PIN: 0000
Login successful

Staff Menu
0) Exit this menu
1) List patients
2) List doctors
3) Add patient
4) Add doctor
5) Book appointment
6) View schedule

Action:5
Patient ID: 1
Doctor ID: 1
Date (mm/dd/yyyy): 12/01/2026
Reason: checkup
Appointment booked

Action: 6
Schedule
12/01/2026: Alice Smith with Dr.Jones - checkup

Action: 0

Clinic Menu

0) Exit
1) Login as staff
2) Login as nurse
3) Login as patient

Action: 2
User name: nurse
PIN: 0000
Login successful

Nurse Menu

0) Exit this menu
1) View schedule
2) Check in a patient
3) Add a note

Action: 2
Patient ID: 1
Date: 12/01/2026
Checked in

Action: 0

Clinic Menu

Action: 3
User name: Alice
PIN: 1111
Login successful

Patient Menu

0) Exit this menu
1) My appointments
2) Book appointment
3) Cancel appointment 

Action: 1
12/01/2026: Dr. Jones - checkup (checked in)
```

## Data design
**Person**: name, ID, PIN. Shared by everyone who logs in
**Patient**: a Person plus a date of birth
**Doctor**: a Person plus a specialty
**Nurse**: a Person with the nurse menu
**Staff**: a Person with the staff menu
**Appointment**: patientID, doctorID, date, reason, checked in flag, note
**AppointmentNode / AppointmentList**: a linked list of appointments so new ones can be added, found, and removed.
**Clinic**: owns an ArrayList of patients, and ArrayList of doctors, the AppointmentList, one Staff, and one Nurse. Saves and loads with serialization.

## UML
``` mermaid
classDiagram
direction TB
    class HasMenu {
        <<interface>>
        + String menu()
        + void start()
    }
    
    class Person {
        <<abstract>>
        # String name
        # int id
        # String PIN
        + boolean login()
        + boolean login(String name, String pin)
        + String getName()
        + int getId()
        + void setPIN(String pin)
        + String getReport()*
    }
    
    class Patient {
        # String birthDate
        + Patient(String name, int id, String pin, String birthDate)
        + String menu()
        + void start()
        + String getReport()
    }
    
    class Doctor {
        # String specialty
        + Doctor(String menu, int id, String pin, String specialty)
        + String menu()
        + void start()
        + String getReport()
    }

    class Nurse {
        + Staff()
        + String menu()
        + void start()
        + String getReport()
    }

    class Appointment {
        # int patientId
        # int doctocId
        # String date
        # String reason
        # boolean checkedIn
        # String note
        + Appointment(int patientId, int doctorId, String date, String reason)
        + int getPatientId()
        + int getDoctorId()
        + String getDate()
        + void setCheckedIn(boolean c)
        + void setNote(String n)
        + String toString()
    }

    class AppointmentNode {
        # Appointment data
        # AppointmentNode next
        + AppointmentNode(Appointment a)
    }

    class AppointmentList {
        # AppointmentNode head
        + void add(Appointment a)
        + void remove(int patientId, String date)
        + Appointment find(int patientId, String date)
        + void printAll()
        + void printForPatient(int patientId)
    }

    class Clinic {
        # Staff staff
        # Nurse nurse
        # ArrayList~Patient~ patients
        # ArrayList~Doctor~ doctors
        # Appointment List appointments
        + main()
        + Clinic()
        + String menu()
        + void start()
        + void startStaff()
        + void startNurse()
        + void loginAsPatient()
        + void addPatient()
        + void addDoctor()
        + void bookAppointment()
        + void checkIn()
        + void addNote()
        + void saveData()
        + void loadData()
    }

    HasMenu <|.. Person
    HasMenu <|.. Clinic
    Person <|-- Patient
    Person <|-- Doctor
    Person <|-- Nurse
    Person <|-- Staff
    Clinic o-- Staff
    Clinic o-- Nurse
    Clinic o-- Patient
    Clinic o-- Doctor
    Clinic o-- AppointmentList
    AppointmentList o-- AppointmentNode
    Appointment o-- Appoint
```

## OOP checklist
- Inheritance: Patient, Doctor, Staff, entend Person
- Polymorphism: getReport() and menu() are different in each subclass
- Encapsulation: all fields protected, getters and setters
- Abstraction: HasMenu interface, Person abstract class
- Aggregation: Clinic holds lists of the other classes
- Data structures: ArrayList and a linked list written by hand

## Milestones
1. Proposal and UML approved
2. HasMenu, Person, Patient - test Patient login
3. Doctor, Nurse, Staff
4. Appointment, AppointmentNode, AppointmentList - test add, find, print
5. Clinic with staff menu: add patient, add doctor, book, view
6. Nurse login and nurse menu: check in, add note
7. Patient login and patient menu
8. Save and load with serialization
9. Extras if time: cancel appointment, sor schedule by date, search by doctor

## Algorithm

### HasMenu
```
interface with two methods, no bodies
    String menu()
    void start()
```

### Person (abstract)

#### Person::login()
```
    ask for user name and PIN
    ok = login(name, pin)
    if ok print "Login successful" else print "Login failed"
    return ok
```

#### Person::login(name, pin)
```
    return true if name equals this name and pin equals this PIN
```

#### Person::setPIN
```
    if pin is 4 digits set PIN to it
    else print a message and set PIN to "0000"
```

#### Person::getReport
```
    abstract, each subclass writes its own
```

### Patient

#### Patient (constructor)
```
    name = name, id = id, PIN = pin, birthDate = birthDate
```

#### Patient::menu
```
    print "Patient Menu" and choices 0 to 3
    print "Action: "
    read a line and return it
```

#### Patient::start(appointments)
```
    keepGoing = true
    while keepGoing
        choice = menu()
        if "1" appointments.printForPatient(id)
        if "2" ask for doctor ID, date, reason
            make a new Appointment and appointment.add(it)
        if "3" ask for date
            appointment.remove(id, date)
        if "0" keepGoing = false
```

#### Patient::getReport
```
    return id, name, birthDate on 1 line
```

### Doctor

#### Doctor (constructor)
```
    name = name, id = id, PIN = pin, specialty = specialty
```

#### Doctor::menu / Doctor::start
```
    not used, left empty (doctors do not log in)
```

#### Doctor::getReport
```
    return id, "Dr." name, specialty on 1 line
```

### Nurse

#### Nurse (constructor)
```
    name = "nurse", id = 0, PIN = "0000"
```

#### Nurse::menu
```
    print "Nurse menu" and choices 0 to 3
    print "Action: "
    read a line and return it
```

#### Nurse::start
```
    empty, the Clinic runs the nurse menu in startNurse
```

#### Nurse::getReport
```
    return "Nurse: " and name
```

### Staff

#### Staff (constructor)
```
    name = "staff", id = 0, PIN = "0000"
```

#### Staff::menu
```
    print "Staff menu" and choices 0 to 6
    print "Action: "
    read a line and return it
```

#### Staff::start
```
    empty, the Clinic runs the staff menu in startStaff
```

#### Staff::getReport
```
    return "Staff: " and name
```

### Appointment

#### Appointment (constructor)
```
    patientId, doctorId, date, reason from the parameters
    checkIn = false
    note = ""
```

#### Appointment::toString
```
    build a string: date, patient ID, doctor ID, reason
    if checkedIn add "(checked in)"
    if note is not empty add " - " and the note
    reuturn it
```

### AppointmentNode

#### AppointmentNode (constructor)
```
    data = the appointment
    next = null
```

### AppointmentList

#### AppointmentList (constructor)
```
    head = null
```

#### ApppointmentList::add
```
    make a new node with the appointment
    if head is null
        head = new node
    else
        current = head
        while current.next is not null
            current = current.next
        current.next = new node
```

#### AppointmentList::find
```
    current = head
    while current is not null
        if current.data matches patientId and date
            return current.data
        current = current.next
    return null
```

#### AppointmentList::remove
```
    if head is null return
    if head.data matches patientId and date
        head = head.next
        return
    previous = head
    current = head.next
    while current is not null
        if current.data matches
            previous.next = current.next
            return
        previous = current
        current = current.next
```

#### AppointmentList::printAll
```
    current = head 
    while current is not null
        print current.data.toString()
        current = current.next
```

#### AppointmentList::printForPatient
```
    current = head 
    while current is not null
        if current.data.patientId equals patientId
            print current.data.toString()
        current = current.next
```

### Clinic

#### main
```
    make a new Clinic
```

#### Clinic (constructor)
```
    loadSampleData
    saveData
    loadData
    start
    save Data
```

#### Clinic::menu
```
    print "Clinic menu" and choices 0 to 3
    print "Action: "
    read a line and return it
```

#### Clinic::start
```
    keepGoing = true
    while keepGoing
        choice = menu()
        if "1" if staff.login() call startStaff
        if "2" if nurse.login() call startNurse
        if "3" call loginAsPatient
        if "0" keepGoing = false
```

#### Clinic::startStaff
```
    keepGoing = true
    while keepGoing
        choice = staff.menu()
        if "1" for each patient print getReport()
        if "2" for each doctor print getReport()
        if "3" call addPatient
        if "4" call addDoctor
        if "5" call bookAppointment
        if "6" appointments.printAll()
        if "0" keepGoing = false
```

#### Clinc::startNurse
```
    keepGoing = true
    while keepGoing
        choice = nurse.menu()
        if "1" appointments.printAll()
        if "2" call checkIn
        if "3" call addNote
        if "0" keepGoing = false
```

#### Clinic::loginAsPatient
```
    ask for name and pin
    found = false
    for each patient in patients
        if patient.login(name, pin)
            patient.start(appointments)
            found = true
        if not found print "No patient with that name and PIN"
```

#### Clinic::addPatient
```
    ask for name, PIN, birth date
    id = patient.size() + 1
    make a new Patient and add it to patients
```

#### Clinic::addDoctor
```
    ask for name, PIN, specialty
    id = doctors.size() + 1
    make a new Doctor and add it to doctors
```

#### Clinic::bookAppointment
```
    ask for patient ID, doctor ID, date, reason
    make a new Appointment
    appointment.add(it)
    print "Appointment booked"
```

#### Clinic::checkIn
```
    ask for patient ID and date
    a = appointments.find(patientId, date)
    if a is not null
        a.setCheckedIn(true)
        print "Checked in"
    else print "not found"
```

#### Clinic::addNote
```
    ask for patient ID and date
    a = appointments.find(patientId, date)
    if a is not null
        ask for the note
        a.setNote(note)
    else print "not found"
```

#### Clinic::loadSampleData
```
    make new empty patients, doctors, appointments
    add patient Alice(id 1, pin 1111)
    add patient Bob (id 2, pin 2222)
    add doctor Jones (id 1, cardiology)
    add doctor Lee (id 2, pediatrics)
    add one sample appointment for Alice with Dr. Jones
```

#### Clinc::saveData
```
    try
        open clinic.dat for output
        write patients, then doctors, then appointments
        close the file
    catch print the error
```

#### Clinic::loadData
```
    try
        open clinic.dat for input
        read patients, doctors, appointments and cast each one
        close the file
    catch
        print "Could not load, using sample data"
        call loadSampleData
```
