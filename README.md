# PROG5121-part2

/*
 * Click nbfs://nbhost/SystemFileSystem/Templates/Licenses/license-default.txt to change this license
 */

package com.mycompany.prog5121_part1_2_st10547298;
import java.io.FileWriter;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Random;
import javax.swing.JOptionPane;
import javax.swing.JPasswordField;
/**
 *
 * @author Wanza
 */
public class PROG5121_Part1_2_ST10547298 {

//                    MAIN
   public static void main(String[] args) {
 
        JOptionPane.showMessageDialog(null,
                "Welcome to QuickChat App 2026!", "QuickChat", JOptionPane.INFORMATION_MESSAGE);
 
        boolean running = true;
        boolean loggedIn = false;
 
        while (running && !loggedIn) {
 
            String[] options = {"Register", "Login", "Exit"};
            int choice = JOptionPane.showOptionDialog(null, "Main Menu - choose an option:",
                    "QuickChat", JOptionPane.DEFAULT_OPTION, JOptionPane.PLAIN_MESSAGE,
                    null, options, options[0]);
 
            switch (choice) {
                case 0:
                    registerFlow();
                    break;
                case 1:
                    loggedIn = loginFlow();
                    break;
                default: // Exit or window closed
                    JOptionPane.showMessageDialog(null, "Goodbye!");
                    running = false;
            }
        }
 
        // Part 2: only logged-in users may send messages
        if (loggedIn) {
            quickChatMenu();
        }
    }
 
    //                      PART 1
    private static void registerFlow() {
        String regUser = JOptionPane.showInputDialog(null, "Enter Username:", "Registration", JOptionPane.QUESTION_MESSAGE);
        if (regUser == null) return;
        regUser = regUser.trim();
 
        String regPass = askPassword("Enter Password:", "Registration");
        if (regPass == null) return;
 
        String regCell = JOptionPane.showInputDialog(null,
                "Enter Cellphone Number (e.g. +27*********):", "Registration", JOptionPane.QUESTION_MESSAGE);
        if (regCell == null) return;
        regCell = regCell.trim();
 
        registerUser(regUser, regPass, regCell);
    }
 
    private static boolean loginFlow() {
        String logUser = JOptionPane.showInputDialog(null, "Enter Username:", "Login", JOptionPane.QUESTION_MESSAGE);
        if (logUser == null) return false;
 
        String logPass = askPassword("Enter Password:", "Login");
        if (logPass == null) return false;
 
        return loginUser(logUser.trim(), logPass);
    }
 
    // Password box that hides what is typed
    private static String askPassword(String prompt, String title) {
        JPasswordField field = new JPasswordField();
        int result = JOptionPane.showConfirmDialog(null, new Object[]{prompt, field},
                title, JOptionPane.OK_CANCEL_OPTION, JOptionPane.QUESTION_MESSAGE);
        if (result != JOptionPane.OK_OPTION) return null;
        return new String(field.getPassword());
    }
 
    // User class
    static class User {
        private final String username;
        private final String password;
        private final String cellphone;
 
        public User(String username, String password, String cellphone) {
            this.username = username;
            this.password = password;
            this.cellphone = cellphone;
        }
 
        public String getUsername() { return username; }
        public String getPassword() { return password; }
        public String getCellphone() { return cellphone; }
    }
 
    // Temporary database
    private static final ArrayList<User> database = new ArrayList<>();
 
    // Username validation
    public static boolean checkUserName(String username) {
        String regex = "^(?=.*_)[a-z_]{5}$";
        return username.matches(regex);
    }
 
    // Password validation
    public static boolean checkPasswordComplexity(String password) {
        String regex = "^(?=.*[A-Z])(?=.*\\d)(?=.*[^a-zA-Z0-9]).{8,}$";
        return password.matches(regex);
    }
 
    // South African cellphone validation
    public static boolean checkCellphoneNumber(String cellphone) {
        String regex = "^\\+27[6-8][0-9]{8}$";
        return cellphone.matches(regex);
    }
 
