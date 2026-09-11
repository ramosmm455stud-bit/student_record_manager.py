# student_record_manager.py
StudentRecordManager
"""
STUDENT RECORD MANAGER
-----------------------
A console-based application that manages student records using a
manually implemented Dynamic Array ADT (no built-in collection
classes such as list.append()-based growth, ArrayList, LinkedList,
Vector, Stack, or Queue are used to implement the dynamic behavior).

Data Structure: Dynamic Array ADT
Initial Capacity: 5
Growth Strategy: New Capacity = Old Capacity * 2
"""


class Student:
    """Represents a single student record."""

    def __init__(self, student_id, name, course, year_level):
        self.student_id = student_id
        self.name = name
        self.course = course
        self.year_level = year_level

    def __str__(self):
        return (
            f"Student ID   : {self.student_id}\n"
            f"Student Name : {self.name}\n"
            f"Course       : {self.course}\n"
            f"Year Level   : {self.year_level}"
        )


class DynamicArray:
    """
    A Dynamic Array ADT implemented manually.

    Internally uses a fixed-size Python list as raw storage. Growth is
    NOT done via list.append() -- instead, when the array is full, a
    brand new (larger) internal array is created and existing elements
    are copied over one by one, doubling the capacity each time.
    """

    def __init__(self, initial_capacity=5):
        self.capacity = initial_capacity
        self.count = 0
        self.array = [None] * self.capacity

    def is_full(self):
        return self.count == self.capacity

    def is_empty(self):
        return self.count == 0

    def resize(self):
        """Doubles the capacity of the internal array and copies elements over."""
        old_capacity = self.capacity
        new_capacity = self.capacity * 2
        new_array = [None] * new_capacity

        for i in range(self.count):
            new_array[i] = self.array[i]

        self.array = new_array
        self.capacity = new_capacity
        print(f"[Array is full. Capacity increased from {old_capacity} to {new_capacity}.]")

    def add(self, item):
        """Adds an item to the end of the array, resizing if necessary."""
        if self.is_full():
            self.resize()
        self.array[self.count] = item
        self.count += 1

    def get(self, index):
        """Returns the item at the given index."""
        if 0 <= index < self.count:
            return self.array[index]
        raise IndexError("Index out of range.")

    def set(self, index, item):
        """Replaces the item at the given index."""
        if 0 <= index < self.count:
            self.array[index] = item
        else:
            raise IndexError("Index out of range.")

    def remove_at(self, index):
        """Removes the item at the given index and shifts remaining elements left."""
        if 0 <= index < self.count:
            for i in range(index, self.count - 1):
                self.array[i] = self.array[i + 1]
            self.array[self.count - 1] = None
            self.count -= 1
        else:
            raise IndexError("Index out of range.")

    def size(self):
        return self.count

    def get_capacity(self):
        return self.capacity

    def display(self):
        """Prints all elements currently stored in the array."""
        if self.is_empty():
            print("No students found.")
            return
        for i in range(self.count):
            print("-" * 32)
            print(self.array[i])
        print("-" * 32)


class StudentRecordManager:
    """Handles the application logic for managing student records."""

    def __init__(self):
        self.records = DynamicArray(initial_capacity=5)
        self._load_sample_data()

    def _load_sample_data(self):
        """Preloads a few sample students so the array isn't empty on startup."""
        sample_students = [
            ("2026-001", "Juan Dela Cruz", "BSIT", 2),
            ("2026-002", "Maria Santos", "BSIT", 2),
            ("2026-003", "Pedro Reyes", "BSIT", 3),
            ("2026-004", "Ana Lopez", "BSIT", 1),
            ("2026-005", "Mark Tan", "BSCS", 2),
            ("2026-006", "Liza Cruz", "BSCS", 1),
        ]
        for student_id, name, course, year_level in sample_students:
            self.records.add(Student(student_id, name, course, year_level))

    def _find_index_by_id(self, student_id):
        for i in range(self.records.size()):
            if self.records.get(i).student_id == student_id:
                return i
        return -1

    @staticmethod
    def _get_valid_int(prompt):
        while True:
            value = input(prompt).strip()
            if value.isdigit():
                return int(value)
            print("Invalid input. Please enter a whole number.")

    def add_student(self):
        student_id = input("Enter Student ID: ").strip()
        if self._find_index_by_id(student_id) != -1:
            print("A student with that ID already exists.")
            return

        name = input("Enter Student Name: ").strip()
        course = input("Enter Course: ").strip()
        year_level = self._get_valid_int("Enter Year Level: ")

        new_student = Student(student_id, name, course, year_level)
        self.records.add(new_student)
        print(f"Student '{name}' added successfully.")

    def display_students(self):
        print("\n=== STUDENT LIST ===")
        self.records.display()

    def search_student(self):
        student_id = input("Enter Student ID to search: ").strip()
        index = self._find_index_by_id(student_id)
        if index == -1:
            print("Student not found.")
        else:
            print("\n=== STUDENT FOUND ===")
            print(self.records.get(index))

    def update_student(self):
        student_id = input("Enter Student ID to update: ").strip()
        index = self._find_index_by_id(student_id)
        if index == -1:
            print("Student not found.")
            return

        student = self.records.get(index)
        print("Leave a field blank to keep its current value.")

        name = input(f"New Name [{student.name}]: ").strip()
        course = input(f"New Course [{student.course}]: ").strip()
        year_input = input(f"New Year Level [{student.year_level}]: ").strip()

        if name:
            student.name = name
        if course:
            student.course = course
        if year_input:
            if year_input.isdigit():
                student.year_level = int(year_input)
            else:
                print("Invalid year level input, keeping old value.")

        self.records.set(index, student)
        print("Student record updated successfully.")

    def remove_student(self):
        student_id = input("Enter Student ID to remove: ").strip()
        index = self._find_index_by_id(student_id)
        if index == -1:
            print("Student not found.")
            return
        self.records.remove_at(index)
        print("Student removed successfully.")

    def display_array_info(self):
        print(f"Current number of students : {self.records.size()}")
        print(f"Current array capacity     : {self.records.get_capacity()}")


def print_menu():
    print("=" * 36)
    print("      STUDENT RECORD MANAGER")
    print("=" * 36)
    print("1. Add Student")
    print("2. Display Students")
    print("3. Search Student")
    print("4. Update Student")
    print("5. Remove Student")
    print("6. Display Array Information")
    print("7. Exit")


def main():
    manager = StudentRecordManager()

    while True:
        print_menu()
        choice = input("Enter your choice: ").strip()

        if choice == "1":
            manager.add_student()
        elif choice == "2":
            manager.display_students()
        elif choice == "3":
            manager.search_student()
        elif choice == "4":
            manager.update_student()
        elif choice == "5":
            manager.remove_student()
        elif choice == "6":
            manager.display_array_info()
        elif choice == "7":
            print("Exiting program. Goodbye!")
            break
        else:
            print("Invalid choice. Please enter a number from 1 to 7.")

        print()  # spacing between operations


if __name__ == "__main__":
    main()
    
