
# Web Scraping Walmart Using Python: A Step-by-Step Guide

Walmart.com is a dynamic website that uses JavaScript to display search results. However, the data you need is embedded in a script tag on the page as a JSON string. This makes scraping Walmart possible without executing JavaScript.

This guide explains two methods to scrape Walmart:
1. Using Selenium to execute JavaScript and extract data.
2. Using Python Requests and BeautifulSoup to extract JSON data directly from the script tag.

In this tutorial, we focus on the second method, which is simpler and more efficient for Walmart product scraping.

---

## Why Use ScraperAPI for Walmart Scraping?

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Environment Setup

This tutorial uses the following packages:
- [Python Requests](https://pypi.org/project/requests/) to manage HTTP requests.
- [BeautifulSoup](https://pypi.org/project/beautifulsoup4/) to parse and extract data.
- `json` for writing data to a file.
- `re` for handling regular expressions.

Install external libraries using pip:
```bash
pip install requests beautifulsoup4
```

The `json` and `re` modules are included in Python's standard library.

---

## Data Scraped from Walmart

This guide demonstrates how to scrape the following data points for each Walmart product:
- Name
- Price
- Rating
- URL

These details are stored in a JSON string within a script tag identified by the `id="__NEXT_DATA__"`.

---

## Code Walkthrough: Scraping Walmart with Python

### Step 1: Import Required Libraries
First, import the necessary libraries:
```python
import requests
from bs4 import BeautifulSoup
import json
```

### Step 2: Input Search Term
Prompt the user for a search term to make the script dynamic:
```python
search_term = input("What do you want to buy on Walmart?")
```

### Step 3: Construct the Walmart Search URL
Visit Walmart.com, perform a search, and copy the URL structure. Replace the search term in the URL with the `search_term` variable.

### Step 4: Set HTTP Headers
Walmart employs anti-scraping measures, so include headers to mimic a browser request:
```python
headers = {
    'accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8',
    'user-agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/119.0.0.0 Safari/537.36'
}
```

### Step 5: Send a GET Request
Make an HTTP GET request to the Walmart search results page:
```python
response = requests.get(url, headers=headers)
soup = BeautifulSoup(response.text, 'html.parser')
```

### Step 6: Extract JSON Data
Locate the script tag containing the JSON data using its ID and parse it:
```python
next_data = soup.find('script', {'id': '__NEXT_DATA__'}).string
json_data = json.loads(next_data)
```

### Step 7: Access Product Data
The product details are nested within the JSON object. Identify the correct keys to access the data:
```python
products = json_data["props"]["pageProps"]["initialData"]["searchResult"]["itemStacks"][0]["items"]
```

### Step 8: Loop Through Products
Iterate through each product, extracting relevant details:
```python
walmart_products = []

for product in products:
    try:
        if product["availabilityStatusV2"]["value"] == 'IN_STOCK':
            name = product["name"]
            url = "https://walmart.com" + product["canonicalUrl"]
            ratings = product["rating"]
            price = product["price"]
            walmart_products.append({
                "name": name,
                "price": price,
                "url": url,
                "ratings": ratings
            })
    except KeyError:
        continue
```

### Step 9: Save Data to a JSON File
Finally, save the extracted product details to a JSON file:
```python
with open("walmart_products.json", "w", encoding="UTF-8") as json_file:
    json.dump(walmart_products, json_file, ensure_ascii=False, indent=4)
```

---

## Limitations of This Code

1. **Does Not Scrape Product Pages:**  
   This code only extracts data from the search results page. Additional coding is required to scrape individual product pages for details like descriptions or reviews.

2. **Dynamic JSON Structure:**  
   Walmart may update its JSON structure, rendering the current code invalid. Regular maintenance and updates are necessary.

3. **Anti-Scraping Measures:**  
   Walmart employs anti-scraping techniques like CAPTCHAs. To handle large-scale projects, consider integrating tools like ScraperAPI for CAPTCHA-solving and proxy rotation.

---

## Walmart Scraper: A No-Code Alternative

If you prefer a no-code solution, try the **ScrapeHero Walmart Scraper**:
1. Visit the [ScrapeHero Marketplace](https://www.scrapehero.com/marketplace/).
2. Create an account.
3. Add the Walmart Scraper.
4. Input your search query and click "Gather Data."

---

## Wrapping Up

Scraping Walmart product data using Python Requests and BeautifulSoup is efficient for small-scale tasks. However, for more detailed data, such as reviews and descriptions, you need to scrape individual product pages. Additionally, handling Walmart's anti-scraping measures requires robust solutions like proxy rotation and CAPTCHA-solving.

For large-scale scraping or to avoid coding entirely, tools like ScraperAPI and ScrapeHero can simplify the process and improve efficiency.

👉 [Start your free trial with ScraperAPI today!](https://bit.ly/Scraperapi)
