import json
from pathlib import Path

DATA_FILE = Path("electricity_usage.json")

def load_data():
    if DATA_FILE.exists():
        try:
            return json.loads(DATA_FILE.read_text(encoding="utf-8"))
        except json.JSONDecodeError:
            return {}
    return {}

def save_data(data):
    DATA_FILE.write_text(
        json.dumps(data, indent=4),
        encoding="utf-8"
    )

def add_usage(data):
    day = input("Enter date (YYYY-MM-DD): ").strip()

    try:
        usage = float(input("Electricity usage (kWh): "))
        price = float(input("Price per kWh: "))

        if usage < 0 or price < 0:
            print("Values cannot be negative.")
            return

        data[day] = {
            "usage": usage,
            "price": price,
            "cost": usage * price
        }

        save_data(data)
        print("Usage recorded successfully.")

    except ValueError:
        print("Invalid input.")

def show_report(data):
    if not data:
        print("No records available.")
        return

    total_usage = sum(x["usage"] for x in data.values())
    total_cost = sum(x["cost"] for x in data.values())

    average = total_usage / len(data)
    highest_day = max(data, key=lambda d: data[d]["usage"])

    print("\n--- Electricity Report ---")

    for day, info in sorted(data.items()):
        print(
            f"{day}: {info['usage']:.2f} kWh | "
            f"Cost: {info['cost']:.2f}"
        )

    print("\nTotal usage:", round(total_usage, 2), "kWh")
    print("Total cost:", round(total_cost, 2))
    print("Average daily usage:", round(average, 2), "kWh")
    print("Highest consumption day:", highest_day)

def main():
    data = load_data()

    while True:
        print("\n1. Add daily usage")
        print("2. Show report")
        print("3. Exit")

        choice = input("Choose: ")

        if choice == "1":
            add_usage(data)
        elif choice == "2":
            show_report(data)
        elif choice == "3":
            break
        else:
            print("Invalid choice.")

if __name__ == "__main__":
    main()