    // Register user
    public static void registerUser(String username, String password, String cellphone) {
 
        if (!checkUserName(username)) {
            JOptionPane.showMessageDialog(null, "Username is not correctly formatted; "
                    + "please ensure that your username contains an underscore(_) "
                    + "and is no more than five characters in length.",
                    "Registration", JOptionPane.ERROR_MESSAGE);
            return;
        }
 
        if (!checkPasswordComplexity(password)) {
            JOptionPane.showMessageDialog(null, "Password is not correctly formatted; "
                    + "please ensure that the password contains at least eight characters, "
                    + "a capital letter, a number, and a special character.",
                    "Registration", JOptionPane.ERROR_MESSAGE);
            return;
        }
 
        if (!checkCellphoneNumber(cellphone)) {
            JOptionPane.showMessageDialog(null, "Cell phone number incorrectly formatted "
                    + "or does not contain international code.",
                    "Registration", JOptionPane.ERROR_MESSAGE);
            return;
        }
 
        for (User u : database) {
            if (u.getUsername().equalsIgnoreCase(username)) {
                JOptionPane.showMessageDialog(null, "Username is already taken.",
                        "Registration", JOptionPane.ERROR_MESSAGE);
                return;
            }
        }
 
        database.add(new User(username, password, cellphone));
        JOptionPane.showMessageDialog(null, "User registered successfully!",
                "Registration", JOptionPane.INFORMATION_MESSAGE);
    }
 
    // Login user - returns true when successful
    public static boolean loginUser(String username, String password) {
 
        for (User u : database) {
            if (u.getUsername().equalsIgnoreCase(username)
                    && u.getPassword().equals(password)) {
 
                JOptionPane.showMessageDialog(null,
                        "Login successful!\nWelcome " + u.getUsername() + ", it is great to see you again.",
                        "Login", JOptionPane.INFORMATION_MESSAGE);
                return true;
            }
        }
 
        JOptionPane.showMessageDialog(null,
                "Username or password is incorrect, please try again.",
                "Login", JOptionPane.ERROR_MESSAGE);
        return false;
    }
 
    //                      PART 2
    private static void quickChatMenu() {
 
        JOptionPane.showMessageDialog(null, "Welcome to QuickChat.", "QuickChat",
                JOptionPane.INFORMATION_MESSAGE);
 
        // User decides how many messages they want to enter
        int maxMessages = askNumberOfMessages();
        if (maxMessages < 0) return; // cancelled
        int messagesEntered = 0;
 
        boolean running = true;
        while (running) {
 
            String[] options = {"1) Send Messages", "2) Show recently sent messages", "3) Quit"};
            int choice = JOptionPane.showOptionDialog(null, "QuickChat Menu - choose a feature:",
                    "QuickChat", JOptionPane.DEFAULT_OPTION, JOptionPane.PLAIN_MESSAGE,
                    null, options, options[0]);
 
            switch (choice) {
                case 0:
                    if (messagesEntered >= maxMessages) {
                        JOptionPane.showMessageDialog(null,
                                "You have already entered the " + maxMessages + " message(s) you set.",
                                "QuickChat", JOptionPane.WARNING_MESSAGE);
                    } else if (sendOneMessage(messagesEntered)) {
                        messagesEntered++;
                        if (messagesEntered == maxMessages) {
                            JOptionPane.showMessageDialog(null,
                                    "Total number of messages sent: " + Message.returnTotalMessages(),
                                    "QuickChat", JOptionPane.INFORMATION_MESSAGE);
                        }
                    }
                    break;
                case 1:
                    JOptionPane.showMessageDialog(null, "Coming Soon.", "QuickChat",
                            JOptionPane.INFORMATION_MESSAGE);
                    break;
                default: // Quit or window closed
                    JOptionPane.showMessageDialog(null,
                            "Total number of messages sent: " + Message.returnTotalMessages()
                            + "\nGoodbye!", "QuickChat", JOptionPane.INFORMATION_MESSAGE);
                    running = false;
            }
        }
    }
 
