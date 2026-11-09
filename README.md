# java-only
1(A) Traffic Light using Radio Buttons
File: TrafficLight.java
-------------------------------------------------
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;

public class TrafficLight extends JFrame implements ActionListener {
    JRadioButton red, yellow, green;
    JLabel message;

    TrafficLight() {
        setTitle("Traffic Light");
        setSize(350, 200);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new FlowLayout());

        message = new JLabel("");
        message.setFont(new Font("Arial", Font.BOLD, 24));
        add(message);

        red = new JRadioButton("Red");
        yellow = new JRadioButton("Yellow");
        green = new JRadioButton("Green");

        ButtonGroup group = new ButtonGroup();
        group.add(red);
        group.add(yellow);
        group.add(green);

        add(red);
        add(yellow);
        add(green);

        red.addActionListener(this);
        yellow.addActionListener(this);
        green.addActionListener(this);

        setVisible(true);
    }

    public void actionPerformed(ActionEvent e) {
        if (e.getSource() == red) {
            message.setText("STOP");
            message.setForeground(Color.RED);
        } else if (e.getSource() == yellow) {
            message.setText("READY");
            message.setForeground(Color.ORANGE);
        } else {
            message.setText("GO");
            message.setForeground(Color.GREEN);
        }
    }

    public static void main(String[] args) {
        new TrafficLight();
    }
}

-------------------------------------------------
1(B) List all files in a directory and subdirectories
File: ListFiles.java
-------------------------------------------------
import java.io.File;
import java.util.Scanner;

public class ListFiles {
    static void showFiles(File folder) {
        File[] files = folder.listFiles();

        if (files == null) {
            return;
        }

        for (File f : files) {
            System.out.println(f.getAbsolutePath());

            if (f.isDirectory()) {
                showFiles(f); // recursive call
            }
        }
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.print("Enter directory path: ");
        String path = sc.nextLine();

        File folder = new File(path);

        if (folder.exists() && folder.isDirectory()) {
            showFiles(folder);
        } else {
            System.out.println("Directory does not exist.");
        }
    }
}

-------------------------------------------------
2(A) Three threads: random number, square if even, cube if odd
File: ThreeThreads.java
-------------------------------------------------
import java.util.Random;
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingQueue;

public class ThreeThreads {
    static BlockingQueue<Integer> evenQ = new LinkedBlockingQueue<>();
    static BlockingQueue<Integer> oddQ = new LinkedBlockingQueue<>();
    static final int STOP = -1;

