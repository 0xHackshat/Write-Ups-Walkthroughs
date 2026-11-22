# Web Application SQL Injection Scanner with Python

**Repository:** [kingakshat/Web-Application-SQL-Injection-Scanner-with-Python](https://github.com/kingakshat/Web-Application-SQL-Injection-Scanner-with-Python)

## Overview

SQL injection vulnerabilities are caused by the lack of sanitation of user input, allowing attackers to execute DBMS queries that could compromise the entire system. This type of web vulnerability is very common.

A successful SQL injection exploit can cause significant damage to the database and web application. For example, it can read sensitive data such as user passwords from the database, insert, modify, and even delete data.

## Python Modules Used

- `requests`: A Python library used for sending HTTP requests.
- `BeautifulSoup`: A Python library for parsing HTML and XML documents, useful for web scraping.
- `sys`: A module providing access to some variables used or maintained by the Python interpreter and to functions that interact strongly with the interpreter.
- `urllib.parse`: A Python module to manipulate URLs and deal with query strings.

## Under Progress

We are currently working on integrating Nmap to quickly scan web servers for SQL injection vulnerabilities. With the network-scripts Nmap, we can also include Web Application-based NSE scripts like:

- `http-csrf`
- `http-dombased-xss`
- `http-stored-xss`
- `http-phpmyadmin-dir-traversal`
- `http-sql-injection`
- `http-enum`

These are useful for identifying vulnerabilities in web applications within a short span of time. Stay tuned for updates!

## Main Script (`main.py`)

The script retrieves HTML forms from a target page, extracts form actions, methods, and inputs, then submits basic quote-based test values. It checks response content for common SQL error strings.

```python
import requests
from bs4 import BeautifulSoup
import sys
from urllib.parse import urljoin

s = requests.Session()
s.headers["User-Agent"] = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/103.0.0.0 Safari/537.36"

# Function to get all forms
def get_forms(url):
    soup = BeautifulSoup(s.get(url).content, "html.parser")
    return soup.find_all("form")

def form_details(form):
    detailsOfForm = {}
    action = form.attrs.get("action")
    method = form.attrs.get("method", "get")
    inputs = []

    for input_tag in form.find_all("input"):
        input_type = input_tag.attrs.get("type", "text")
        input_name = input_tag.attrs.get("name")
        input_value = input_tag.attrs.get("value", "")
        inputs.append({
            "type": input_type,
            "name": input_name,
            "value": input_value,
        })

    detailsOfForm['action'] = action
    detailsOfForm['method'] = method
    detailsOfForm['inputs'] = inputs
    return detailsOfForm

def vulnerable(response):
    errors = {
        "quoted string not properly terminated",
        "unclosed quotation mark after the charachter string",
        "you have an error in you SQL syntax"
    }

    for error in errors:
        if error in response.content.decode().lower():
            return True
    return False

def sql_injection_scan(url):
    forms = get_forms(url)
    print(f"[+] Detected {len(forms)} forms on {url}.")

    for form in forms:
        details = form_details(form)

        for i in "\\\"'":
            data = {}

            for input_tag in details["inputs"]:
                if input_tag["type"] == "hidden" or input_tag["value"]:
                    data[input_tag['name']] = input_tag["value"] + i
                elif input_tag["type"] != "submit":
                    data[input_tag['name']] = f"test{i}"

            print(url)
            form_details(form)

            if details["method"] == "post":
                res = s.post(url, data=data)
            elif details["method"] == "get":
                res = s.get(url, params=data)

            if vulnerable(res):
                print("SQL injection attack vulnerability in link: ", url)
            else:
                print("No SQL injection attack vulnerability detected")
            break

if __name__ == "__main__":
    urlToBeChecked = "https://juice-shop.herokuapp.com/#/login"
    sql_injection_scan(urlToBeChecked)
```

## Contribution

- Contributions and feedback are welcome! If you have suggestions for improvement or found errors, feel free to create pull requests or raise issues.

## Disclaimer

This project is developed for educational purposes only. Ensure that you have appropriate authorization before implementing any techniques or using tools described herein.

## Connect with Us

Connect with us on GitHub to learn more about the project and contributions to web application development.

**Original repository:** [GitHub](https://github.com/kingakshat/Web-Application-SQL-Injection-Scanner-with-Python)
