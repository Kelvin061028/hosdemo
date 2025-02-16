import java.time.LocalDate;
import java.time.LocalTime;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

// Staff superclass
abstract class Staff {
    protected String id;
    protected String name;

    public Staff(String id, String name) {
        this.id = id;
        this.name = name;
    }

    // Getters
    public String getId() { return id; }
    public String getName() { return name; }
}

// Doctor subclass
class Doctor extends Staff {
    private String specialization;
    private int yearsExperience;
    private List<Patient> patients;

    public Doctor(String id, String name, String specialization, int yearsExperience) {
        super(id, name);
        this.specialization = specialization;
        this.yearsExperience = yearsExperience;
        this.patients = new ArrayList<>();
    }

    public void addPatient(Patient patient) {
        if (!patients.contains(patient)) {
            patients.add(patient);
        }
    }

    public List<Patient> getPatients() { return patients; }
    public String getSpecialization() { return specialization; }
    public int getYearsExperience() { return yearsExperience; }
}

// Nurse subclass
class Nurse extends Staff {
    private String assignedTo;

    public Nurse(String id, String name, String assignedTo) {
        super(id, name);
        this.assignedTo = assignedTo;
    }

    public String getAssignedTo() { return assignedTo; }
}

// Patient class
class Patient {
    private String id;
    private String name;
    private String address;
    private String phone;
    private String emergencyContact;
    private MedicalRecord medicalRecord;
    private List<Doctor> doctors;
    private List<Appointment> appointments;

    public Patient(String id, String name, String address, String phone, String emergencyContact) {
        this.id = id;
        this.name = name;
        this.address = address;
        this.phone = phone;
        this.emergencyContact = emergencyContact;
        this.medicalRecord = new MedicalRecord(this);
        this.doctors = new ArrayList<>();
        this.appointments = new ArrayList<>();
    }

    public void addDoctor(Doctor doctor) {
        if (!doctors.contains(doctor)) {
            doctors.add(doctor);
        }
    }

    public void addAppointment(Appointment appointment) {
        appointments.add(appointment);
    }

    // Getters
    public MedicalRecord getMedicalRecord() { return medicalRecord; }
    public List<Doctor> getDoctors() { return doctors; }
    public String getId() { return id; }
    public String getName() { return name; }
}

// Medical Record class
class MedicalRecord {
    private Patient patient;
    private List<String> diagnoses;
    private List<String> medications;
    private List<String> treatments;
    private List<LocalDate> followUps;

    public MedicalRecord(Patient patient) {
        this.patient = patient;
        this.diagnoses = new ArrayList<>();
        this.medications = new ArrayList<>();
        this.treatments = new ArrayList<>();
        this.followUps = new ArrayList<>();
    }

    public void addDiagnosis(Doctor doctor, String diagnosis) {
        if (patient.getDoctors().contains(doctor)) {
            diagnoses.add(diagnosis);
        } else {
            throw new SecurityException("Doctor not authorized to modify this record");
        }
    }

    // Similar methods for medications, treatments, and follow-ups
    public List<String> getDiagnoses() { return diagnoses; }
}

// Appointment class
class Appointment {
    private String id;
    private Patient patient;
    private Doctor doctor;
    private LocalDate date;
    private LocalTime time;
    private String room;

    public Appointment(String id, Patient patient, Doctor doctor, LocalDate date, LocalTime time, String room) {
        this.id = id;
        this.patient = patient;
        this.doctor = doctor;
        this.date = date;
        this.time = time;
        this.room = room;
    }

    // Getters
    public Doctor getDoctor() { return doctor; }
}

// Hospital Management System
public class hos {
    private List<Patient> patients = new ArrayList<>();
    private List<Doctor> doctors = new ArrayList<>();
    private List<Nurse> nurses = new ArrayList<>();
    private List<Appointment> appointments = new ArrayList<>();
    private int appointmentCounter = 1;

    public void addPatient(Patient patient) {
        patients.add(patient);
    }