    private static int askNumberOfMessages() {
        while (true) {
            String input = JOptionPane.showInputDialog(null,
                    "How many messages do you wish to enter?", "QuickChat", JOptionPane.QUESTION_MESSAGE);
            if (input == null) return -1;
            try {
                int n = Integer.parseInt(input.trim());
                if (n > 0) return n;
            } catch (NumberFormatException ignored) { }
            JOptionPane.showMessageDialog(null, "Please enter a whole number greater than 0.",
                    "QuickChat", JOptionPane.ERROR_MESSAGE);
        }
    }
 
    // Collects one message. Returns true if it counted towards the user's limit (sent or stored).
    private static boolean sendOneMessage(int messageNumber) {
 
        // Recipient
        String recipient;
        while (true) {
            recipient = JOptionPane.showInputDialog(null,
                    "Enter the recipient's cell number (with international code):",
                    "Message " + (messageNumber + 1), JOptionPane.QUESTION_MESSAGE);
            if (recipient == null) return false;
            recipient = recipient.trim();
 
            String result = Message.checkRecipientCell(recipient);
            if (result.equals(Message.CELL_SUCCESS)) {
                JOptionPane.showMessageDialog(null, result);
                break;
            }
            JOptionPane.showMessageDialog(null, result, "Error", JOptionPane.ERROR_MESSAGE);
        }
 
        // Message text
        String text;
        while (true) {
            text = JOptionPane.showInputDialog(null,
                    "Enter your message (max 250 characters):",
                    "Message " + (messageNumber + 1), JOptionPane.QUESTION_MESSAGE);
            if (text == null) return false;
            text = text.trim();
 
            String result = Message.checkMessageLength(text);
            if (result.equals(Message.LENGTH_SUCCESS)) {
                JOptionPane.showMessageDialog(null, result);
                break;
            }
            JOptionPane.showMessageDialog(null, result, "Error", JOptionPane.ERROR_MESSAGE);
        }
 
        Message msg = new Message(messageNumber, recipient, text);
        JOptionPane.showMessageDialog(null, "Message ID generated: " + msg.getMessageID());
 
        // Send / Disregard / Store
        String result = msg.sentMessage();
        JOptionPane.showMessageDialog(null, result);
 
        if (result.equals(Message.MSG_SENT) || result.equals(Message.MSG_STORED)) {
            if (result.equals(Message.MSG_SENT)) {
                JOptionPane.showMessageDialog(null, msg.printMessages(),
                        "Message Details", JOptionPane.INFORMATION_MESSAGE);
            }
            return true;
        }
        return false; // disregarded
    }
}
 
//                      MESSAGE CLASS
class Message {
 
    // Messages shown to the user
    public static final String CELL_SUCCESS = "Cell phone number successfully captured.";
    public static final String CELL_FAIL = "Cell phone number is incorrectly formatted or does not "
            + "contain an international code. Please correct the number and try again.";
    public static final String LENGTH_SUCCESS = "Message ready to send.";
    public static final String MSG_SENT = "Message successfully sent.";
    public static final String MSG_DISREGARDED = "Press 0 to delete the message.";
    public static final String MSG_STORED = "Message successfully stored.";
 
    public static final String JSON_FILE = "messages.json";
 
    // Messages sent while the program is running
    private static final ArrayList<Message> sentMessages = new ArrayList<>();
    private static int totalMessagesSent = 0;
 
    private final String messageID;
    private final int messageNumber;
    private final String recipient;
    private final String messageText;
    private final String messageHash;
 
    // Normal constructor - ID is randomly generated
    public Message(int messageNumber, String recipient, String messageText) {
        this(generateMessageID(), messageNumber, recipient, messageText);
    }
 
    // Constructor that accepts a given ID
    public Message(String messageID, int messageNumber, String recipient, String messageText) {
        this.messageID = messageID;
        this.messageNumber = messageNumber;
        this.recipient = recipient;
        this.messageText = messageText;
        this.messageHash = createMessageHash();
    }
 
    // Random ten-digit ID (leading zeros allowed)
    private static String generateMessageID() {
        long number = (long) (new Random().nextDouble() * 10_000_000_000L);
        return String.format("%010d", number);
    }
 
    // Message ID must be no more than ten characters
    public boolean checkMessageID() {
        return messageID != null && messageID.length() <= 10;
    }
 
