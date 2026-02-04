# codeintern_python_Personal-Expense-Tracker
import csv
import os
from datetime import datetime
import matplotlib.pyplot as plt

FILE_NAME = "expenses.csv"

if not os.path.exists(FILE_NAME):
    with open(FILE_NAME, mode='w', newline='') as file:
        writer = csv.writer(file)
        writer.writerow(["Date", "Category", "Description", "Amount"])

def add_expense():
    date = input("Enter date (YYYY-MM-DD) or press Enter for today: ")
    if date == "":
        date = datetime.now().strftime("%Y-%m-%d")

    category = input("Enter category (Food, Travel, Rent, etc.): ")
    description = input("Enter description: ")
    amount = float(input("Enter amount: "))

    with open(FILE_NAME, mode='a', newline='') as file:
        writer = csv.writer(file)
        writer.writerow([date, category, description, amount])

    print("✅ Expense added successfully!")

def read_expenses():
    expenses = []
    with open(FILE_NAME, mode='r') as file:
        reader = csv.DictReader(file)
        for row in reader:
            row["Amount"] = float(row["Amount"])
            expenses.append(row)
    return expenses

def daily_summary():
    date = input("Enter date (YYYY-MM-DD): ")
    expenses = read_expenses()
    total = sum(e["Amount"] for e in expenses if e["Date"] == date)
    print(f"📅 Total expense on {date}: ₹{total}")

def monthly_summary():
    month = input("Enter month (YYYY-MM): ")
    expenses = read_expenses()
    total = sum(e["Amount"] for e in expenses if e["Date"].startswith(month))
    print(f"📆 Total expense for {month}: ₹{total}")


def top_categories():
    expenses = read_expenses()
    category_total = {}

    for e in expenses:
        category_total[e["Category"]] = category_total.get(e["Category"], 0) + e["Amount"]

    print("\n🔥 Top Spending Categories:")
    for cat, amt in sorted(category_total.items(), key=lambda x: x[1], reverse=True):
        print(f"{cat}: ₹{amt}")

    return category_total


def visualize():
    category_total = top_categories()

    categories = list(category_total.keys())
    amounts = list(category_total.values())

    plt.figure()
    plt.bar(categories, amounts)
    plt.title("Expenses by Category")
    plt.xlabel("Category")
    plt.ylabel("Amount")
    plt.show()

    plt.figure()
    plt.pie(amounts, labels=categories, autopct="%1.1f%%")
    plt.title("Expense Distribution")
    plt.show()

def menu():
    while True:
        print("\n--- Personal Expense Tracker ---")
        print("1. Add Expense")
        print("2. Daily Summary")
        print("3. Monthly Summary")
        print("4. Top Spending Categories")
        print("5. Visualize Expenses")
        print("6. Exit")

        choice = input("Choose option: ")

        if choice == "1":
            add_expense()
        elif choice == "2":
            daily_summary()
        elif choice == "3":
            monthly_summary()
        elif choice == "4":
            top_categories()
        elif choice == "5":
            visualize()
        elif choice == "6":
            print("👋 Exiting... Thank you!")
            break
        else:
            print("❌ Invalid choice!")

menu()
