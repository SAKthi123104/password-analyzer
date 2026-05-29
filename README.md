# password-analyzer
import java.util.ArrayList;
import java.util.List;
import java.util.Random;
import java.util.Scanner;

public class PasswordStrengthAnalyzer {

    // Simulated database of previously used passwords
    private static final List<String> passwordDatabase = new ArrayList<>();

    static {
        passwordDatabase.add("Password123!");
        passwordDatabase.add("Admin@2025");
        passwordDatabase.add("Welcome2026!");
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.println("=== Password Strength Analyzer ===");
        System.out.print("Enter a password to evaluate: ");
        String password = scanner.nextLine();

        // 1. Check for Reuse (Database Integration)
        if (isPasswordReused(password)) {
            System.out.println("\nResult: REJECTED");
            System.out.println("Error: This password has been used before. Please choose a unique password.");
            System.out.println("Suggested Alternative: " + generateStrongPassword());
        } else {
            // 2. Evaluate Strength
            evaluatePasswordStrength(password);
        }

        scanner.close();
    }

    /**
     * Evaluates the strength of a password based on length, complexity, and uniqueness.
     */
    public static void evaluatePasswordStrength(String password) {
        int score = 0;
        List<String> feedback = new ArrayList<>();

        // Criteria 1: Length Check
        if (password.length() >= 12) {
            score += 2;
        } else if (password.length() >= 8) {
            score += 1;
        } else {
            feedback.add("- Increase password length to at least 8 characters (12+ preferred).");
        }

        // Criteria 2: Complexity Checks
        boolean hasUpper = false;
        boolean hasLower = false;
        boolean hasDigit = false;
        boolean hasSpecial = false;

        for (char ch : password.toCharArray()) {
            if (Character.isUpperCase(ch)) hasUpper = true;
            else if (Character.isLowerCase(ch)) hasLower = true;
            else if (Character.isDigit(ch)) hasDigit = true;
            else if (!Character.isLetterOrDigit(ch)) hasSpecial = true;
        }

        if (hasUpper) score++;
        else feedback.add("- Add at least one uppercase letter (A-Z).");

        if (hasLower) score++;
        else feedback.add("- Add at least one lowercase letter (a-z).");

        if (hasDigit) score++;
        else feedback.add("- Add at least one numerical digit (0-9).");

        if (hasSpecial) score++;
        else feedback.add("- Add at least one special character (e.g., !, @, #, $, %).");

        // Criteria 3: Uniqueness / Common Pattern Check
        if (password.toLowerCase().contains("password") || password.toLowerCase().contains("123")) {
            score = Math.max(0, score - 2); 
            feedback.add("- Avoid obvious words or sequential patterns like 'password' or '123'.");
        }

        // Output results based on the score matrix
        System.out.println("\n--- Analysis Report ---");
        System.out.println("Total Score: " + score + " / 6");

        if (score >= 5) {
            System.out.println("Strength: STRONG");
            System.out.println("Your password meets high security standards.");
        } else if (score >= 3) {
            System.out.println("Strength: MEDIUM");
            System.out.println("Improvement Suggestions:");
            for (String item : feedback) {
                System.out.println(item);
            }
            System.out.println("\nSuggested Alternative: " + generateStrongPassword());
        } else {
            System.out.println("Strength: WEAK");
            System.out.println("Improvement Suggestions:");
            for (String item : feedback) {
                System.out.println(item);
            }
            System.out.println("\nSuggested Alternative: " + generateStrongPassword());
        }
    }

    /**
     * Checks if the password matches any entry in the simulated historical database.
     */
    public static boolean isPasswordReused(String password) {
        for (String oldPassword : passwordDatabase) {
            if (oldPassword.equals(password)) {
                return true;
            }
        }
        return false;
    }

    /**
     * Generates a structurally sound alternative strong password.
     */
    public static String generateStrongPassword() {
        String upperChars = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
        String lowerChars = "abcdefghijklmnopqrstuvwxyz";
        String digits = "0123456789";
        String specialChars = "!@#$%^&*()-_=+[]{}|;:,.<>?";
        String allChars = upperChars + lowerChars + digits + specialChars;

        Random random = new Random();
        StringBuilder sb = new StringBuilder();

        // Ensure at least one character from each mandatory set is present
        sb.append(upperChars.charAt(random.nextInt(upperChars.length())));
        sb.append(lowerChars.charAt(random.nextInt(lowerChars.length())));
        sb.append(digits.charAt(random.nextInt(digits.length())));
        sb.append(specialChars.charAt(random.nextInt(specialChars.length())));

        // Fill remaining length up to 12 characters randomly
        for (int i = 4; i < 12; i++) {
            sb.append(allChars.charAt(random.nextInt(allChars.length())));
        }

        return sb.toString();
    }
}
