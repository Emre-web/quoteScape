# Selenium Web Scraper Project

This project is a Python-based web scraper that extracts quotes, authors, and tags from the website [Quotes to Scrape](https://quotes.toscrape.com/js/). The scraper utilizes the Selenium library to automate browser interactions and retrieve dynamic content. The scraped data is stored in an Excel file for further analysis.

---

## Project Structure

### Key Files:
1. **Python Script**: The main script contains the scraping logic.
2. **Output File**: An Excel file named `quotes.xlsx` where the scraped data is saved.

---

## Features

1. **Automated Login**:
   - The script simulates user login by entering a username and password.
   - It clicks the login button to authenticate.

2. **Dynamic Content Handling**:
   - The script uses Selenium to handle JavaScript-rendered content, ensuring all quotes on the page are retrieved.

3. **Pagination Support**:
   - The scraper navigates through all available pages using the "Next" button until no further pages exist.

4. **Data Extraction**:
   - Quotes: Extracted as strings.
   - Authors: The names of the authors of the quotes.
   - Tags: All tags associated with each quote, formatted as a comma-separated string.

5. **Data Storage**:
   - The extracted data is saved to an Excel file using the Pandas library.

---

## Dependencies

To run this project, ensure the following Python libraries are installed:

- `selenium`
- `pandas`
- `openpyxl`

You can install these using pip:
```bash
pip install selenium pandas openpyxl
```

---

## Code Explanation

### Importing Required Libraries
```python
from selenium import webdriver
from selenium.webdriver.common.by import By
import pandas as pd
import openpyxl
```
- `selenium`: For browser automation.
- `pandas`: To organize and save data.
- `openpyxl`: To write data to Excel.

### Initializing the Data Dictionary
```python
quote_dict = {'quote': [], 'author': [], 'tags': []}
```
This dictionary stores the scraped data.

### Login Function
```python
def login(the_driver):
    login_button = the_driver.find_element(By.XPATH, '//a[text()="Login"]')
    login_button.click()

    username_input = the_driver.find_element(By.ID, 'username')
    username_input.send_keys('kullaniciadi')

    password_input = the_driver.find_element(By.ID, 'password')
    password_input.send_keys('sifre')

    log_me_in = the_driver.find_element(By.CSS_SELECTOR, 'input[value="Login"]')
    log_me_in.click()
```
This function automates the login process by finding and interacting with the login form elements.

### Scrape Page Function
```python
def scrape_page(the_driver):
    quotes = the_driver.find_elements(By.CLASS_NAME, 'quote')

    for element in quotes:
        quote = element.find_element(By.CSS_SELECTOR, 'span.text').text
        quote_dict['quote'].append(quote)

        author = element.find_element(By.CSS_SELECTOR, 'small.author').text
        quote_dict['author'].append(author)

        tag_container = element.find_element(By.CLASS_NAME, 'tags')
        a_tags = tag_container.find_elements(By.CSS_SELECTOR, 'a')
        tags = ''
        for i, a_tag in enumerate(a_tags):
            if i == len(a_tags) - 1:
                tag = a_tag.text
            else:
                tag = a_tag.text + ', '
            tags += tag
        quote_dict['tags'].append(tags)
```
This function:
- Extracts quotes, authors, and tags from each page.
- Appends the data to the `quote_dict` dictionary.

### Main Script
```python
url = 'https://quotes.toscrape.com/js/'
options = webdriver.ChromeOptions()

options.add_argument('--start-maximized')  # Open the browser in maximized mode
options.add_experimental_option('detach', True)  # Keep the browser open after the script ends

driver = webdriver.Chrome(options=options)
driver.get(url)

login(driver)

while True:
    scrape_page(driver)
    try:
        next_button = driver.find_element(By.CSS_SELECTOR, 'li.next a')
        next_button.click()
    except:
        break

df = pd.DataFrame(quote_dict)
df.to_excel('quotes.xlsx')
```
- Initializes the Selenium WebDriver.
- Logs into the website.
- Iterates through all pages, scraping data until there are no more pages.
- Saves the scraped data to `quotes.xlsx`.

---

## Usage

1. **Setup WebDriver**:
   - Download the appropriate version of ChromeDriver for your browser from [here](https://sites.google.com/chromium.org/driver/).
   - Ensure the `chromedriver` executable is in your system's PATH or specify its location in the script.

2. **Run the Script**:
   - Save the code to a `.py` file (e.g., `scraper.py`).
   - Run the script using:
     ```bash
     python scraper.py
     ```

3. **View Results**:
   - Open the generated `quotes.xlsx` file to view the extracted data.

---

## Notes

- Ensure you replace `'kullaniciadi'` and `'sifre'` in the `login` function with valid credentials.
- The script is configured to work with the JavaScript-enabled version of the website (`https://quotes.toscrape.com/js/`).
- Use the `--detach` option in Selenium for debugging purposes to keep the browser open after the script finishes.

---



