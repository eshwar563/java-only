# java-only
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class TrafficLightSimulator extends JFrame implements ActionListener {
    // UI Components
    private JLabel messageLabel;
    private JRadioButton redButton;
    private JRadioButton yellowButton;
    private JRadioButton greenButton;
    private ButtonGroup buttonGroup;

    public TrafficLightSimulator() {
        // Set up the frame layout and properties
        setTitle("Traffic Light Simulator");
        setSize(400, 200);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);
        setLayout(new BorderLayout(10, 10));

        // 1. Message Label (Initially empty, placed at the top/North)
        messageLabel = new JLabel("", SwingConstants.CENTER);
        messageLabel.setFont(new Font("Arial", Font.BOLD, 28));
        add(messageLabel, BorderLayout.CENTER);

        // 2. Radio Buttons Panel (Placed at the bottom/South)
        JPanel buttonPanel = new JPanel();
        buttonPanel.setLayout(new FlowLayout());

        redButton = new JRadioButton("Red");
        yellowButton = new JRadioButton("Yellow");
        greenButton = new JRadioButton("Green");

        // Group the buttons so only one can be selected at a time
        buttonGroup = new ButtonGroup();
        buttonGroup.add(redButton);
        buttonGroup.add(yellowButton);
        buttonGroup.add(greenButton);

        // Add action listeners to handle selections
        redButton.addActionListener(this);
        yellowButton.addActionListener(this);
        greenButton.addActionListener(this);

        // Add buttons to the panel
        buttonPanel.add(redButton);
        buttonPanel.add(yellowButton);
        buttonPanel.add(greenButton);

        add(buttonPanel, BorderLayout.SOUTH);
    }

    // Handle button selections
    @Override
    public void actionPerformed(ActionEvent e) {
        if (e.getSource() == redButton) {
            messageLabel.setText("Stop");
            messageLabel.setForeground(Color.RED);
        } else if (e.getSource() == yellowButton) {
            messageLabel.setText("Ready");
            messageLabel.setForeground(Color.ORANGE); // Orange provides better visual clarity than pure yellow on a light background
        } else if (e.getSource() == greenButton) {
            messageLabel.setText("Go");
            messageLabel.setForeground(new Color(0, 150, 0)); // A clean, readable green color
        }
    }

    public static void main(String[] args) {
        // Run the GUI inside the Event Dispatch Thread (EDT)
        SwingUtilities.invokeLater(() -> {
            new TrafficLightSimulator().setVisible(true);
        });
    }
}
