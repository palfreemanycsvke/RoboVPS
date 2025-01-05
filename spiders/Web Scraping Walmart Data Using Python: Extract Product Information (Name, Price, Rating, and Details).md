
# Web Scraping Walmart Data Using Python: Extract Product Information (Name, Price, Rating, and Details)

## Introduction

Scraping Walmart's product data can have multiple use cases. As one of the largest retailers in the USA, Walmart offers extensive public product data, making it a valuable source for price tracking, product trends, and customer reviews.

By scraping Walmart prices, you can monitor pricing trends over time and make informed purchasing decisions. In this tutorial, we will learn how to use Python to scrape Walmart product information such as name, price, rating, and details.

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Why Use Python for Web Scraping?

Python is one of the most widely used programming languages for web scraping due to its simplicity, flexibility, and robust libraries. Even beginners can quickly learn Python and leverage its powerful scraping tools like:

- **Requests:** Helps establish HTTP connections with the target website.
- **BeautifulSoup:** Parses HTML data, making it easier to extract required elements.

With a vibrant community on forums like StackOverflow and GitHub, Python also provides support when debugging errors.

---

## Setting Up the Environment

To get started, create a folder for the project and install the required libraries:

1. **Requests**: Makes HTTP requests to Walmart's website.
2. **BeautifulSoup**: Parses and extracts specific elements from HTML.

Install these libraries using pip:

```bash
pip install requests
pip install beautifulsoup4
```

Next, create a Python file in the folder for your scraping code. For this tutorial, we will scrape data from a Walmart product page like [this Samsung TV](https://www.walmart.com/ip/SAMSUNG-58-Class-4K-Crystal-UHD-2160P-LED-Smart-TV-with-HDR-UN58TU7000/820835173). Our target data includes:

- **Name**
- **Price**
- **Rating**
- **Product Details**

---

## Analyzing Walmart’s HTML Structure

Before coding, inspect the target Walmart page's HTML structure to locate the elements containing the required data:

- **Name:** Found under the `<h1>` tag with the `itemprop` attribute.
- **Price:** Found under the `<span>` tag with the `itemprop="price"` attribute.
- **Rating:** Found under the `<span>` tag with the class `rating-number`.
- **Product Details:** Found under the `<div>` tag with the class `dangerous-html`.

Below are example screenshots for locating these elements in the HTML:

### Product Name
![Name](https://www.scrapingdog.com/wp-content/uploads/2024/08/walmart-2.png)

### Price
![Price](https://www.scrapingdog.com/wp-content/uploads/2024/08/walmart-3.png)

### Rating
![Rating](https://www.scrapingdog.com/wp-content/uploads/2024/08/walmart-4-1024x281.png)

---

## Bypassing Captchas and Scraping Data

Walmart uses captchas to block bots, but this can be bypassed by sending proper HTTP headers that mimic a legitimate browser request. Below is an example of setting headers for a GET request:

```python
import requests
from bs4 import BeautifulSoup

headers = {
    "Referer": "https://www.google.com",
    "Connection": "Keep-Alive",
    "Accept-Language": "en-US,en;q=0.9",
    "Accept-Encoding": "gzip, deflate, br",
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8",
    "User-Agent": "Mozilla/5.0 (iPad; CPU OS 9_3_5 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Version/9.0 Mobile/13G36 Safari/601.1"
}

target_url = "https://www.walmart.com/ip/SAMSUNG-58-Class-4K-Crystal-UHD-2160P-LED-Smart-TV-with-HDR-UN58TU7000/820835173"
response = requests.get(target_url, headers=headers)
soup = BeautifulSoup(response.text, 'html.parser')
```

### Extracting Product Details

Using the inspected HTML structure, the following code extracts the required data:

```python
product_data = {}

try:
    product_data["name"] = soup.find("h1", {"itemprop": "name"}).text
except:
    product_data["name"] = None

try:
    product_data["price"] = soup.find("span", {"itemprop": "price"}).text.replace("Now ", "")
except:
    product_data["price"] = None

try:
    product_data["rating"] = soup.find("span", {"class": "rating-number"}).text.strip("()")
except:
    product_data["rating"] = None

print(product_data)
```

---

## Handling Dynamic Content

Walmart uses the **Next.js framework**, which loads some content dynamically via JavaScript. This means certain elements, like **Product Details**, may not be available in the static HTML response.

To scrape dynamic content, locate the `<script>` tag with the `id="__NEXT_DATA__"` and extract the JSON data:

```python
import json

script_tag = soup.find("script", {"id": "__NEXT_DATA__"})
json_data = json.loads(script_tag.text)

try:
    product_data["details"] = json_data['props']['pageProps']['initialData']['data']['product']['shortDescription']
except:
    product_data["details"] = None

print(product_data)
```

---

## Overcoming Blocks with ScraperAPI

If you plan to scrape thousands or millions of pages, Walmart will likely block your IP address. To avoid this, use **ScraperAPI** to manage proxy rotation and bypass captchas. Here’s how to integrate ScraperAPI:

1. **Sign Up for ScraperAPI**: [Start here](https://bit.ly/Scraperapi) and get your free trial with 1000 API calls.
2. **Modify Your Code**: Replace the Walmart URL with ScraperAPI’s URL and include your API key:

```python
api_url = f"https://api.scraperapi.com?api_key=SCRAPE9837861&url={target_url}"
response = requests.get(api_url)
soup = BeautifulSoup(response.text, 'html.parser')
```

---

## Full Scraping Code

Below is the complete code to scrape Walmart product information:

```python
import requests
from bs4 import BeautifulSoup
import json

# ScraperAPI URL
api_url = "https://api.scraperapi.com?api_key=SCRAPE9837861&url=https://www.walmart.com/ip/SAMSUNG-58-Class-4K-Crystal-UHD-2160P-LED-Smart-TV-with-HDR-UN58TU7000/820835173"

response = requests.get(api_url)
soup = BeautifulSoup(response.text, 'html.parser')

# Extract static data
product_data = {}

try:
    product_data["name"] = soup.find("h1", {"itemprop": "name"}).text
except:
    product_data["name"] = None

try:
    product_data["price"] = soup.find("span", {"itemprop": "price"}).text.replace("Now ", "")
except:
    product_data["price"] = None

try:
    product_data["rating"] = soup.find("span", {"class": "rating-number"}).text.strip("()")
except:
    product_data["rating"] = None

# Extract dynamic data
script_tag = soup.find("script", {"id": "__NEXT_DATA__"})
json_data = json.loads(script_tag.text)

try:
    product_data["details"] = json_data['props']['pageProps']['initialData']['data']['product']['shortDescription']
except:
    product_data["details"] = None

print(product_data)
```

---

## Conclusion

With Python and the right libraries, scraping Walmart's product data becomes manageable. While handling captchas and dynamic content can be challenging, tools like ScraperAPI simplify the process by managing proxies and rendering JavaScript.

Unlock seamless scraping with ScraperAPI. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)
