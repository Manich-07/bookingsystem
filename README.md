import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.util.List;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Map;
import java.util.Arrays;

// Enum for travel options
enum TravelOption {
    BUS, TRAIN, AIRPLANE, CAB
}

enum BusType {
    AC_SLEEPER(1.5), NON_AC_SLEEPER(1.0), AC_SEATER(1.2), NON_AC_SEATER(0.8);
    public final double rateMultiplier;

    BusType(double rateMultiplier) { this.rateMultiplier = rateMultiplier; }
}

enum TrainClass {
    FIRST_AC(2.5), SECOND_AC(1.8), THIRD_AC(1.2), SLEEPER(0.9), GENERAL(0.5);
    public final double rateMultiplier;

    TrainClass(double rateMultiplier) { this.rateMultiplier = rateMultiplier; }
}

enum PlaneClass {
    BUSINESS(3.0), ECONOMY(1.5), FIRST_CLASS(5.0);
    public final double rateMultiplier;

    PlaneClass(double rateMultiplier) { this.rateMultiplier = rateMultiplier; }
}

enum CabType {
    THREE_SEATER(1.2), FOUR_SEATER(1.0), FIVE_SEATER(0.9), SIX_SEATER(0.8), AC(1.5), NON_AC(1.0);
    public final double rateMultiplier;

    CabType(double rateMultiplier) { this.rateMultiplier = rateMultiplier; }
}

// Class to store journey timings
class JourneyOption {
    String timing;
    String expressName;

    public JourneyOption(String timing, String expressName) {
        this.timing = timing;
        this.expressName = expressName;
    }

    @Override
    public String toString() {
        return expressName + " at " + timing;
    }
}

// Class to store booking details
class Booking {
    private String userName;
    private int age;
    private String gender;
    private String phoneNumber;
    private TravelOption travelOption;
    private String travelAgency;
    private String timing;
    private String source;
    private String destination;
    private int numberOfTickets;
    private double cost;
    private String travelClass;

    // Constructor
    public Booking(String userName, int age, String gender, String phoneNumber,
                   TravelOption travelOption, String travelAgency, String timing,
                   String source, String destination, int numberOfTickets,
                   double cost, String travelClass) {
        this.userName = userName;
        this.age = age;
        this.gender = gender;
        this.phoneNumber = phoneNumber;
        this.travelOption = travelOption;
        this.travelAgency = travelAgency;
        this.timing = timing;
        this.source = source;
        this.destination = destination;
        this.numberOfTickets = numberOfTickets;
        this.cost = cost;
        this.travelClass = travelClass;
    }

    @Override
    public String toString() {
        return "Booking{" +
                "userName='" + userName + '\'' +
                ", age=" + age +
                ", gender='" + gender + '\'' +
                ", phoneNumber='" + phoneNumber + '\'' +
                ", travelOption=" + travelOption +
                ", travelAgency='" + travelAgency + '\'' +
                ", timing='" + timing + '\'' +
                ", source='" + source + '\'' +
                ", destination='" + destination + '\'' +
                ", numberOfTickets=" + numberOfTickets +
                ", cost=₹" + cost +
                ", travelClass='" + travelClass + '\'' +
                '}';
    }
}

// Main class for the travel booking system
public class TravelBookingSystem extends JFrame {
    private static final List<String> CITIES = List.of(
            "Delhi", "Mumbai", "Kolkata", "Chennai", "Bangalore",
            "Hyderabad", "Pune", "Jaipur", "Ahmedabad", "Surat"
    );

    private static final Map<String, Map<String, Integer>> distances = new HashMap<>();
    private static final List<Booking> pastBookings = new ArrayList<>();
    private static final Map<TravelOption, List<JourneyOption>> journeyOptions = new HashMap<>();

    private JTextField usernameField;
    private JPasswordField passwordField;

    // User details to be retained
    private String loggedInUserName;
    private int loggedInUserAge;
    private String loggedInUserGender;
    private String loggedInUserPhoneNumber;

    public TravelBookingSystem() {
        initializeData();
        initializeJourneyOptions();
        createLoginScreen();
    }

