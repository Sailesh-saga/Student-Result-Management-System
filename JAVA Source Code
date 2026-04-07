import java.sql.*;
import java.util.Scanner;

public class StudentResultManagement1 {

    static Scanner sc = new Scanner(System.in);

    static final String URL = "jdbc:oracle:thin:@localhost:1521:XE";
    static final String USER = "system";
    static final String PASSWORD = "12345";

    static String role = "";
    static String loggedInUser = "";

    static boolean login(Connection con) throws Exception {
        System.out.println("\n--- LOGIN ---");
        System.out.println("1. Admin\n2. Faculty\n3. Student");
        System.out.print("Select Role: ");
        int ch = sc.nextInt();

        String selectedRole = (ch == 1) ? "admin" :
                              (ch == 2) ? "faculty" :
                              (ch == 3) ? "student" : "";

        System.out.print("Username: ");
        String user = sc.next();

        System.out.print("Password: ");
        String pass = sc.next();

        PreparedStatement ps = con.prepareStatement(
                "SELECT role FROM login WHERE username=? AND password=?");

        ps.setString(1, user);
        ps.setString(2, pass);

        ResultSet rs = ps.executeQuery();

        if (rs.next() && rs.getString(1).equals(selectedRole)) {
            role = selectedRole;
            loggedInUser = user;
            System.out.println("Login Successful as " + role);
            return true;
        }

        System.out.println("Invalid Login!");
        return false;
    }

    static String getGrade(double avg) {
        if (avg >= 85) return "A";
        else if (avg >= 70) return "B";
        else if (avg >= 50) return "C";
        else if (avg >= 40) return "D";
        else return "F";
    }

    static double calculateSGPA(double avg) {
        return avg / 10;
    }

    static void adminMenu(Connection con) throws Exception {
        int ch;
        do {
            System.out.println("\n--- ADMIN MENU ---");
            System.out.println("1.Add Student");
            System.out.println("2.Add Subject");
            System.out.println("3.Register Course");
            System.out.println("4.Assign Faculty");
            System.out.println("5.View Registrations");
            System.out.println("6.View Results");
            System.out.println("7.Topper List");
            System.out.println("8.Course Count");
            System.out.println("9.Pass/Fail Stats");
            System.out.println("10.Performance Table");
            System.out.println("11.Logout");
	    System.out.print("Choose option:");
            ch = sc.nextInt();

            switch (ch) {
                case 1: addStudent(con); break;
                case 2: addSubject(con); break;
                case 3: courseRegistration(con); break;
                case 4: assignFaculty(con); break;
                case 5: viewRegistrations(con); break;
                case 6: viewResults(con); break;
                case 7: viewTopper(con); break;
                case 8: courseCount(con); break;
                case 9: passFailStats(con); break;
                case 10: performanceTable(con); break;
            }

        } while (ch != 11);

        role = "";
        loggedInUser = "";
    }

