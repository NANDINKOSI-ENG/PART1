package com.mycompany.prog5121part1;

import java.util.regex.Pattern;

/**
 * Handles registration validation and login authentication.
 *
 * Regular expression reference:
 * Oracle Java Documentation, Pattern class:
 * https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/regex/Pattern.html
 *
 * @author Matimba Calton Hobyani
 */
public class Login {

    private String userId;
    private String secret;
    private String mobileNumber;
    private String givenName;
    private String familyName;

    public Login(String userId, String secret, String mobileNumber,
                 String givenName, String familyName) {
        this.userId = userId;
        this.secret = secret;
        this.mobileNumber = mobileNumber;
        this.givenName = givenName;
        this.familyName = familyName;
    }

    public boolean checkUserName() {
        if (userId == null || userId.length() > 5) {
            return false;
        }

        return userId.contains("_");
    }

    public boolean checkPasswordComplexity() {
        if (secret == null || secret.length() < 8) {
            return false;
        }

        boolean capitalFound = false;
        boolean numberFound = false;
        boolean specialFound = false;

        for (int index = 0; index < secret.length(); index++) {
            char current = secret.charAt(index);

            if (Character.isUpperCase(current)) {
                capitalFound = true;
            } else if (Character.isDigit(current)) {
                numberFound = true;
            } else if (!Character.isLetterOrDigit(current)) {
                specialFound = true;
            }
        }

        return capitalFound && numberFound && specialFound;
    }

    public boolean checkCellPhoneNumber() {
        if (mobileNumber == null) {
            return false;
        }

        String southAfricanMobilePattern = "^\\+27[6-8]\\d{8}$";
        return Pattern.matches(southAfricanMobilePattern, mobileNumber);
    }

    public String registerUser() {
        if (!checkUserName()) {
            return "Username is not correctly formatted; please ensure "
                    + "that your username contains an underscore and is no "
                    + "more than five characters in length.";
        }

        if (!checkPasswordComplexity()) {
            return "Password is not correctly formatted; please ensure "
                    + "that the password contains at least eight characters, "
                    + "a capital letter, a number, and a special character.";
        }

        if (!checkCellPhoneNumber()) {
            return "Cell phone number incorrectly formatted or does not "
                    + "contain international code.";
        }

        return "Username successfully captured.\n"
                + "Password successfully captured.\n"
                + "Cell phone number successfully added.";
    }

    public boolean loginUser(String suppliedUsername, String suppliedPassword) {
        boolean usernameMatches = userId != null
                && userId.equals(suppliedUsername);

        boolean passwordMatches = secret != null
                && secret.equals(suppliedPassword);

        return usernameMatches && passwordMatches;
    }

    public String returnLoginStatus(boolean authenticated) {
        if (authenticated) {
            return "Welcome " + givenName + " " + familyName
                    + " it is great to see you again.";
        }

        return "Username or password incorrect, please try again.";
    }

    public String getUsername() {
        return userId;
    }
}


package com.mycompany.prog5121part1;

import java.util.ArrayList;
import java.util.Scanner;

/**
 * PROG5121 Part 1 - Registration and Login Application.
 *
 * 
 * 
 * 
 * 
 * @author Nandi Nkosi
 */
public class Prog5121part1 {

    public static void main(String[] args) {
        Scanner input = new Scanner(System.in);
        ArrayList<Login> accountList = new ArrayList<>();

        showApplication(input, accountList);

        input.close();
    }

    private static void showApplication(Scanner input,
                                         ArrayList<Login> accountList) {

        boolean running = true;

        System.out.println("\nWelcome to the Registration and Login App!");

        while (running) {
            displayMenu();
            String menuChoice = input.nextLine();

            if (menuChoice.equals("1")) {
                createAccount(input, accountList);
            } else if (menuChoice.equals("2")) {
                authenticateAccount(input, accountList);
            } else if (menuChoice.equals("3")) {
                System.out.println("\nGoodbye!");
                running = false;
            } else {
                System.out.println("\nInvalid option. Please choose 1, 2 or 3.");
            }
        }
    }

    private static void displayMenu() {
        System.out.println("\nMain Menu:");
        System.out.println("1. Register");
        System.out.println("2. Login");
        System.out.println("3. Exit");
        System.out.print("Please choose an option: ");
    }

    private static void createAccount(Scanner input,
                                      ArrayList<Login> accountList) {

        System.out.println("\n=== Registration ===");

        System.out.print("Enter your first name: ");
        String first = input.nextLine();

        System.out.print("Enter your last name: ");
        String last = input.nextLine();

        System.out.print("Enter username: ");
        String newUsername = input.nextLine();

        System.out.print("Enter password: ");
        String newPassword = input.nextLine();

        System.out.print("Enter a South African cell phone number "
                + "(e.g. +27838968976): ");
        String newPhone = input.nextLine();

        Login account = new Login(
                newUsername,
                newPassword,
                newPhone,
                first,
                last
        );

        System.out.println("\n" + account.registerUser());

        if (!account.checkUserName()
                || !account.checkPasswordComplexity()
                || !account.checkCellPhoneNumber()) {

            System.out.println("Registration was not completed.");
            return;
        }

        if (usernameAlreadyUsed(accountList, newUsername)) {
            System.out.println("Username already exists. "
                    + "Please choose another username.");
            return;
        }

        accountList.add(account);
        System.out.println("User registered successfully!");
        System.out.println("You can now choose Login from the Main Menu.");
    }

    private static boolean usernameAlreadyUsed(
            ArrayList<Login> accountList, String candidate) {

        for (Login account : accountList) {
            if (account.getUsername().equals(candidate)) {
                return true;
            }
        }

        return false;
    }

    private static void authenticateAccount(
            Scanner input, ArrayList<Login> accountList) {

        if (accountList.isEmpty()) {
            System.out.println("\nNo users are registered yet. "
                    + "Please register first.");
            return;
        }

        System.out.println("\nLogin");

        System.out.print("Enter username: ");
        String loginName = input.nextLine();

        System.out.print("Enter password: ");
        String loginPassword = input.nextLine();

        Login matchedAccount = findAccount(accountList, loginName);

        if (matchedAccount == null) {
            System.out.println(
                    "Username or password incorrect, please try again."
            );
            return;
        }

        boolean authenticated = matchedAccount.loginUser(
                loginName,
                loginPassword
        );

        System.out.println(
                matchedAccount.returnLoginStatus(authenticated)
        );
    }

    private static Login findAccount(
            ArrayList<Login> accountList, String requestedUsername) {

        for (Login account : accountList) {
            if (account.getUsername().equals(requestedUsername)) {
                return account;
            }
        }

        return null;
    }
}



