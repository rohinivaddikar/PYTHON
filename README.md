# employee_task_tracker.py
import json
import datetime
import matplotlib.pyplot as plt

# File to store employee tasks
TASK_FILE = "tasks.json"

# Load tasks from JSON file
def load_tasks():
    try:
        with open(TASK_FILE, "r") as f:
            return json.load(f)
    except FileNotFoundError:
        return {}

# Save tasks to JSON file
def save_tasks(tasks):
    with open(TASK_FILE, "w") as f:
        json.dump(tasks, f, indent=4)

# Add a new task for an employee
def add_task(employee_name, task_name, due_date):
    tasks = load_tasks()
    task = {
        "task_name": task_name,
        "due_date": due_date,
        "status": "Pending"
    }
    if employee_name in tasks:
        tasks[employee_name].append(task)
    else:
        tasks[employee_name] = [task]
    save_tasks(tasks)
    print(f"Task '{task_name}' added for {employee_name}!")

# Update task status
def update_task(employee_name, task_index, status):
    tasks = load_tasks()
    try:
        tasks[employee_name][task_index]["status"] = status
        save_tasks(tasks)
        print(f"Task status updated for {employee_name}.")
    except (KeyError, IndexError):
        print("Invalid employee or task index!")

# Display all tasks
def display_tasks():
    tasks = load_tasks()
    for emp, emp_tasks in tasks.items():
        print(f"\nEmployee: {emp}")
        for i, t in enumerate(emp_tasks):
            print(f"{i}. {t['task_name']} | Due: {t['due_date']} | Status: {t['status']}")

# Simple analytics: Task completion rate
def task_analytics():
    tasks = load_tasks()
    total_tasks = 0
    completed_tasks = 0
    for emp_tasks in tasks.values():
        for t in emp_tasks:
            total_tasks += 1
            if t['status'].lower() == "completed":
                completed_tasks += 1
    if total_tasks == 0:
        print("No tasks available for analytics.")
        return
    completion_rate = (completed_tasks / total_tasks) * 100
    print(f"Task Completion Rate: {completion_rate:.2f}%")
    
    # Plot a pie chart
    labels = ['Completed', 'Pending']
    sizes = [completed_tasks, total_tasks - completed_tasks]
    colors = ['green', 'red']
    plt.pie(sizes, labels=labels, colors=colors, autopct='%1.1f%%')
    plt.title("Task Completion Overview")
    plt.show()

# Example usage
if __name__ == "__main__":
    while True:
        print("\n1. Add Task\n2. Update Task Status\n3. Display Tasks\n4. Show Analytics\n5. Exit")
        choice = input("Enter choice: ")
        if choice == "1":
            emp = input("Employee Name: ")
            task = input("Task Name: ")
            due = input("Due Date (YYYY-MM-DD): ")
            add_task(emp, task, due)
        elif choice == "2":
            emp = input("Employee Name: ")
            idx = int(input("Task Index: "))
            status = input("Status (Pending/Completed): ")
            update_task(emp, idx, status)
        elif choice == "3":
            display_tasks()
        elif choice == "4":
            task_analytics()
        elif choice == "5":
            break
        else:
            print("Invalid choice!")