    public void addDoctor(Doctor doctor) {
        doctors.add(doctor);
    }

    public void addNurse(Nurse nurse) {
        nurses.add(nurse);
    }

    public void createAppointment(Patient patient, Doctor doctor, LocalDate date, LocalTime time, String room) {
        String appointmentId = "APP-" + appointmentCounter++;
        Appointment appointment = new Appointment(appointmentId, patient, doctor, date, time, room);
        appointments.add(appointment);
        patient.addAppointment(appointment);
        doctor.addPatient(patient);
        patient.addDoctor(doctor);
    }

    // Main Menu
    public static void main(String[] args) {
        hos hospital = new hos();
        Scanner scanner = new Scanner(System.in);
        int choice;

        do {
            System.out.println("\n--- Hospital Management System ---");
            System.out.println("1. Manage Patients");
            System.out.println("2. Manage Doctors");
            System.out.println("3. Manage Nurses");
            System.out.println("4. Manage Medical Records");
            System.out.println("5. Exit");
            System.out.print("Enter your choice: ");
            choice = scanner.nextInt();
            scanner.nextLine(); // Consume the newline character

            switch (choice) {
                case 1:
                    managePatients(hospital, scanner);
                    break;
                case 2:
                    manageDoctors(hospital, scanner);
                    break;
                case 3:
                    manageNurses(hospital, scanner);
                    break;
                case 4:
                    manageMedicalRecords(hospital, scanner);
                    break;
                case 5:
                    System.out.println("Exiting the system. Goodbye!");
                    break;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        } while (choice != 5);

        scanner.close();
    }

    // Manage Patients
    private static void managePatients(hos hospital, Scanner scanner) {
        int choice;
        do {
            System.out.println("\n--- Manage Patients ---");
            System.out.println("1. Add Patient");
            System.out.println("2. View Patients");
            System.out.println("3. Back to Main Menu");
            System.out.print("Enter your choice: ");
            choice = scanner.nextInt();
            scanner.nextLine(); // Consume the newline character

            switch (choice) {
                case 1:
                    System.out.println("Enter Patient ID:");
                    String patientId = scanner.nextLine();
                    System.out.println("Enter Patient Name:");
                    String patientName = scanner.nextLine();
                    System.out.println("Enter Patient Address:");
                    String address = scanner.nextLine();
                    System.out.println("Enter Patient Phone:");
                    String phone = scanner.nextLine();
                    System.out.println("Enter Emergency Contact:");
                    String emergencyContact = scanner.nextLine();

                    Patient patient = new Patient(patientId, patientName, address, phone, emergencyContact);
                    hospital.addPatient(patient);
                    System.out.println("Patient added successfully!");
                    break;
                case 2:
                    System.out.println("\n--- List of Patients ---");
                    for (Patient p : hospital.patients) {
                        System.out.println("ID: " + p.getId() + ", Name: " + p.getName());
                    }
                    break;
                case 3:
                    System.out.println("Returning to main menu...");
                    break;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        } while (choice != 3);
    }

    // Manage Doctors
    private static void manageDoctors(hos hospital, Scanner scanner) {
        int choice;
        do {
            System.out.println("\n--- Manage Doctors ---");
            System.out.println("1. Add Doctor");
            System.out.println("2. View Doctors");
            System.out.println("3. Back to Main Menu");
            System.out.print("Enter your choice: ");
            choice = scanner.nextInt();
            scanner.nextLine(); // Consume the newline character

            switch (choice) {
                case 1:
                    System.out.println("Enter Doctor ID:");
                    String docId = scanner.nextLine();
                    System.out.println("Enter Doctor Name:");
                    String docName = scanner.nextLine();
                    System.out.println("Enter Doctor Specialization:");
                    String specialization = scanner.nextLine();
                    System.out.println("Enter Years of Experience:");
                    int yearsExperience = scanner.nextInt();
                    scanner.nextLine(); // Consume the newline character

                    Doctor doc = new Doctor(docId, docName, specialization, yearsExperience);
                    hospital.addDoctor(doc);
                    System.out.println("Doctor added successfully!");
                    break;
                case 2:
                    System.out.println("\n--- List of Doctors ---");
                    for (Doctor d : hospital.doctors) {
                        System.out.println("ID: " + d.getId() + ", Name: " + d.getName() + ", Specialization: " + d.getSpecialization());
                    }
                    break;
                case 3:
                    System.out.println("Returning to main menu...");
                    break;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        } while (choice != 3);
    }

    // Manage Nurses
    private static void manageNurses(hos hospital, Scanner scanner) {
        int choice;
        do {
            System.out.println("\n--- Manage Nurses ---");
            System.out.println("1. Add Nurse");
            System.out.println("2. View Nurses");
            System.out.println("3. Back to Main Menu");
            System.out.print("Enter your choice: ");
            choice = scanner.nextInt();
            scanner.nextLine(); // Consume the newline character

            switch (choice) {
                case 1:
                    System.out.println("Enter Nurse ID:");
                    String nurseId = scanner.nextLine();
                    System.out.println("Enter Nurse Name:");
                    String nurseName = scanner.nextLine();
                    System.out.println("Enter Assigned To (Doctor ID or Department):");
                    String assignedTo = scanner.nextLine();

                    Nurse nurse = new Nurse(nurseId, nurseName, assignedTo);
                    hospital.addNurse(nurse);
                    System.out.println("Nurse added successfully!");
                    break;
                case 2:
                    System.out.println("\n--- List of Nurses ---");
                    for (Nurse n : hospital.nurses) {
                        System.out.println("ID: " + n.getId() + ", Name: " + n.getName() + ", Assigned To: " + n.getAssignedTo());
                    }
                    break;
                case 3:
                    System.out.println("Returning to main menu...");
                    break;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        } while (choice != 3);
    }

    // Manage Medical Records
    private static void manageMedicalRecords(hos hospital, Scanner scanner) {
        int choice;
        do {
            System.out.println("\n--- Manage Medical Records ---");
            System.out.println("1. Add Diagnosis");
            System.out.println("2. View Diagnoses");
            System.out.println("3. Back to Main Menu");
            System.out.print("Enter your choice: ");
            choice = scanner.nextInt();
            scanner.nextLine(); // Consume the newline character

            switch (choice) {
                case 1:
                    System.out.println("Enter Patient ID:");
                    String patientId = scanner.nextLine();
                    System.out.println("Enter Doctor ID:");
                    String docId = scanner.nextLine();
                    System.out.println("Enter Diagnosis:");
                    String diagnosis = scanner.nextLine();

                    Patient patient = hospital.patients.stream()
                            .filter(p -> p.getId().equals(patientId))
                            .findFirst()
                            .orElse(null);
                    Doctor doctor = hospital.doctors.stream()
                            .filter(d -> d.getId().equals(docId))
                            .findFirst()
                            .orElse(null);

                    if (patient != null && doctor != null) {
                        try {
                            patient.getMedicalRecord().addDiagnosis(doctor, diagnosis);
                            System.out.println("Diagnosis added successfully!");
                        } catch (SecurityException e) {
                            System.out.println("Error: " + e.getMessage());
                        }
                    } else {
                        System.out.println("Patient or Doctor not found.");
                    }
                    break;
                case 2:
                    System.out.println("Enter Patient ID:");
                    String pId = scanner.nextLine();
                    Patient p = hospital.patients.stream()
                            .filter(pat -> pat.getId().equals(pId))
                            .findFirst()
                            .orElse(null);

                    if (p != null) {
                        System.out.println("\n--- Diagnoses for Patient " + p.getName() + " ---");
                        for (String d : p.getMedicalRecord().getDiagnoses()) {
                            System.out.println(d);
                        }
                    } else {
                        System.out.println("Patient not found.");
                    }
                    break;
                case 3:
                    System.out.println("Returning to main menu...");
                    break;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        } while (choice != 3);
    }
}
