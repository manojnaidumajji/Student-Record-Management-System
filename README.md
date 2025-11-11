import java.sql.*;
import java.util.Scanner;

public class StudentManagement {
    static final String URL = "jdbc:mysql://localhost:3306/studentdb";
    static final String USER = "root";
    static final String PASSWORD = "manoj2130";

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int choice;

        try {
            // Step 1: Load JDBC Driver
            Class.forName("com.mysql.cj.jdbc.Driver");

            // Step 2: Establish Connection
            Connection con = DriverManager.getConnection(URL, USER, PASSWORD);

            while (true) {
                System.out.println("\n=== Student Record Management ===");
                System.out.println("1. Add Student");
                System.out.println("2. View All Students");
                System.out.println("3. Update Student Marks");
                System.out.println("4. Delete Student");
                System.out.println("5. Exit");
                System.out.print("Enter your choice: ");
                choice = sc.nextInt();

                switch (choice) {
                    case 1:
                        addStudent(con, sc);
                        break;
                    case 2:
                        viewStudents(con);
                        break;
                    case 3:
                        updateMarks(con, sc);
                        break;
                    case 4:
                        deleteStudent(con, sc);
                        break;
                    case 5:
                        System.out.println("Exiting... Goodbye!");
                        con.close();
                        System.exit(0);
                    default:
                        System.out.println("Invalid choice! Try again.");
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    static void addStudent(Connection con, Scanner sc) throws SQLException {
        System.out.print("Enter Name: ");
        String name = sc.next();
        System.out.print("Enter Roll No: ");
        String roll = sc.next();
        System.out.print("Enter Course: ");
        String course = sc.next();
        System.out.print("Enter Marks: ");
        int marks = sc.nextInt();

        String sql = "INSERT INTO students(name, roll_no, course, marks) VALUES(?, ?, ?, ?)";
        PreparedStatement ps = con.prepareStatement(sql);
        ps.setString(1, name);
        ps.setString(2, roll);
        ps.setString(3, course);
        ps.setInt(4, marks);
        ps.executeUpdate();

        System.out.println("✅ Student added successfully!");
    }

    static void viewStudents(Connection con) throws SQLException {
        Statement st = con.createStatement();
        ResultSet rs = st.executeQuery("SELECT * FROM students");

        System.out.println("\n--- Student List ---");
        while (rs.next()) {
            System.out.println(
                rs.getInt("id") + " | " +
                rs.getString("name") + " | " +
                rs.getString("roll_no") + " | " +
                rs.getString("course") + " | " +
                rs.getInt("marks"));
        }
    }

    static void updateMarks(Connection con, Scanner sc) throws SQLException {
        System.out.print("Enter Roll No to update: ");
        String roll = sc.next();
        System.out.print("Enter New Marks: ");
        int marks = sc.nextInt();

        String sql = "UPDATE students SET marks = ? WHERE roll_no = ?";
        PreparedStatement ps = con.prepareStatement(sql);
        ps.setInt(1, marks);
        ps.setString(2, roll);

        int rows = ps.executeUpdate();
        if (rows > 0)
            System.out.println("✅ Marks updated successfully!");
        else
            System.out.println("❌ Student not found!");
    }

    static void deleteStudent(Connection con, Scanner sc) throws SQLException {
        System.out.print("Enter Roll No to delete: ");
        String roll = sc.next();

        String sql = "DELETE FROM students WHERE roll_no = ?";
        PreparedStatement ps = con.prepareStatement(sql);
        ps.setString(1, roll);

        int rows = ps.executeUpdate();
        if (rows > 0)
            System.out.println("✅ Student deleted successfully!");
        else
            System.out.println("❌ Student not found!");
    }
}