    // Recipient must be a valid number with an international code
    public static String checkRecipientCell(String cell) {
        if (cell != null && PROG5121_Part1_2_ST10547298.checkCellphoneNumber(cell)) {
            return CELL_SUCCESS;
        }
        return CELL_FAIL;
    }
 
    // Message must not exceed 250 characters
    public static String checkMessageLength(String text) {
        int length = text.length();
        if (length <= 250) {
            return LENGTH_SUCCESS;
        }
        return "Message exceeds 250 characters by " + (length - 250) + ", please reduce the size.";
    }
 
    // First two digits of ID : message number : FIRSTLAST (all caps), e.g. 00:0:HITONIGHT
    public String createMessageHash() {
        String[] words = messageText.trim().split("\\s+");
        String first = words[0].replaceAll("[^A-Za-z0-9]", "");
        String last = words[words.length - 1].replaceAll("[^A-Za-z0-9]", "");
        String firstTwo = messageID.length() >= 2 ? messageID.substring(0, 2) : messageID;
        return (firstTwo + ":" + messageNumber + ":" + first + last).toUpperCase();
    }
 
    // Let the user choose what to do with the message (dialog version)
    public String sentMessage() {
        String[] options = {"Send Message", "Disregard Message", "Store Message to send later"};
        int choice = JOptionPane.showOptionDialog(null, "What would you like to do with this message?",
                "Message " + (messageNumber + 1), JOptionPane.DEFAULT_OPTION, JOptionPane.QUESTION_MESSAGE,
                null, options, options[0]);
        return sentMessage(choice + 1); // 1 = send, 2 = disregard, 3 = store
    }
 
    // Logic version (1 = send, 2 = disregard, 3 = store)
    public String sentMessage(int option) {
        switch (option) {
            case 1:
                sentMessages.add(this);
                totalMessagesSent++;
                return MSG_SENT;
            case 3:
                storeMessage();
                return MSG_STORED;
            default:
                return MSG_DISREGARDED;
        }
    }
 
    // All messages sent while the program is running
    public String printMessages() {
        StringBuilder sb = new StringBuilder();
        for (Message m : sentMessages) {
            sb.append("Message ID: ").append(m.messageID).append("\n")
              .append("Message Hash: ").append(m.messageHash).append("\n")
              .append("Recipient: ").append(m.recipient).append("\n")
              .append("Message: ").append(m.messageText).append("\n\n");
        }
        return sb.toString().trim();
    }
 
    // Total number of messages sent
    public static int returnTotalMessages() {
        return totalMessagesSent;
    }
 
    // Appends this message to messages.json
    public void storeMessage() {
        String json = "{\"messageID\":\"" + escape(messageID) + "\","
                + "\"messageHash\":\"" + escape(messageHash) + "\","
                + "\"recipient\":\"" + escape(recipient) + "\","
                + "\"message\":\"" + escape(messageText) + "\"}";
        try {
            java.nio.file.Path path = java.nio.file.Paths.get(JSON_FILE);
            String existing = java.nio.file.Files.exists(path)
                    ? new String(java.nio.file.Files.readAllBytes(path)).trim() : "";
            String updated;
            if (existing.isEmpty() || existing.equals("[]")) {
                updated = "[\n  " + json + "\n]";
            } else {
                updated = existing.substring(0, existing.lastIndexOf(']')).trim() + ",\n  " + json + "\n]";
            }
            try (FileWriter writer = new FileWriter(JSON_FILE)) {
                writer.write(updated);
            }
        } catch (IOException e) {
            JOptionPane.showMessageDialog(null, "Could not store message: " + e.getMessage(),
                    "Error", JOptionPane.ERROR_MESSAGE);
        }
    }
 
    private static String escape(String s) {
        return s.replace("\\", "\\\\").replace("\"", "\\\"").replace("\n", "\\n");
    }
 
    // Getters
    public String getMessageID() { return messageID; }
    public String getMessageHash() { return messageHash; }
    public String getRecipient() { return recipient; }
    public String getMessageText() { return messageText; }
    public int getMessageNumber() { return messageNumber; }
}