    static void addStudent(Connection con) {
    try {
        System.out.print("Student ID: "); int sid = sc.nextInt(); sc.nextLine();
        System.out.print("Name: "); String name = sc.nextLine();
        System.out.print("Branch ID: "); int bid = sc.nextInt();
        System.out.print("Semester: "); int sem = sc.nextInt();

        System.out.print("Username: "); String uname = sc.next();
        System.out.print("Password: "); String pass = sc.next();

        // Insert into student table
        PreparedStatement ps1 = con.prepareStatement(
                "INSERT INTO student VALUES (?,?,?,?,?)");

        ps1.setInt(1, sid);
        ps1.setString(2, name);
        ps1.setInt(3, bid);
        ps1.setInt(4, sem);
        ps1.setString(5, uname);

        ps1.executeUpdate();

        // Insert into login table
        PreparedStatement ps2 = con.prepareStatement(
                "INSERT INTO login VALUES (?, ?, ?)");

        ps2.setString(1, uname);
        ps2.setString(2, pass);
        ps2.setString(3, "student");

        ps2.executeUpdate();

        System.out.println("Student Added Successfully!");

    } catch (Exception e) {
        System.out.println(" Error adding student. Please try again.");
    }
}
   
static void viewResults(Connection con) throws Exception {
    Statement st = con.createStatement();
    ResultSet rs = st.executeQuery(
            "SELECT s.student_name, AVG(m.marks) FROM student s " +
            "JOIN marks m ON s.student_id=m.student_id GROUP BY s.student_name");

    while (rs.next()) {
        double avg = rs.getDouble(2);
        System.out.println(rs.getString(1) + "\t" + avg + "\t" + getGrade(avg));
    }
}

static void viewTopper(Connection con) throws Exception {
    Statement st = con.createStatement();
    ResultSet rs = st.executeQuery(
            "SELECT * FROM (SELECT s.student_name, AVG(m.marks) avg_marks " +
            "FROM student s JOIN marks m ON s.student_id=m.student_id " +
            "GROUP BY s.student_name ORDER BY avg_marks DESC) WHERE ROWNUM<=3");

    int rank = 1;
    while (rs.next()) {
        System.out.println(rank++ + ". " + rs.getString(1) + " - " + rs.getDouble(2));
    }
}

static void courseCount(Connection con) throws Exception {
    Statement st = con.createStatement();
    ResultSet rs = st.executeQuery(
            "SELECT sub.subject_name, COUNT(*) FROM course_registration cr " +
            "JOIN subject sub ON cr.subject_id=sub.subject_id GROUP BY sub.subject_name");

    while (rs.next()) {
        System.out.println(rs.getString(1) + "\t" + rs.getInt(2));
    }
}

static void passFailStats(Connection con) throws Exception {
    Statement st = con.createStatement();
    ResultSet rs = st.executeQuery(
            "SELECT sub.subject_name, " +
            "SUM(CASE WHEN m.marks>=40 THEN 1 ELSE 0 END), " +
            "SUM(CASE WHEN m.marks<40 THEN 1 ELSE 0 END) " +
            "FROM marks m JOIN subject sub ON m.subject_id=sub.subject_id " +
            "GROUP BY sub.subject_name");

    while (rs.next()) {
        System.out.println(rs.getString(1) + "\t" +
                rs.getInt(2) + "\t" + rs.getInt(3));
    }
}

static void performanceTable(Connection con) throws Exception {
    Statement st = con.createStatement();
    ResultSet rs = st.executeQuery(
            "SELECT s.student_name, AVG(m.marks) FROM student s " +
            "JOIN marks m ON s.student_id=m.student_id GROUP BY s.student_name");

    while (rs.next()) {
        double avg = rs.getDouble(2);
        System.out.println(rs.getString(1) + "\t" + avg + "\t" + getGrade(avg));
    }
}

// ================= FACULTY EXTRA =================

static void viewSubjectResults(Connection con) throws Exception {
    System.out.print("Subject ID: ");
    int subid = sc.nextInt();

    PreparedStatement ps = con.prepareStatement(
            "SELECT s.student_name, NVL(m.marks,-1) FROM course_registration cr " +
            "JOIN student s ON cr.student_id=s.student_id " +
            "LEFT JOIN marks m ON cr.student_id=m.student_id AND cr.subject_id=m.subject_id " +
            "WHERE cr.subject_id=?");

    ps.setInt(1, subid);
    ResultSet rs = ps.executeQuery();

    while (rs.next()) {
        int m = rs.getInt(2);
        if (m == -1)
            System.out.println(rs.getString(1) + "\tAB\tFAIL");
        else
            System.out.println(rs.getString(1) + "\t" + m + "\t" + (m >= 40 ? "PASS" : "FAIL"));
    }
}

static void subjectPassPercentage(Connection con) throws Exception {
    System.out.print("Subject ID: ");
    int subid = sc.nextInt();

    PreparedStatement ps = con.prepareStatement(
            "SELECT SUM(CASE WHEN marks>=40 THEN 1 ELSE 0 END), COUNT(*) FROM marks WHERE subject_id=?");

    ps.setInt(1, subid);
    ResultSet rs = ps.executeQuery();

    if (rs.next()) {
        int pass = rs.getInt(1);
        int total = rs.getInt(2);
        System.out.println("Pass %: " + (pass * 100.0 / total));
    }
}

// ================= STUDENT EXTRA =================

static void viewFullResult(Connection con) throws Exception {
    PreparedStatement ps = con.prepareStatement(
            "SELECT s.student_id, s.student_name, b.branch_name, s.semester " +
            "FROM student s JOIN branch b ON s.branch_id=b.branch_id WHERE s.username=?");

    ps.setString(1, loggedInUser);
    ResultSet rs = ps.executeQuery();

    if (!rs.next()) return;

    int sid = rs.getInt(1);

    System.out.println("\n===== RESULT =====");
    System.out.println("Name: " + rs.getString(2));
    System.out.println("Branch: " + rs.getString(3));
    System.out.println("Semester: " + rs.getInt(4));

    PreparedStatement ps2 = con.prepareStatement(
            "SELECT sub.subject_name, NVL(m.marks,-1) FROM course_registration cr " +
            "JOIN subject sub ON cr.subject_id=sub.subject_id " +
            "LEFT JOIN marks m ON cr.student_id=m.student_id AND cr.subject_id=m.subject_id " +
            "WHERE cr.student_id=?");

    ps2.setInt(1, sid);
    ResultSet r2 = ps2.executeQuery();

    while (r2.next()) {
        int m = r2.getInt(2);
        if (m == -1)
            System.out.println(r2.getString(1) + " : AB");
        else
            System.out.println(r2.getString(1) + " : " + m);
    }
}

static void viewMyCourses(Connection con) throws Exception {
    PreparedStatement ps = con.prepareStatement(
            "SELECT sub.subject_name FROM course_registration cr " +
            "JOIN subject sub ON cr.subject_id=sub.subject_id " +
            "JOIN student s ON s.student_id=cr.student_id WHERE s.username=?");

    ps.setString(1, loggedInUser);
    ResultSet rs = ps.executeQuery();

    while (rs.next())
        System.out.println(rs.getString(1));
}

static void subjectWisePerformance(Connection con) throws Exception {
    PreparedStatement ps = con.prepareStatement(
            "SELECT sub.subject_name, NVL(m.marks,-1) FROM course_registration cr " +
            "JOIN subject sub ON cr.subject_id=sub.subject_id " +
            "LEFT JOIN marks m ON cr.student_id=m.student_id AND cr.subject_id=m.subject_id " +
            "JOIN student s ON s.student_id=cr.student_id WHERE s.username=?");

    ps.setString(1, loggedInUser);
    ResultSet rs = ps.executeQuery();

    while (rs.next()) {
        int m = rs.getInt(2);
        if (m == -1)
            System.out.println(rs.getString(1) + "\tAB\tFAIL");
        else
            System.out.println(rs.getString(1) + "\t" + m + "\t" + getGrade(m));
    }
}

static void gradeSummary(Connection con) throws Exception {
    PreparedStatement ps = con.prepareStatement(
            "SELECT NVL(m.marks,-1) FROM course_registration cr " +
            "LEFT JOIN marks m ON cr.student_id=m.student_id AND cr.subject_id=m.subject_id " +
            "JOIN student s ON s.student_id=cr.student_id WHERE s.username=?");

    ps.setString(1, loggedInUser);
    ResultSet rs = ps.executeQuery();

    int pass = 0, fail = 0, abs = 0;

    while (rs.next()) {
        int m = rs.getInt(1);
        if (m == -1) { abs++; fail++; }
        else if (m >= 40) pass++;
        else fail++;
    }

    System.out.println("Pass: " + pass + " Fail: " + fail + " Absent: " + abs);
}

