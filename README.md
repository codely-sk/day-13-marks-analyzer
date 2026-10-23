
import csv
import os

FILE_NAME = "student_marks.csv"
SUBJECTS = ["Python", "Maths", "English"]


def create_sample_file():
    if not os.path.exists(FILE_NAME):
        students = [
            ["Name", *SUBJECTS],
            ["Aarav", 85, 78, 92],
            ["Riya", 95, 88, 91],
            ["Kabir", 67, 74, 80],
            ["Meera", 89, 96, 94],
            ["Dev", 55, 62, 70]
        ]

        with open(FILE_NAME, "w", newline="") as file:
            writer = csv.writer(file)
            writer.writerows(students)


def load_students():
    students = []

    try:
        with open(FILE_NAME, "r", newline="") as file:
            reader = csv.DictReader(file)

            for row in reader:
                marks = [float(row[subject]) for subject in SUBJECTS]

                if any(mark < 0 or mark > 100 for mark in marks):
                    print("Invalid marks found for", row["Name"])
                    continue

                students.append({
                    "name": row["Name"],
                    "marks": marks
                })

    except (OSError, ValueError, KeyError):
        print("Could not read the CSV file. Check its format.")

    return students


def calculate_grade(average):
    if average >= 90:
        return "A"
    elif average >= 80:
        return "B"
    elif average >= 70:
        return "C"
    elif average >= 60:
        return "D"
    else:
        return "F"


def show_report(students):
    if not students:
        print("No valid student records found.")
        return

    print("\n===== STUDENT PERFORMANCE REPORT =====")

    for student in students:
        total = sum(student["marks"])
        average = total / len(SUBJECTS)
        grade = calculate_grade(average)

        student["total"] = total
        student["average"] = average
        student["grade"] = grade

        print(f"\nName: {student['name']}")
        print(f"Total: {total:.1f}/{len(SUBJECTS) * 100}")
        print(f"Average: {average:.2f}")
        print(f"Grade: {grade}")

    ranked_students = sorted(
        students,
        key=lambda student: student["average"],
        reverse=True
    )

    print("\n===== CLASS RANKING =====")

    for rank, student in enumerate(ranked_students, start=1):
        print(
            f"{rank}. {student['name']} - "
            f"{student['average']:.2f}%"
        )

    topper = ranked_students[0]
    print(
        f"\nTop student: {topper['name']} "
        f"({topper['average']:.2f}%)"
    )


create_sample_file()
students = load_students()
show_report(students)