    private void initializeData() {
        int[][] distanceMatrix = {
                {0, 1420, 1530, 2180, 2170, 1580, 1160, 260, 800, 1000},    // Delhi
                {1420, 0, 2210, 1340, 980, 710, 150, 1170, 530, 300},      // Mumbai
                {1530, 2210, 0, 1660, 1880, 1490, 2060, 1320, 2020, 2200}, // Kolkata
                {2180, 1340, 1660, 0, 350, 630, 1280, 1920, 1870, 1640},   // Chennai
                {2170, 980, 1880, 350, 0, 500, 950, 1910, 1520, 1290},     // Bangalore
                {1580, 710, 1490, 630, 500, 0, 570, 1320, 930, 700},       // Hyderabad
                {1160, 150, 2060, 1280, 950, 570, 0, 1100, 660, 450},      // Pune
                {260, 1170, 1320, 1920, 1910, 1320, 1100, 0, 790, 1030},   // Jaipur
                {800, 530, 2020, 1870, 1520, 930, 660, 790, 0, 250},       // Ahmedabad
                {1000, 300, 2200, 1640, 1290, 700, 450, 1030, 250, 0}      // Surat
        };

        for (int i = 0; i < CITIES.size(); i++) {
            Map<String, Integer> cityDistances = new HashMap<>();
            for (int j = 0; j < CITIES.size(); j++) {
                if (i != j) {
                    cityDistances.put(CITIES.get(j), distanceMatrix[i][j]);
                }
            }
            distances.put(CITIES.get(i), cityDistances);
        }
    }

    private void initializeJourneyOptions() {
        journeyOptions.put(TravelOption.BUS, Arrays.asList(
                new JourneyOption("08:00 AM", "Rajdhani Express Bus"),
                new JourneyOption("10:00 AM", "Kondaveedu Express Bus"),
                new JourneyOption("12:00 PM", "Shatabdi Express Bus")
        ));

        journeyOptions.put(TravelOption.TRAIN, Arrays.asList(
                new JourneyOption("09:00 AM", "Rajdhani Express"),
                new JourneyOption("11:00 AM", "Duronto Express"),
                new JourneyOption("01:00 PM", "Kochuveli Express")
        ));

        journeyOptions.put(TravelOption.AIRPLANE, Arrays.asList(
                new JourneyOption("07:00 AM", "IndiGo Flight 1"),
                new JourneyOption("09:30 AM", "SpiceJet Flight 2"),
                new JourneyOption("02:00 PM", "Air India Flight 3")
        ));

        journeyOptions.put(TravelOption.CAB, Arrays.asList(
                new JourneyOption("Anytime", "Fast Cab Service")
        ));
    }

