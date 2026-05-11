# url-shortener
Python URL Shortener CLI Project
"""
🔗 URL Shortener CLI Project
Author: Charitha Reddy Soreddy

Features:
1. Shorten URLs
2. Expand Short URLs
3. View All URLs
4. Delete URLs
5. Search URLs
6. Statistics
7. Save data permanently using JSON
"""

import json
import random
import string
import os
from datetime import datetime

# ---------------- CONFIG ---------------- #

DATA_FILE = "urls.json"
BASE_URL = "https://short.ly/"
CODE_LENGTH = 6

# ---------------- LOAD DATA ---------------- #

def load_data():
    if os.path.exists(DATA_FILE):
        with open(DATA_FILE, "r") as file:
            return json.load(file)
    return {}

# ---------------- SAVE DATA ---------------- #

def save_data(data):
    with open(DATA_FILE, "w") as file:
        json.dump(data, file, indent=4)

# ---------------- GENERATE SHORT CODE ---------------- #

def generate_code(data):

    characters = string.ascii_letters + string.digits

    while True:
        code = "".join(random.choices(characters, k=CODE_LENGTH))

        if code not in data:
            return code

# ---------------- VALIDATE URL ---------------- #

def is_valid_url(url):

    return url.startswith("http://") or url.startswith("https://")

# ---------------- SHORTEN URL ---------------- #

def shorten_url(data):

    print("\n--- SHORTEN URL ---")

    long_url = input("Enter Long URL: ").strip()

    if not is_valid_url(long_url):
        print("❌ Invalid URL")
        print("URL must start with http:// or https://")
        return

    # Check existing URL
    for code, info in data.items():

        if info["original"] == long_url:

            print("\n✅ URL already shortened!")
            print("Short URL:", BASE_URL + code)
            return

    code = generate_code(data)

    data[code] = {
        "original": long_url,
        "short": BASE_URL + code,
        "visits": 0,
        "created": datetime.now().strftime("%Y-%m-%d %H:%M")
    }

    save_data(data)

    print("\n✅ URL Shortened Successfully!")
    print("Short URL:", BASE_URL + code)

# ---------------- EXPAND URL ---------------- #

def expand_url(data):

    print("\n--- EXPAND URL ---")

    entry = input("Enter Short URL or Code: ").strip()

    code = entry.replace(BASE_URL, "")

    if code in data:

        data[code]["visits"] += 1

        save_data(data)

        print("\n✅ Original URL:")
        print(data[code]["original"])

        print("👁 Total Visits:", data[code]["visits"])

    else:
        print("❌ URL not found")

# ---------------- VIEW ALL URLS ---------------- #

def view_urls(data):

    print("\n--- ALL URLS ---")

    if not data:
        print("No URLs stored")
        return

    for code, info in data.items():

        print("\n---------------------------")
        print("Code:", code)
        print("Short URL:", info["short"])
        print("Original URL:", info["original"])
        print("Visits:", info["visits"])
        print("Created:", info["created"])

# ---------------- DELETE URL ---------------- #

def delete_url(data):

    print("\n--- DELETE URL ---")

    entry = input("Enter Short URL or Code: ").strip()

    code = entry.replace(BASE_URL, "")

    if code in data:

        del data[code]

        save_data(data)

        print("🗑 URL deleted successfully")

    else:
        print("❌ URL not found")

# ---------------- SEARCH URL ---------------- #

def search_url(data):

    print("\n--- SEARCH URL ---")

    keyword = input("Enter keyword: ").lower()

    found = False

    for code, info in data.items():

        if keyword in info["original"].lower():

            found = True

            print("\n---------------------------")
            print("Code:", code)
            print("Original:", info["original"])
            print("Short:", info["short"])

    if not found:
        print("❌ No matching URLs")

# ---------------- STATISTICS ---------------- #

def statistics(data):

    print("\n--- STATISTICS ---")

    if not data:
        print("No data available")
        return

    total_urls = len(data)

    total_visits = sum(info["visits"] for info in data.values())

    print("📊 Total URLs:", total_urls)
    print("👁 Total Visits:", total_visits)

    # Most visited URL
    top = max(data.items(), key=lambda item: item[1]["visits"])

    print("\n🏆 Most Visited URL:")
    print("Short URL:", top[1]["short"])
    print("Visits:", top[1]["visits"])

# ---------------- MENU ---------------- #

def menu():

    data = load_data()

    while True:

        print("\n")
        print("====================================")
        print("     🔗 URL SHORTENER SYSTEM")
        print("====================================")
        print("1. Shorten URL")
        print("2. Expand URL")
        print("3. View All URLs")
        print("4. Delete URL")
        print("5. Search URL")
        print("6. Statistics")
        print("7. Exit")
        print("====================================")

        choice = input("Enter your choice (1-7): ")

        if choice == "1":
            shorten_url(data)

        elif choice == "2":
            expand_url(data)

        elif choice == "3":
            view_urls(data)

        elif choice == "4":
            delete_url(data)

        elif choice == "5":
            search_url(data)

        elif choice == "6":
            statistics(data)

        elif choice == "7":

            print("\n👋 Thank you for using URL Shortener!")
            break

        else:
            print("❌ Invalid choice")

# ---------------- MAIN ---------------- #

if __name__ == "__main__":
    menu()