    static void addSubject(Connection con) throws Exception {
        System.out.print("Subject ID: "); int subid = sc.nextInt(); sc.nextLine();
        System.out.print("Subject Name: "); String subname = sc.nextLine();
        System.out.print("Branch ID: "); int bid = sc.nextInt();

        PreparedStatement ps = con.prepareStatement(
                "INSERT INTO subject VALUES (?, ?, ?, 100)");

        ps.setInt(1, subid);
        ps.setString(2, subname);
        ps.setInt(3, bid);

        ps.executeUpdate();
        System.out.println("Subject Added!");
    }

    static void assignFaculty(Connection con) throws Exception {
        System.out.print("Faculty Username: ");
        String faculty = sc.next();

        System.out.print("Subject ID: ");
        int subid = sc.nextInt();

        PreparedStatement ps = con.prepareStatement(
                "INSERT INTO faculty_subject VALUES (?, ?)");

        ps.setString(1, faculty);
        ps.setInt(2, subid);

        ps.executeUpdate();
        System.out.println("Faculty Assigned!");
    }

    static void courseRegistration(Connection con) throws Exception {
        System.out.print("Student ID: "); int sid = sc.nextInt();
        System.out.print("Subject ID: "); int subid = sc.nextInt();

        PreparedStatement ps = con.prepareStatement(
                "INSERT INTO course_registration VALUES (?, ?)");

        ps.setInt(1, sid);
        ps.setInt(2, subid);

        ps.executeUpdate();
        System.out.println("Registered!");
    }

