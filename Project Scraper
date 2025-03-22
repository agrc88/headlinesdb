import requests
from bs4 import BeautifulSoup
import mysql.connector
from datetime import datetime
import os
from dotenv import load_dotenv

# MySQL Connection
load_dotenv()

try:
   print("Connecting to DB...")
   db = mysql.connector.connect(
       host=os.getenv("DB_HOST"),
       user=os.getenv("DB_USER"),
       password=os.getenv("DB_PASSWORD"),
       database=os.getenv("DB_NAME"),
   )
   cursor = db.cursor()
   print("Successful connect to DB")
except mysql.connector.Error as err:
   print(f"Error: {err}")
   exit()

# Fetch NPR Text website
url = "https://text.npr.org/"
try:
    print(f"Fetching data from {url}...")
    response = requests.get(url)
    response.raise_for_status()  # Raise an error if the request fails
    print("Successfully fetched webpage.")
except requests.RequestException as e:
    print(f"Error fetching the webpage: {e}")
    exit()

# Parse HTML with BeautifulSoup
try:
    soup = BeautifulSoup(response.text, "html.parser")
    print("Successfully parsed HTML.")
except Exception as e:
    print(f"Error parsing HTML: {e}")
    exit()

# Extract headlines
headline_list = soup.find("ul")  # Locate the first <ul> on the page
if not headline_list:
    print("No <ul> found. Check the website structure.")
    exit()

headlines = headline_list.find_all("li")  # Find all <li> inside the <ul>

if not headlines:
    print("No headlines found. Check the website structure.")
    print("Here is a sample of the HTML content for debugging:\n")
    print(soup.prettify()[:1000])  # Print first 1000 characters of the HTML for analysis
    exit()

print(f"Found {len(headlines)} headlines.")

# Get today's date
postdate = datetime.today().strftime('%Y-%m-%d')

# Insert headlines into MySQL
for index, h in enumerate(headlines, start=1):
    headline_text = h.get_text().strip()
    print(f"Inserting headline {index}: {headline_text}")

    try:
        sql = "INSERT INTO headlines (headline, postdate) VALUES (%s, %s)"
        cursor.execute(sql, (headline_text, postdate))
        db.commit()
        print(f"Successfully inserted headline {index}.")
    except mysql.connector.Error as err:
        print(f"Error inserting headline {index}: {err}")

# Close database connection
cursor.close()
db.close()
print("Database connection closed.")
