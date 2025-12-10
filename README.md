# student_management_system.py
import sqlite3

# Create or connect database
conn = sqlite3.connect("students.db")
c = conn.cursor()

# Create table
c.execute('''CREATE TABLE IF NOT EXISTS students (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                name TEXT NOT NULL,
                age INTEGER,
                grade TEXT
            )''')
conn.commit()

# Add student
def add_student():
    name = input("Enter student name: ").strip()
    if not name:
        print("Name cannot be empty!\n")
        return
    try:
        age = int(input("Enter student age: "))
    except ValueError:
        print("Invalid age! Please enter a number.\n")
        return
    grade = input("Enter student grade: ").strip()
    c.execute("INSERT INTO students (name, age, grade) VALUES (?, ?, ?)", (name, age, grade))
    conn.commit()
    print("Student added successfully!\n")

# View all students
def view_students():
    c.execute("SELECT * FROM students")
    students = c.fetchall()
    if students:
        print("\n--- Student List ---")
        for student in students:
            print(f"ID: {student[0]}, Name: {student[1]}, Age: {student[2]}, Grade: {student[3]}")
    else:
        print("No students found.")
    print()

# Update student
def update_student():
    try:
        student_id = int(input("Enter student ID to update: "))
    except ValueError:
        print("Invalid ID!\n")
        return
    c.execute("SELECT * FROM students WHERE id=?", (student_id,))
    student = c.fetchone()
    if student:
        name = input(f"Enter new name ({student[1]}): ").strip() or student[1]
        try:
            age_input = input(f"Enter new age ({student[2]}): ")
            age = int(age_input) if age_input else student[2]
        except ValueError:
            print("Invalid age input! Keeping old age.")
            age = student[2]
        grade = input(f"Enter new grade ({student[3]}): ").strip() or student[3]
        c.execute("UPDATE students SET name=?, age=?, grade=? WHERE id=?", (name, age, grade, student_id))
        conn.commit()
        print("Student updated successfully!\n")
    else:
        print("Student not found!\n")

# Delete student
def delete_student():
    try:
        student_id = int(input("Enter student ID to delete: "))
    except ValueError:
        print("Invalid ID!\n")
        return
    c.execute("SELECT * FROM students WHERE id=?", (student_id,))
    if c.fetchone():
        c.execute("DELETE FROM students WHERE id=?", (student_id,))
        conn.commit()
        print("Student deleted successfully!\n")
    else:
        print("Student not found!\n")

# Search student by name
def search_student():
    name = input("Enter student name to search: ").strip()
    c.execute("SELECT * FROM students WHERE name LIKE ?", ('%'+name+'%',))
    students = c.fetchall()
    if students:
        print("\n--- Search Results ---")
        for student in students:
            print(f"ID: {student[0]}, Name: {student[1]}, Age: {student[2]}, Grade: {student[3]}")
    else:
        print("No student found with that name.")
    print()

# Main menu
def main():
    while True:
        print("=== Student Management System ===")
        print("1. Add Student")
        print("2. View Students")
        print("3. Update Student")
        print("4. Delete Student")
        print("5. Search Student")
        print("6. Exit")
        choice = input("Enter your choice: ").strip()

        if choice == '1':
            add_student()
        elif choice == '2':
            view_students()
        elif choice == '3':
            update_student()
        elif choice == '4':
            delete_student()
        elif choice == '5':
            search_student()
        elif choice == '6':
            print("Exiting...")
            break
        else:
            print("Invalid choice! Try again.\n")

    conn.close()

if __name__ == "__main__":
    main()
