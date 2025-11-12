import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;
import java.util.stream.Collectors;

/**
 * A complete, console-based E-Health Management System in a single Java file.
 *
 * This program demonstrates core functionalities like managing patients, doctors,
 * appointments, and basic medical records.
 *
 * It uses nested classes (Patient, Doctor, Appointment) to keep everything
 * self-contained.
 */
public class EHealthManagementSystem {

    // --- Data Models (Nested Classes) ---

    /**
     * Represents a Patient in the system.
     */
    static class Patient {
        private String patientId;
        private String name;
        private int age;
        private String gender;
        private String contact;
        private List<String> medicalHistory; // A simple list of medical notes

        public Patient(String patientId, String name, int age, String gender, String contact) {
            this.patientId = patientId;
            this.name = name;
            this.age = age;
            this.gender = gender;
            this.contact = contact;
            this.medicalHistory = new ArrayList<>();
            this.medicalHistory.add("Patient record created."); // Initial note
        }

        // Getters
        public String getPatientId() { return patientId; }
        public String getName() { return name; }
        public List<String> getMedicalHistory() { return medicalHistory; }

        /**
         * Adds a new note to the patient's medical history.
         * @param note The medical note to add.
         */
        public void addMedicalHistory(String note) {
            this.medicalHistory.add(note);
            System.out.println("Medical note added for patient " + this.name);
        }

        @Override
        public String toString() {
            return "Patient [ID=" + patientId + ", Name=" + name + ", Age=" + age +
                   ", Gender=" + gender + ", Contact=" + contact + "]";
        }

        /**
         * Displays the full medical record for the patient.
         */
        public void displayMedicalRecord() {
            System.out.println("--- Medical Record for " + this.name + " (ID: " + this.patientId + ") ---");
            if (medicalHistory.isEmpty()) {
                System.out.println("No medical history found.");
            } else {
                for (int i = 0; i < medicalHistory.size(); i++) {
                    System.out.println((i + 1) + ". " + medicalHistory.get(i));
                }
            }
            System.out.println("------------------------------------------");
        }
    }

    /**
     * Represents a Doctor in the system.
     */
    static class Doctor {
        private String doctorId;
        private String name;
        private String specialization;
        private String contact;

        public Doctor(String doctorId, String name, String specialization, String contact) {
            this.doctorId = doctorId;
            this.name = name;
            this.specialization = specialization;
            this.contact = contact;
        }

        // Getters
        public String getDoctorId() { return doctorId; }
        public String getName() { return name; }
        public String getSpecialization() { return specialization; }

        @Override
        public String toString() {
            return "Doctor [ID=" + doctorId + ", Name=" + name +
                   ", Specialization=" + specialization + ", Contact=" + contact + "]";
        }
    }

    /**
     * Represents an Appointment, linking one Patient and one Doctor.
     */
    static class Appointment {
        private String appointmentId;
        private Patient patient;
        private Doctor doctor;
        private String date; // e.g., "2025-12-25"
        private String time; // e.g., "10:30 AM"
        private String status; // "Scheduled", "Completed", "Cancelled"

        public Appointment(String appointmentId, Patient patient, Doctor doctor, String date, String time) {
            this.appointmentId = appointmentId;
            this.patient = patient;
            this.doctor = doctor;
            this.date = date;
            this.time = time;
            this.status = "Scheduled"; // Default status
        }

        // Getters
        public String getAppointmentId() { return appointmentId; }
        public Patient getPatient() { return patient; }
        public Doctor getDoctor() { return doctor; }
        public String getStatus() { return status; }

        public void setStatus(String status) {
            this.status = status;
        }

        @Override
        public String toString() {
            return "Appointment [ID=" + appointmentId + ", Date=" + date + ", Time=" + time +
                   ", Status=" + status + "\n  Patient: " + patient.getName() + " (ID: " + patient.getPatientId() + ")" +
                   "\n  Doctor: " + doctor.getName() + " (Spec: " + doctor.getSpecialization() + ")]";
        }
    }

    // --- Main Application Logic ---

    // In-memory "database" using ArrayLists
    private List<Patient> patients = new ArrayList<>();
    private List<Doctor> doctors = new ArrayList<>();
    private List<Appointment> appointments = new ArrayList<>();