    private void createLoginScreen() {
        setTitle("Travel Booking System - Login");
        setSize(400, 400);
        setDefaultCloseOperation(JFrame.DISPOSE_ON_CLOSE); // Close only the login window
        setLocationRelativeTo(null);

        JPanel panel = new JPanel(new GridLayout(6, 2, 10, 10));
        panel.setBorder(BorderFactory.createEmptyBorder(20, 20, 20, 20));

        // Username
        panel.add(new JLabel("Username:"));
        usernameField = new JTextField();
        panel.add(usernameField);

        // Password
        panel.add(new JLabel("Password:"));
        passwordField = new JPasswordField();
        panel.add(passwordField);

        // Age
        panel.add(new JLabel("Age:"));
        JTextField ageField = new JTextField();
        panel.add(ageField);

        // Gender
        panel.add(new JLabel("Gender (Male/Female/Other):"));
        JTextField genderField = new JTextField();
        panel.add(genderField);

        // Phone Number
        panel.add(new JLabel("Phone Number (10 digits):"));
        JTextField phoneNumberField = new JTextField();
        panel.add(phoneNumberField);

        // Login Button
        JButton loginButton = new JButton("Login");
        loginButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String username = usernameField.getText();
                String password = new String(passwordField.getPassword());
                String age = ageField.getText();
                String gender = genderField.getText();
                String phoneNumber = phoneNumberField.getText();

                // Basic validation
                if (username.isEmpty() || password.isEmpty() || age.isEmpty() || gender.isEmpty() || phoneNumber.isEmpty()) {
                    JOptionPane.showMessageDialog(null, "Please fill in all fields.");
                    return;
                }

                // Validate phone number
                if (!phoneNumber.matches("\\d{10}")) {
                    JOptionPane.showMessageDialog(null, "Phone number must be 10 digits.");
                    return;
                }

                // Validate gender
                if (!(gender.equalsIgnoreCase("Male") || gender.equalsIgnoreCase("Female") || gender.equalsIgnoreCase("Other"))) {
                    JOptionPane.showMessageDialog(null, "Gender must be Male, Female, or Other.");
                    return;
                }

                // Store user details
                loggedInUserName = username; 
                loggedInUserAge = Integer.parseInt(age); 
                loggedInUserGender = gender; 
                loggedInUserPhoneNumber = phoneNumber;

                // Accept any username and password for testing
                JOptionPane.showMessageDialog(null, "Login Successful!\nUsername: " + username + "\nAge: " + age + "\nGender: " + gender + "\nPhone Number: " + phoneNumber);
                dispose(); // Close login window
                showMainMenu(); // Open main menu
            }
        });
        panel.add(loginButton);

        add(panel);
        setVisible(true);
    }

    private void showMainMenu() {
        JFrame mainMenuFrame = new JFrame("Travel Booking System - Main Menu");
        mainMenuFrame.setSize(600, 400);
        mainMenuFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); // Close the application when this window is closed
        mainMenuFrame.setLocationRelativeTo(null);

        JPanel panel = new JPanel(new GridLayout(4, 1));
        panel.add(new JLabel("Welcome to the Travel Booking System!", SwingConstants.CENTER));

        JButton bookTripButton = new JButton("Book a Trip");
        bookTripButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                showBookingDialog(mainMenuFrame); // Open the booking dialog
            }
        });
        panel.add(bookTripButton);

        JButton viewBookingsButton = new JButton("View Past Bookings");
        viewBookingsButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                showPastBookingsDialog(mainMenuFrame); // Open the past bookings dialog
            }
        });
        panel.add(viewBookingsButton);

        JButton exitButton = new JButton("Exit");
        exitButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                mainMenuFrame.dispose(); // Close the main menu window
            }
        });
        panel.add(exitButton);

        mainMenuFrame.add(panel);
        mainMenuFrame.setVisible(true);
    }

    private void showBookingDialog(JFrame parentFrame) {
        JDialog bookingDialog = new JDialog(parentFrame, "Book a Trip", true);
        bookingDialog.setSize(400, 300);
        bookingDialog.setLocationRelativeTo(parentFrame);

        JPanel panel = new JPanel(new GridLayout(5, 1));

        // Add travel options
        panel.add(new JLabel("Select Travel Option:"));
        JComboBox<TravelOption> travelOptionComboBox = new JComboBox<>(TravelOption.values());
        panel.add(travelOptionComboBox);

        // Add a button to confirm selection
        JButton confirmButton = new JButton("Confirm");
        confirmButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                TravelOption selectedOption = (TravelOption) travelOptionComboBox.getSelectedItem();
                bookingDialog.dispose(); // Close the dialog
                showBookingDetailsDialog(parentFrame, selectedOption); // Open the booking details dialog
            }
        });
        panel.add(confirmButton);

        bookingDialog.add(panel);
        bookingDialog.setVisible(true);
    }

    private void showBookingDetailsDialog(JFrame parentFrame, TravelOption selectedOption) {
        JDialog bookingDetailsDialog = new JDialog(parentFrame, "Booking Details", true);
        bookingDetailsDialog.setSize(500, 400);
        bookingDetailsDialog.setLocationRelativeTo(parentFrame);

        JPanel panel = new JPanel(new GridLayout(7, 2));

        // Add source city selection
        panel.add(new JLabel("Source City:"));
        JComboBox<String> sourceCityComboBox = new JComboBox<>(CITIES.toArray(new String[0]));
        panel.add(sourceCityComboBox);

        // Add destination city selection
        panel.add(new JLabel("Destination City:"));
        JComboBox<String> destinationCityComboBox = new JComboBox<>(CITIES.toArray(new String[0]));
        panel.add(destinationCityComboBox);

        // Add number of tickets
        panel.add(new JLabel("Number of Tickets:"));
        JTextField numberOfTicketsField = new JTextField();
        panel.add(numberOfTicketsField);

        // Add a button to proceed to the next step
        JButton nextButton = new JButton("Next");
        nextButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                String source = (String) sourceCityComboBox.getSelectedItem();
                String destination = (String) destinationCityComboBox.getSelectedItem();
                int numberOfTickets;

                // Validate number of tickets
                try {
                    numberOfTickets = Integer.parseInt(numberOfTicketsField.getText());
                    if (numberOfTickets <= 0) {
                        throw new NumberFormatException();
                    }
                } catch (NumberFormatException ex) {
                    JOptionPane.showMessageDialog(bookingDetailsDialog, "Please enter a valid number of tickets.");
                    return; // Exit the method to avoid further processing
                }

                // Proceed to the next step (e.g., select timing or class)
                bookingDetailsDialog.dispose(); // Close the current dialog
                showTimingOrClassDialog(parentFrame, selectedOption, source, destination, numberOfTickets);
            }
        });
        panel.add(nextButton);

        bookingDetailsDialog.add(panel);
        bookingDetailsDialog.setVisible(true);
    }

    private void showTimingOrClassDialog(JFrame parentFrame, TravelOption selectedOption,
                                         String source, String destination, int numberOfTickets) {
        JDialog timingOrClassDialog = new JDialog(parentFrame, "Select Timing or Class", true);
        timingOrClassDialog.setSize(500, 400);
        timingOrClassDialog.setLocationRelativeTo(parentFrame);

        JPanel panel = new JPanel(new GridLayout(5, 2));

        // Add timing or class selection based on the travel option
        if (selectedOption == TravelOption.CAB) {
            panel.add(new JLabel("Select Cab Type:"));
            JComboBox<CabType> cabTypeComboBox = new JComboBox<>(CabType.values());
            panel.add(cabTypeComboBox);
        } else {
            panel.add(new JLabel("Select Timing:"));
            JComboBox<JourneyOption> timingComboBox = new JComboBox<>(journeyOptions.get(selectedOption).toArray(new JourneyOption[0]));
            panel.add(timingComboBox);

            // Add class selection for Bus, Train, and Airplane
            panel.add(new JLabel("Select Class:"));
            JComboBox<?> classComboBox;
            switch (selectedOption) {
                case BUS:
                    classComboBox = new JComboBox<>(BusType.values());
                    break;
                case TRAIN:
                    classComboBox = new JComboBox<>(TrainClass.values());
                    break;
                case AIRPLANE:
                    classComboBox = new JComboBox<>(PlaneClass.values());
                    break;
                default:
                    classComboBox = new JComboBox<>();
                    break;
            }
            panel.add(classComboBox);
        }

        // Add a button to confirm the selection
        JButton confirmButton = new JButton("Confirm");
        confirmButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent e) {
                int distance = distances.get(source).get(destination);
                double cost = calculateCost(selectedOption, distance, numberOfTickets);

                // Create a new booking and add it to past bookings
                Booking booking = new Booking(
                    loggedInUserName, // Use the logged-in username
                    loggedInUserAge, // Use the logged-in age
                    loggedInUserGender, // Use the logged-in gender
                    loggedInUserPhoneNumber, // Use the logged-in phone number
                    selectedOption,
                    "Travel Agency", // Replace with actual travel agency
                    "Timing", // Replace with actual timing
                    source,
                    destination,
                    numberOfTickets,
                    cost,
                    "Class" // Replace with actual class
                );
                pastBookings.add(booking);

                // Display booking summary
                JOptionPane.showMessageDialog(timingOrClassDialog,
                    "Booking Summary:\n" +
                    "Source: " + source + "\n" +
                    "Destination: " + destination + "\n" +
                    "Travel Option: " + selectedOption + "\n" +
                    "Number of Tickets: " + numberOfTickets + "\n" +
                    "Total Cost: ₹" + cost
                );

                timingOrClassDialog.dispose(); // Close the dialog
            }
        });
        panel.add(confirmButton);

        timingOrClassDialog.add(panel);
        timingOrClassDialog.setVisible(true);
    }

    private double calculateCost(TravelOption selectedOption, int distance, int numberOfTickets) {
        double baseRate = 0;

        switch (selectedOption) {
            case BUS:
                baseRate = 2.0;
                break;
            case TRAIN:
                baseRate = 1.5;
                break;
            case AIRPLANE:
                baseRate = 5.0;
                break;
            case CAB:
                baseRate = 8.0;
                break;
        }
        return distance * baseRate * numberOfTickets;
    }

    private void showPastBookingsDialog(JFrame parentFrame) {
        JDialog pastBookingsDialog = new JDialog(parentFrame, "Past Bookings", true);
        pastBookingsDialog.setSize(600, 400);
        pastBookingsDialog.setLocationRelativeTo(parentFrame);

        JPanel panel = new JPanel(new BorderLayout());
        JTextArea textArea = new JTextArea();
        textArea.setEditable(false);

        for (Booking booking : pastBookings) {
            textArea.append(booking.toString() + "\n");
        }

        panel.add(new JScrollPane(textArea), BorderLayout.CENTER);
        JButton closeButton = new JButton("Close");
        closeButton.addActionListener(e -> pastBookingsDialog.dispose());
        panel.add(closeButton, BorderLayout.SOUTH);

        pastBookingsDialog.add(panel);
        pastBookingsDialog.setVisible(true);
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> new TravelBookingSystem());
    }
}