    static void viewRegistrations(Connection con) throws Exception {
        Statement st = con.createStatement();
        ResultSet rs = st.executeQuery(
                "SELECT s.student_name, sub.subject_name FROM course_registration cr " +
                "JOIN student s ON cr.student_id=s.student_id " +
                "JOIN subject sub ON cr.subject_id=sub.subject_id");

        while (rs.next())
            System.out.println(rs.getString(1) + " -> " + rs.getString(2));
    }

    // ================= FACULTY =================
    static void facultyMenu(Connection con) throws Exception {
        int ch;
        do {
            System.out.println("\n--- FACULTY MENU ---");
            System.out.println("1.Enter Marks");
            System.out.println("2.My Subjects");
            System.out.println("3.Subject Results");
            System.out.println("4.Pass Percentage");
            System.out.println("5.Logout");
	    System.out.print("Choose option:");
            ch = sc.nextInt();

            switch (ch) {
                case 1: marksMenu(con); break;
                case 2: mySubjects(con); break;
                case 3: viewSubjectResults(con); break;
                case 4: subjectPassPercentage(con); break;
            }

        } while (ch != 5);

        role = "";
        loggedInUser = "";
    }

    static void mySubjects(Connection con) throws Exception {
        PreparedStatement ps = con.prepareStatement(
                "SELECT sub.subject_name FROM faculty_subject fs " +
                "JOIN subject sub ON fs.subject_id=sub.subject_id " +
                "WHERE fs.faculty_username=?");

        ps.setString(1, loggedInUser);
        ResultSet rs = ps.executeQuery();

        while (rs.next())
            System.out.println(rs.getString(1));
    }

    static void marksMenu(Connection con) throws Exception {

        System.out.print("Subject ID: ");
        int subid = sc.nextInt();

        PreparedStatement check = con.prepareStatement(
                "SELECT * FROM faculty_subject WHERE faculty_username=? AND subject_id=?");

        check.setString(1, loggedInUser);
        check.setInt(2, subid);

        ResultSet rs = check.executeQuery();

        if (!rs.next()) {
            System.out.println(" You are NOT assigned to this subject!");
            return;
        }

        System.out.print("Student ID: ");
        int sid = sc.nextInt();

        System.out.print("Marks: ");
        int marks = sc.nextInt();

        PreparedStatement ps = con.prepareStatement(
                "MERGE INTO marks m USING dual ON (m.student_id=? AND m.subject_id=?) " +
                "WHEN MATCHED THEN UPDATE SET m.marks=? " +
                "WHEN NOT MATCHED THEN INSERT VALUES (?, ?, ?)");

        ps.setInt(1, sid);
        ps.setInt(2, subid);
        ps.setInt(3, marks);
        ps.setInt(4, sid);
        ps.setInt(5, subid);
        ps.setInt(6, marks);

        ps.executeUpdate();

        System.out.println("Marks Saved!");
    }

    static void studentMenu(Connection con) throws Exception {
        int ch;
        do {
            System.out.println("\n--- STUDENT DASHBOARD ---");
            System.out.println("1.Full Result");
            System.out.println("2.Subject Performance");
            System.out.println("3.My Courses");
            System.out.println("4.Grade Summary");
            System.out.println("5.Logout");
            System.out.print("Choose option:");
            ch = sc.nextInt();

            switch (ch) {
                case 1: viewFullResult(con); break;
                case 2: subjectWisePerformance(con); break;
                case 3: viewMyCourses(con); break;
                case 4: gradeSummary(con); break;
            }

        } while (ch != 5);

        role = "";
        loggedInUser = "";
    }

    public static void main(String[] args) {
        try {
            Class.forName("oracle.jdbc.driver.OracleDriver");
            Connection con = DriverManager.getConnection(URL, USER, PASSWORD);

            while (true) {
                if (!login(con)) continue;

                if (role.equals("admin")) adminMenu(con);
                else if (role.equals("faculty")) facultyMenu(con);
                else studentMenu(con);

                System.out.println("\nLogged out successfully!");
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