    // Counters for generating unique IDs
    private int patientIdCounter = 1000;
    private int doctorIdCounter = 500;
    private int appointmentIdCounter = 2000;

    // Scanner for user input
    private Scanner scanner;

    /**
     * Constructor to initialize the system and scanner.
     */
    public EHealthManagementSystem() {
        this.scanner = new Scanner(System.in);
        // Add some dummy data to start with
        seedData();
    }
    
    /**
     * Adds initial data to make the system testable.
     */
    private void seedData() {
        // Add dummy patients
        Patient p1 = new Patient("P" + (++patientIdCounter), "John Doe", 34, "Male", "555-1234");
        p1.addMedicalHistory("Diagnosed with Type 2 Diabetes (2023-01-15).");
        p1.addMedicalHistory("Prescribed Metformin 500mg.");
        patients.add(p1);
        patients.add(new Patient("P" + (++patientIdCounter), "Jane Smith", 28, "Female", "555-5678"));

        // Add dummy doctors
        doctors.add(new Doctor("D" + (++doctorIdCounter), "Dr. Alice Brown", "Cardiology", "111-2222"));
        doctors.add(new Doctor("D" + (++doctorIdCounter), "Dr. Bob White", "Dermatology", "111-3333"));
    }

    /**
     * The main entry point to run the application.
     */
    public void run() {
        int choice;
        do {
            printMainMenu();
            choice = getIntInput("Enter your choice: ");
            
            switch (choice) {
                case 1:
                    managePatients();
                    break;
                case 2:
                    manageDoctors();
                    break;
                case 3:
                    manageAppointments();
                    break;
                case 4:
                    accessMedicalRecord();
                    break;
                case 0:
                    System.out.println("Exiting E-Health Management System. Goodbye!");
                    break;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        } while (choice != 0);
    }

    /**
     * Displays the main menu.
     */
    private void printMainMenu() {
        System.out.println("\n--- E-Health Management System ---");
        System.out.println("1. Patient Management");
        System.out.println("2. Doctor Management");
        System.out.println("3. Appointment Management");
        System.out.println("4. Access Patient Medical Record");
        System.out.println("0. Exit");
        System.out.println("------------------------------------");
    }

    // --- Patient Management ---

    private void managePatients() {
        int choice;
        do {
            System.out.println("\n-- Patient Management --");
            System.out.println("1. Add New Patient");
            System.out.println("2. View All Patients");
            System.out.println("3. Search for Patient by ID");
            System.out.println("0. Back to Main Menu");
            choice = getIntInput("Enter your choice: ");

            switch (choice) {
                case 1:
                    addPatient();
                    break;
                case 2:
                    viewAllPatients();
                    break;
                case 3:
                    searchPatientById();
                    break;
                case 0:
                    break;
                default:
                    System.out.println("Invalid choice.");
            }
        } while (choice != 0);
    }

    private void addPatient() {
        System.out.println("\n-- Add New Patient --");
        String name = getStringInput("Enter Name: ");
        int age = getIntInput("Enter Age: ");
        String gender = getStringInput("Enter Gender: ");
        String contact = getStringInput("Enter Contact (Phone): ");

        String patientId = "P" + (++patientIdCounter);
        Patient newPatient = new Patient(patientId, name, age, gender, contact);
        patients.add(newPatient);

        System.out.println("Patient added successfully! New Patient ID: " + patientId);
    }

    private void viewAllPatients() {
        System.out.println("\n-- All Patients --");
        if (patients.isEmpty()) {
            System.out.println("No patients found in the system.");
            return;
        }
        for (Patient p : patients) {
            System.out.println(p);
        }
    }

    private Patient searchPatientById() {
        String id = getStringInput("Enter Patient ID (e.g., P1001): ");
        Patient patient = findPatientById(id);
        
        if (patient != null) {
            System.out.println("Patient Found:");
            System.out.println(patient);
        } else {
            System.out.println("No patient found with ID: " + id);
        }
        return patient;
    }

    /**
     * Helper method to find a patient by their ID.
     * @param id The ID to search for.
     * @return The Patient object, or null if not found.
     */
    private Patient findPatientById(String id) {
        for (Patient p : patients) {
            if (p.getPatientId().equalsIgnoreCase(id)) {
                return p;
            }
        }
        return null;
    }

    // --- Doctor Management ---

    private void manageDoctors() {
        int choice;
        do {
            System.out.println("\n-- Doctor Management --");
            System.out.println("1. Add New Doctor");
            System.out.println("2. View All Doctors");
            System.out.println("0. Back to Main Menu");
            choice = getIntInput("Enter your choice: ");

            switch (choice) {
                case 1:
                    addDoctor();
                    break;
                case 2:
                    viewAllDoctors();
                    break;
                case 0:
                    break;
                default:
                    System.out.println("Invalid choice.");
            }
        } while (choice != 0);
    }

    private void addDoctor() {
        System.out.println("\n-- Add New Doctor --");
        String name = getStringInput("Enter Name (e.g., Dr. Smith): ");
        String specialization = getStringInput("Enter Specialization: ");
        String contact = getStringInput("Enter Contact (Phone): ");

        String doctorId = "D" + (++doctorIdCounter);
        Doctor newDoctor = new Doctor(doctorId, name, specialization, contact);
        doctors.add(newDoctor);

        System.out.println("Doctor added successfully! New Doctor ID: " + doctorId);
    }

    private void viewAllDoctors() {
        System.out.println("\n-- All Doctors --");
        if (doctors.isEmpty()) {
            System.out.println("No doctors found in the system.");
            return;
        }
        for (Doctor d : doctors) {
            System.out.println(d);
        }
    }

    /**
     * Helper method to find a doctor by their ID.
     * @param id The ID to search for.
     * @return The Doctor object, or null if not found.
     */
    private Doctor findDoctorById(String id) {
        for (Doctor d : doctors) {
            if (d.getDoctorId().equalsIgnoreCase(id)) {
                return d;
            }
        }
        return null;
    }

    // --- Appointment Management ---

    private void manageAppointments() {
        int choice;
        do {
            System.out.println("\n-- Appointment Management --");
            System.out.println("1. Schedule New Appointment");
            System.out.println("2. View All Appointments");
            System.out.println("3. View Appointments by Doctor ID");
            System.out.println("4. View Appointments by Patient ID");
            System.out.println("5. Update Appointment Status");
            System.out.println("0. Back to Main Menu");
            choice = getIntInput("Enter your choice: ");

            switch (choice) {
                case 1:
                    scheduleAppointment();
                    break;
                case 2:
                    viewAllAppointments();
                    break;
                case 3:
                    viewAppointmentsByDoctor();
                    break;
                case 4:
                    viewAppointmentsByPatient();
                    break;
                case 5:
                    updateAppointmentStatus();
                    break;
                case 0:
                    break;
                default:
                    System.out.println("Invalid choice.");
            }
        } while (choice != 0);
    }

    private void scheduleAppointment() {
        System.out.println("\n-- Schedule New Appointment --");
        
        // 1. Get Patient
        viewAllPatients();
        String patientId = getStringInput("Enter Patient ID: ");
        Patient patient = findPatientById(patientId);
        if (patient == null) {
            System.out.println("Invalid Patient ID. Aborting appointment.");
            return;
        }

        // 2. Get Doctor
        viewAllDoctors();
        String doctorId = getStringInput("Enter Doctor ID: ");
        Doctor doctor = findDoctorById(doctorId);
        if (doctor == null) {
            System.out.println("Invalid Doctor ID. Aborting appointment.");
            return;
        }

        // 3. Get Date and Time
        String date = getStringInput("Enter Date (YYYY-MM-DD): ");
        String time = getStringInput("Enter Time (e.g., 10:30 AM): ");

        // 4. Create Appointment
        String appointmentId = "A" + (++appointmentIdCounter);
        Appointment newAppointment = new Appointment(appointmentId, patient, doctor, date, time);
        appointments.add(newAppointment);
        
        // Add a note to patient's record
        String note = "Appointment scheduled with " + doctor.getName() + 
                      " (" + doctor.getSpecialization() + ") on " + date + " at " + time + ".";
        patient.addMedicalHistory(note);

        System.out.println("Appointment scheduled successfully! Appointment ID: " + appointmentId);
    }

    private void viewAllAppointments() {
        System.out.println("\n-- All Appointments --");
        if (appointments.isEmpty()) {
            System.out.println("No appointments found.");
            return;
        }
        for (Appointment a : appointments) {
            System.out.println(a);
            System.out.println(); // Extra line for readability
        }
    }

    private void viewAppointmentsByDoctor() {
        String doctorId = getStringInput("Enter Doctor ID: ");
        Doctor doctor = findDoctorById(doctorId);
        if (doctor == null) {
            System.out.println("Invalid Doctor ID.");
            return;
        }

        System.out.println("\n-- Appointments for " + doctor.getName() + " --");
        List<Appointment> doctorApps = appointments.stream()
            .filter(a -> a.getDoctor().getDoctorId().equalsIgnoreCase(doctorId))
            .collect(Collectors.toList());

        if (doctorApps.isEmpty()) {
            System.out.println("No appointments found for this doctor.");
        } else {
            for (Appointment a : doctorApps) {
                System.out.println(a);
                System.out.println();
            }
        }
    }

    private void viewAppointmentsByPatient() {
        String patientId = getStringInput("Enter Patient ID: ");
        Patient patient = findPatientById(patientId);
        if (patient == null) {
            System.out.println("Invalid Patient ID.");
            return;
        }

        System.out.println("\n-- Appointments for " + patient.getName() + " --");
        List<Appointment> patientApps = appointments.stream()
            .filter(a -> a.getPatient().getPatientId().equalsIgnoreCase(patientId))
            .collect(Collectors.toList());

        if (patientApps.isEmpty()) {
            System.out.println("No appointments found for this patient.");
        } else {
            for (Appointment a : patientApps) {
                System.out.println(a);
                System.out.println();
            }
        }
    }

    private void updateAppointmentStatus() {
        String appId = getStringInput("Enter Appointment ID to update: ");
        Appointment appointment = null;
        for (Appointment a : appointments) {
            if (a.getAppointmentId().equalsIgnoreCase(appId)) {
                appointment = a;
                break;
            }
        }

        if (appointment == null) {
            System.out.println("Appointment not found.");
            return;
        }

        System.out.println("Current Status: " + appointment.getStatus());
        String newStatus = getStringInput("Enter new status (e.g., Completed, Cancelled): ");
        appointment.setStatus(newStatus);
        
        // Add note to patient record if completed
        if(newStatus.equalsIgnoreCase("Completed")) {
            appointment.getPatient().addMedicalHistory("Appointment " + appointment.getAppointmentId() + " with " + 
                                                      appointment.getDoctor().getName() + " marked as 'Completed'.");
        }

        System.out.println("Appointment status updated.");
    }

    // --- Medical Record Management ---

    private void accessMedicalRecord() {
        System.out.println("\n-- Access Patient Medical Record --");
        String patientId = getStringInput("Enter Patient ID: ");
        Patient patient = findPatientById(patientId);

        if (patient == null) {
            System.out.println("Patient not found.");
            return;
        }

        patient.displayMedicalRecord();

        // Option to add a new note
        String addNote = getStringInput("Do you want to add a new medical note? (yes/no): ");
        if (addNote.equalsIgnoreCase("yes") || addNote.equalsIgnoreCase("y")) {
            String note = getStringInput("Enter new medical note: ");
            patient.addMedicalHistory(note);
            System.out.println("Note added.");
            patient.displayMedicalRecord(); // Show updated record
        }
    }


    // --- Utility Methods ---

    /**
     * Helper to get a non-empty String input.
     * @param prompt The message to display to the user.
     * @return A non-empty String.
     */
    private String getStringInput(String prompt) {
        String input;
        while (true) {
            System.out.print(prompt);
            input = scanner.nextLine().trim();
            if (!input.isEmpty()) {
                return input;
            } else {
                System.out.println("Input cannot be empty. Please try again.");
            }
        }
    }

    /**
     * Helper to get a valid integer input.
     * @param prompt The message to display to the user.
     * @return A valid integer.
     */
    private int getIntInput(String prompt) {
        while (true) {
            try {
                // Use getStringInput to handle the "nextline" problem
                String inputStr = getStringInput(prompt);
                return Integer.parseInt(inputStr);
            } catch (NumberFormatException e) {
                System.out.println("Invalid input. Please enter a number.");
            }
        }
    }


    // --- Main Method ---

    /**
     * The main method to start the E-Health Management System.
     * @param args Command line arguments (not used).
     */
    public static void main(String[] args) {
        // Create an instance of the system and run it
        EHealthManagementSystem eHealthSystem = new EHealthManagementSystem();
        eHealthSystem.run();
    }
}
