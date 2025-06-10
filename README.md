import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.util.HashMap;

public class SmartCurrencyConverter {

    // Core conversion rates
    private HashMap<String, Double> currencyRates;

    // GUI Components
    private JFrame frame;
    private JComboBox<String> fromCurrency, toCurrency;
    private JTextField amountField;
    private JButton convertButton;
    private JLabel resultLabel, titleLabel;
    private JCheckBox darkModeToggle;

    public SmartCurrencyConverter() {
        initializeRates();
        buildUI();
    }

    // Initialize currency rates (static for demo)
    private void initializeRates() {
        currencyRates = new HashMap<>();
        currencyRates.put("INR", 1.0);
        currencyRates.put("USD", 0.012);
        currencyRates.put("EUR", 0.011);
        currencyRates.put("GBP", 0.0095);
        currencyRates.put("JPY", 1.72);
    }

    // Convert logic with data validation
    private double convertCurrency(String from, String to, double amount) {
        double inINR = amount / currencyRates.get(from);
        return inINR * currencyRates.get(to);
    }

    // Build full UI
    private void buildUI() {
        frame = new JFrame("Smart Currency Converter");
        frame.setSize(420, 380);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setLocationRelativeTo(null);

        JPanel panel = new JPanel(new GridLayout(9, 1, 8, 8));
        panel.setBorder(BorderFactory.createEmptyBorder(15, 25, 15, 25));

        titleLabel = new JLabel("Smart Currency Converter", SwingConstants.CENTER);
        titleLabel.setFont(new Font("Arial", Font.BOLD, 20));
        panel.add(titleLabel);

        fromCurrency = new JComboBox<>(currencyRates.keySet().toArray(new String[0]));
        toCurrency = new JComboBox<>(currencyRates.keySet().toArray(new String[0]));

        amountField = new JTextField();

        panel.add(new JLabel("From Currency:"));
        panel.add(fromCurrency);
        panel.add(new JLabel("To Currency:"));
        panel.add(toCurrency);
        panel.add(new JLabel("Amount to Convert:"));
        panel.add(amountField);

        convertButton = new JButton("Convert");
        resultLabel = new JLabel("Result: ", SwingConstants.CENTER);
        resultLabel.setFont(new Font("Arial", Font.BOLD, 14));
        darkModeToggle = new JCheckBox("Dark Mode");

        JPanel bottomPanel = new JPanel(new GridLayout(1, 2));
        bottomPanel.add(convertButton);
        bottomPanel.add(darkModeToggle);

        panel.add(bottomPanel);
        panel.add(resultLabel);

        frame.add(panel);
        frame.setVisible(true);

        // Events
        convertButton.addActionListener(e -> handleConversion());
        darkModeToggle.addActionListener(e -> toggleDarkMode(panel));
    }

    // Handle conversion with validation and error handling
    private void handleConversion() {
        try {
            String from = (String) fromCurrency.getSelectedItem();
            String to = (String) toCurrency.getSelectedItem();
            String inputText = amountField.getText().trim();

            if (inputText.isEmpty()) {
                throw new IllegalArgumentException("Amount cannot be empty.");
            }

            double amount = Double.parseDouble(inputText);

            if (amount < 0) {
                throw new IllegalArgumentException("Amount must be positive.");
            }

            double result = convertCurrency(from, to, amount);
            resultLabel.setText("Result: " + String.format("%.2f", result) + " " + to);

        } catch (NumberFormatException ex) {
            JOptionPane.showMessageDialog(frame, "Invalid number format. Enter a valid amount.", "Error", JOptionPane.ERROR_MESSAGE);
        } catch (IllegalArgumentException ex) {
            JOptionPane.showMessageDialog(frame, ex.getMessage(), "Validation Error", JOptionPane.WARNING_MESSAGE);
        } catch (Exception ex) {
            JOptionPane.showMessageDialog(frame, "Unexpected error occurred.", "System Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    // Dark mode toggle for entire UI
    private void toggleDarkMode(JPanel panel) {
        Color bg = darkModeToggle.isSelected() ? Color.DARK_GRAY : Color.WHITE;
        Color fg = darkModeToggle.isSelected() ? Color.WHITE : Color.BLACK;

        for (Component c : panel.getComponents()) {
            c.setBackground(bg);
            c.setForeground(fg);
            if (c instanceof JPanel) {
                for (Component child : ((JPanel) c).getComponents()) {
                    child.setBackground(bg);
                    child.setForeground(fg);
                }
            }
        }
        panel.setBackground(bg);
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(SmartCurrencyConverter::new);
    }
}