    public static void main(String[] args) {
        Thread evenThread = new Thread(() -> {
            try {
                while (true) {
                    int n = evenQ.take();
                    if (n == STOP) break;
                    System.out.println("Square of " + n + " = " + (n * n));
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        Thread oddThread = new Thread(() -> {
            try {
                while (true) {
                    int n = oddQ.take();
                    if (n == STOP) break;
                    System.out.println("Cube of " + n + " = " + (n * n * n));
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        Thread generator = new Thread(() -> {
            Random r = new Random();
            try {
                for (int i = 1; i <= 10; i++) {
                    int n = r.nextInt(100) + 1;
                    System.out.println("Generated: " + n);

                    if (n % 2 == 0)
                        evenQ.put(n);
                    else
                        oddQ.put(n);

                    Thread.sleep(1000);
                }
                evenQ.put(STOP);
                oddQ.put(STOP);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        evenThread.start();
        oddThread.start();
        generator.start();
    }
}

-------------------------------------------------
2(B) Read names and phone numbers using HashMap
File: PhoneBook.java
-------------------------------------------------
import java.io.*;
import java.util.*;

public class PhoneBook {
    public static void main(String[] args) throws Exception {
        HashMap<String, String> map = new HashMap<>();

        BufferedReader br = new BufferedReader(new FileReader("contacts.txt"));
        String line;

        while ((line = br.readLine()) != null) {
            String[] data = line.split("\\t");
            if (data.length >= 2) {
                map.put(data[0].trim(), data[1].trim());
                map.put(data[1].trim(), data[0].trim());
            }
        }
        br.close();

        Scanner sc = new Scanner(System.in);
        System.out.print("Enter name or phone number: ");
        String key = sc.nextLine().trim();

        if (map.containsKey(key))
            System.out.println("Corresponding value: " + map.get(key));
        else
            System.out.println("Record not found.");
    }
}

Create contacts.txt in the same folder. Each line must have NAME, press TAB, then PHONE.
Example:
Asha    9876543210
Ravi    9123456780
(There must be a real Tab between name and number.)

-------------------------------------------------
3(A) Producer-Consumer using inter-thread communication
File: ProducerConsumer.java
-------------------------------------------------
import java.util.LinkedList;

class Buffer {
    LinkedList<Integer> list = new LinkedList<>();
    int capacity = 5;

    synchronized void produce(int value) throws InterruptedException {
        while (list.size() == capacity)
            wait();

        list.add(value);
        System.out.println("Produced: " + value);
        notifyAll();
    }

    synchronized void consume() throws InterruptedException {
        while (list.isEmpty())
            wait();

        int value = list.removeFirst();
        System.out.println("Consumed: " + value);
        notifyAll();
    }
}

public class ProducerConsumer {
    public static void main(String[] args) {
        Buffer b = new Buffer();

        Thread producer = new Thread(() -> {
            try {
                for (int i = 1; i <= 10; i++) {
                    b.produce(i);
                    Thread.sleep(500);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        Thread consumer = new Thread(() -> {
            try {
                for (int i = 1; i <= 10; i++) {
                    b.consume();
                    Thread.sleep(800);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        producer.start();
        consumer.start();
    }
}

-------------------------------------------------
3(B) Display a CSV text file using GridLayout and Labels
File: DisplayTable.java
-------------------------------------------------
import javax.swing.*;
import java.awt.*;
import java.io.*;
import java.util.*;

public class DisplayTable {
    public static void main(String[] args) throws Exception {
        BufferedReader br = new BufferedReader(new FileReader("Table.txt"));

        ArrayList<String[]> rows = new ArrayList<>();
        String line;
        int columns = 0;

        while ((line = br.readLine()) != null) {
            String[] values = line.split(",", -1);
            rows.add(values);
            columns = Math.max(columns, values.length);
        }
        br.close();

        JFrame frame = new JFrame("Table");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setLayout(new GridLayout(rows.size(), columns, 5, 5));

        for (String[] row : rows) {
            for (int c = 0; c < columns; c++) {
                String value = c < row.length ? row[c].trim() : "";
                JLabel label = new JLabel(value, SwingConstants.CENTER);
                label.setBorder(BorderFactory.createLineBorder(Color.BLACK));
                frame.add(label);
            }
        }

        frame.setSize(500, 300);
        frame.setVisible(true);
    }
}

Create Table.txt in the same folder, for example:
Name,Age,City
Asha,20,Hyderabad
Ravi,21,Chennai

-------------------------------------------------
4) Doubly linked list: create, delete an element, display
File: DoublyList.java
-------------------------------------------------
import java.util.Scanner;

public class DoublyList {
    static class Node {
        int data;
        Node prev, next;

        Node(int data) {
            this.data = data;
        }
    }

    Node head = null;

    void add(int value) {
        Node n = new Node(value);

        if (head == null) {
            head = n;
            return;
        }

        Node temp = head;
        while (temp.next != null)
            temp = temp.next;

        temp.next = n;
        n.prev = temp;
    }

    void delete(int value) {
        Node temp = head;

        while (temp != null && temp.data != value)
            temp = temp.next;

        if (temp == null) {
            System.out.println("Element not found.");
            return;
        }

        if (temp.prev != null)
            temp.prev.next = temp.next;
        else
            head = temp.next;

        if (temp.next != null)
            temp.next.prev = temp.prev;

        System.out.println("Deleted: " + value);
    }

    void display() {
        Node temp = head;
        System.out.print("List: ");

        while (temp != null) {
            System.out.print(temp.data + " ");
            temp = temp.next;
        }
        System.out.println();
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        DoublyList list = new DoublyList();

        System.out.print("How many elements? ");
        int n = sc.nextInt();

        System.out.println("Enter elements:");
        for (int i = 0; i < n; i++)
            list.add(sc.nextInt());

        System.out.print("Enter element to delete: ");
        int value = sc.nextInt();

        list.delete(value);
        list.display();
    }
}

-------------------------------------------------
5(A) Integer division GUI with exception handling
File: DivisionGUI.java
-------------------------------------------------
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;

public class DivisionGUI extends JFrame implements ActionListener {
    JTextField num1, num2, result;
    JButton divide;

    DivisionGUI() {
        setTitle("Integer Division");
        setSize(350, 220);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new GridLayout(4, 2, 5, 5));

        add(new JLabel("Num1:"));
        num1 = new JTextField();
        add(num1);

        add(new JLabel("Num2:"));
        num2 = new JTextField();
        add(num2);

        add(new JLabel("Result:"));
        result = new JTextField();
        result.setEditable(false);
        add(result);

        divide = new JButton("Divide");
        add(divide);
        divide.addActionListener(this);

        setVisible(true);
    }

    public void actionPerformed(ActionEvent e) {
        try {
            int a = Integer.parseInt(num1.getText());
            int b = Integer.parseInt(num2.getText());
            result.setText(String.valueOf(a / b));
        } catch (NumberFormatException ex) {
            JOptionPane.showMessageDialog(this, "Enter valid integers.");
        } catch (ArithmeticException ex) {
            JOptionPane.showMessageDialog(this, "Cannot divide by zero.");
        }
    }

    public static void main(String[] args) {
        new DivisionGUI();
    }
}

-------------------------------------------------
5(B) Handle mouse events using Adapter classes
File: MouseEvents.java
-------------------------------------------------
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;

public class MouseEvents extends JFrame {
    JLabel label;

    MouseEvents() {
        setTitle("Mouse Events");
        setSize(400, 250);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout());

        label = new JLabel("Perform a mouse event", SwingConstants.CENTER);
        add(label, BorderLayout.CENTER);

        JPanel panel = new JPanel();
        panel.addMouseListener(new MouseAdapter() {
            public void mouseClicked(MouseEvent e) {
                label.setText("Mouse Clicked");
            }
            public void mousePressed(MouseEvent e) {
                label.setText("Mouse Pressed");
            }
            public void mouseReleased(MouseEvent e) {
                label.setText("Mouse Released");
            }
            public void mouseEntered(MouseEvent e) {
                label.setText("Mouse Entered");
            }
            public void mouseExited(MouseEvent e) {
                label.setText("Mouse Exited");
            }
        });

        panel.addMouseMotionListener(new MouseMotionAdapter() {
            public void mouseMoved(MouseEvent e) {
                label.setText("Mouse Moved");
            }
            public void mouseDragged(MouseEvent e) {
                label.setText("Mouse Dragged");
            }
        });

        panel.addMouseWheelListener(e -> label.setText("Mouse Wheel Moved"));
        panel.setBackground(Color.LIGHT_GRAY);
        add(panel, BorderLayout.SOUTH);
        panel.setPreferredSize(new Dimension(400, 100));

        setVisible(true);
    }

    public static void main(String[] args) {
        new MouseEvents();
    }
}

-------------------------------------------------
6(A) Abstract Shape class: Rectangle, Triangle, Circle
File: ShapeDemo.java
-------------------------------------------------
abstract class Shape {
    int a, b;

    Shape(int a, int b) {
        this.a = a;
        this.b = b;
    }

    abstract void printArea();
}

class Rectangle extends Shape {
    Rectangle(int length, int breadth) {
        super(length, breadth);
    }

    void printArea() {
        System.out.println("Rectangle area = " + (a * b));
    }
}

class Triangle extends Shape {
    Triangle(int base, int height) {
        super(base, height);
    }

    void printArea() {
        System.out.println("Triangle area = " + (0.5 * a * b));
    }
}

class Circle extends Shape {
    Circle(int radius) {
        super(radius, radius);
    }

    void printArea() {
        System.out.println("Circle area = " + (Math.PI * a * a));
    }
}

public class ShapeDemo {
    public static void main(String[] args) {
        Shape r = new Rectangle(10, 5);
        Shape t = new Triangle(10, 5);
        Shape c = new Circle(7);

        r.printArea();
        t.printArea();
        c.printArea();
    }
}

-------------------------------------------------
6(B) Simple calculator using GridLayout
File: Calculator.java
-------------------------------------------------
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;

public class Calculator extends JFrame implements ActionListener {
    JTextField display;
    double first;
    String operator = "";
    boolean newNumber = true;

    Calculator() {
        setTitle("Calculator");
        setSize(300, 350);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout());

        display = new JTextField("0");
        display.setEditable(false);
        add(display, BorderLayout.NORTH);

        JPanel panel = new JPanel(new GridLayout(4, 4, 5, 5));
        String[] keys = {
            "7", "8", "9", "+",
            "4", "5", "6", "-",
            "1", "2", "3", "*",
            "0", "C", "=", "%"
        };

        for (String key : keys) {
            JButton button = new JButton(key);
            button.addActionListener(this);
            panel.add(button);
        }

        add(panel, BorderLayout.CENTER);
        setVisible(true);
    }

    public void actionPerformed(ActionEvent e) {
        String key = e.getActionCommand();

        if (key.matches("[0-9]")) {
            if (newNumber || display.getText().equals("0"))
                display.setText(key);
            else
                display.setText(display.getText() + key);
            newNumber = false;
        } else if (key.equals("C")) {
            display.setText("0");
            first = 0;
            operator = "";
            newNumber = true;
        } else if (key.equals("=")) {
            try {
                double second = Double.parseDouble(display.getText());
                double answer = 0;

                switch (operator) {
                    case "+": answer = first + second; break;
                    case "-": answer = first - second; break;
                    case "*": answer = first * second; break;
                    case "%":
                        if (second == 0) throw new ArithmeticException();
                        answer = first % second;
                        break;
                    default: return;
                }

                display.setText(String.valueOf(answer));
                operator = "";
                newNumber = true;
            } catch (ArithmeticException ex) {
                JOptionPane.showMessageDialog(this, "Cannot divide/remainder by zero.");
                display.setText("0");
                newNumber = true;
            }
        } else {
            first = Double.parseDouble(display.getText());
            operator = key;
            newNumber = true;
        }
    }

    public static void main(String[] args) {
        new Calculator();
    }
}
