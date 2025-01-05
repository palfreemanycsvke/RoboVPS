
# Python Asynchronous Requests: A Complete Web Scraping Guide with AIOHTTP

## Introduction

If you’ve ever scraped tens or hundreds of web pages, you know the frustration of waiting for one request to finish before starting another. What if you could send multiple requests simultaneously without waiting? That’s where asynchronous web scraping saves time and effort, enabling faster data collection.

In this tutorial, we’ll walk through the process of asynchronous web scraping using Python’s `aiohttp` library. You’ll learn how to scrape multiple web pages in parallel, reducing execution time from minutes to seconds. This technique can also reduce the likelihood of being blocked by websites since requests are spaced out and appear less bot-like.

Stop wasting time on proxies and CAPTCHAs! ScraperAPI’s simple API handles millions of web scraping requests, allowing you to focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## What is Asynchronous Web Scraping?

### Key Advantages of Asynchronous Scraping

- **Faster Execution:** Send multiple requests simultaneously, significantly reducing scraping time.
- **Efficiency:** Make the most of your system's resources with non-blocking I/O operations.
- **Lower Risk of Being Blocked:** Space out requests to mimic human-like behavior, avoiding detection by websites.

### Introducing `aiohttp`

`aiohttp` is a Python library built on `asyncio` that enables asynchronous web scraping. It allows you to:

- Send non-blocking HTTP requests.
- Manage sessions and maintain state between requests.
- Handle headers, authentication, cookies, and more.
- Support asynchronous I/O operations for better performance.

---

## Setting Up Your Environment

Before you start, ensure you have the following tools installed:

1. **Python:** Download the latest version from [python.org](https://www.python.org/).
2. **aiohttp:** Install it using:
   ```bash
   pip install aiohttp
   ```
3. **asyncio:** A standard Python library for asynchronous operations.
4. **Beautiful Soup:** Install for HTML parsing:
   ```bash
   pip install beautifulsoup4
   ```

---

## Step-by-Step Tutorial: Scraping a Dummy Website

We’ll scrape the dummy website [Books to Scrape](https://books.toscrape.com), collecting book titles, URLs, and unique product codes (UPCs). This example demonstrates the power of asynchronous requests for large datasets.

### Step 1: Import Required Modules

```python
import aiohttp
import asyncio
from bs4 import BeautifulSoup
from urllib.parse import urljoin
import csv
```

### Step 2: Initialize Variables

Define the starting URL and a list to store results.

```python
start_url = "https://books.toscrape.com/"
result_dict = []
```

### Step 3: Create a URL Formatter

Ensure all URLs are properly formatted as absolute links.

```python
def make_url(href):
    if "catalogue/" in href:
        return urljoin(start_url, href)
    return urljoin(f"{start_url}catalogue/", href)
```

---

## Crawling the Pages

### Step 1: Send Asynchronous Requests

Use `aiohttp.ClientSession` to manage sessions and send requests asynchronously.

```python
async def scrape_urls(session, url):
    async with session.get(url) as resp:
        if resp.status == 200:
            resp_data = await resp.text()
            next_url = await parse_page(resp_data)
            if next_url is not None:
                await scrape_urls(session, next_url)
        else:
            print(f"Request failed for {url} with: {resp.status}")
```

### Step 2: Parse HTML for Book Links

Extract book titles and URLs using Beautiful Soup.

```python
async def parse_page(resp_data):
    soup = BeautifulSoup(resp_data, "html.parser")
    books_on_page = soup.findAll("article", class_="product_pod")
    for book in books_on_page:
        link_elem = book.find("h3").a
        book_title = link_elem.get("title")
        book_url = make_url(link_elem.get("href"))
        print(f"{book_title}: {book_url}")
        result_dict.append({
            'title': book_title,
            'url': book_url,
        })
    try:
        next_page_link = soup.find("li", class_="next").a
        return make_url(next_page_link.get("href"))
    except:
        print("No link to the next page. Done.")
        return None
```

---

## Scraping Book Information

### Step 1: Fetch Book Details

Scrape the UPC field for each book.

```python
async def scrape_item(session, book):
    print(f"Scraping: {book['url']}")
    async with session.get(book['url']) as resp:
        if resp.status == 200:
            resp_data = await resp.text()
            soup = BeautifulSoup(resp_data, "html.parser")
            table_elem = soup.find("table", class_="table table-striped")
            book_upc = table_elem.select("td:nth-of-type(1)")[0].get_text()
            book["upc"] = book_upc
        else:
            print(f"Request failed for {book['url']} with: {resp.status}")
```

### Step 2: Process All Books

Loop through the `result_dict` list and scrape each book.

```python
async def main():
    async with aiohttp.ClientSession() as session:
        await scrape_urls(session, start_url)
        for book in result_dict:
            await scrape_item(session, book)
    print(result_dict)
    write_to_csv()
```

---

## Writing Data to a CSV File

Store the scraped data in a CSV file for further analysis.

```python
def write_to_csv():
    with open("books_result.csv", "w", newline='') as f:
        writer = csv.DictWriter(f, fieldnames=['title', 'url', 'upc'])
        writer.writeheader()
        writer.writerows(result_dict)
```

---

## Full Code Example

```python
import aiohttp
import asyncio
from bs4 import BeautifulSoup
from urllib.parse import urljoin
import csv

start_url = "https://books.toscrape.com/"
result_dict = []

def make_url(href):
    if "catalogue/" in href:
        return urljoin(start_url, href)
    return urljoin(f"{start_url}catalogue/", href)

async def parse_page(resp_data):
    soup = BeautifulSoup(resp_data, "html.parser")
    books_on_page = soup.findAll("article", class_="product_pod")
    for book in books_on_page:
        link_elem = book.find("h3").a
        book_title = link_elem.get("title")
        book_url = make_url(link_elem.get("href"))
        result_dict.append({'title': book_title, 'url': book_url})
    try:
        next_page_link = soup.find("li", class_="next").a
        return make_url(next_page_link.get("href"))
    except:
        return None

async def scrape_urls(session, url):
    async with session.get(url) as resp:
        if resp.status == 200:
            resp_data = await resp.text()
            next_url = await parse_page(resp_data)
            if next_url:
                await scrape_urls(session, next_url)

async def scrape_item(session, book):
    async with session.get(book['url']) as resp:
        if resp.status == 200:
            resp_data = await resp.text()
            soup = BeautifulSoup(resp_data, "html.parser")
            table_elem = soup.find("table", class_="table table-striped")
            book_upc = table_elem.select("td:nth-of-type(1)")[0].get_text()
            book["upc"] = book_upc

def write_to_csv():
    with open("books_result.csv", "w", newline='') as f:
        writer = csv.DictWriter(f, fieldnames=['title', 'url', 'upc'])
        writer.writeheader()
        writer.writerows(result_dict)

async def main():
    async with aiohttp.ClientSession() as session:
        await scrape_urls(session, start_url)
        for book in result_dict:
            await scrape_item(session, book)
    write_to_csv()

if __name__ == "__main__":
    asyncio.run(main())
```

---

## Conclusion

Asynchronous web scraping with `aiohttp` enables faster, more efficient data collection. By managing multiple requests in parallel, you can scrape large datasets without unnecessary delays. Whether you’re gathering product details or monitoring competitors, asynchronous techniques ensure maximum efficiency.

👉 [Start your free trial with ScraperAPI today!](https://bit.ly/Scraperapi)
