
# Web Scraping eBay: A Step-by-Step Guide

## Introduction

eBay, one of the largest online marketplaces, is a treasure trove of product data, including prices, descriptions, seller ratings, and more. Whether you're comparing prices, analyzing trends, or gathering competitor insights, accessing eBay data can help inform your decisions.

In this guide, we’ll demonstrate how to build a web scraper using **Puppeteer**, a powerful Node.js library. By the end of this tutorial, you'll be able to extract product data, automate eBay searches, and save the results in a CSV file for analysis.

---

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Table of Contents
1. [What is Puppeteer?](#what-is-puppeteer)
2. [Prerequisites](#prerequisites)
3. [Setting Up Puppeteer](#setting-up-puppeteer)
4. [Navigating and Executing Search Queries](#navigating-and-executing-search-queries)
5. [Extracting Product Data](#extracting-product-data)
6. [Scraping Multiple Pages](#scraping-multiple-pages)
7. [Saving Data to CSV](#saving-data-to-csv)
8. [Conclusion](#conclusion)

---

## What is Puppeteer?

Puppeteer is a Node.js library developed by Google that provides an API to control Chrome or Chromium browsers. It is widely used for:

- **Web scraping** dynamic and JavaScript-rendered websites.
- **Browser automation** for repetitive tasks.
- **Automated testing** of web applications.

### Key Features of Puppeteer:

- **Headless mode**: Perform tasks without a browser UI, improving speed and resource efficiency.
- **Dynamic content rendering**: Extract data from web pages rendered with JavaScript.
- **Flexible navigation**: Simulate user interactions such as clicks and form submissions.
- **Network interception**: Monitor API calls and manipulate requests and responses.

---

## Prerequisites

Before starting, ensure you have the following:

1. **Node.js installed**: Download it from [Node.js](https://nodejs.org/).
2. **Puppeteer**: Install it via npm.
3. **Objects-to-CSV**: A library to save scraped data in CSV format.

### Install Dependencies
Run the following commands in your terminal to set up your environment:

```bash
# Initialize a new Node.js project
npm init -y

# Install Puppeteer and Objects-to-CSV
npm install puppeteer objects-to-csv
```

---

## Setting Up Puppeteer

### Create the Main Script

1. Create a file named `index.js` in your project directory.
2. Import Puppeteer in your script:

```javascript
const puppeteer = require('puppeteer');
```

3. Define an asynchronous function for the scraping logic:

```javascript
async function run(searchQuery) {
    const browser = await puppeteer.launch({ headless: false, defaultViewport: false });
    const page = await browser.newPage();

    // Your scraping logic will go here

    await browser.close(); // Close the browser when done
}

run("Nike Air Jordan");
```

### Launch Puppeteer

The `launch()` method starts a new browser instance:

- **headless: false**: Keeps the browser UI visible (useful for debugging).
- **defaultViewport: false**: Disables default viewport settings for a full-screen view.

---

## Navigating and Executing Search Queries

### Step 1: Navigate to eBay

Use the `goto()` method to load eBay’s homepage:

```javascript
await page.goto("https://www.ebay.com", { waitUntil: "domcontentloaded" });
```

### Step 2: Perform a Search

Simulate entering a search term and clicking the search button:

```javascript
await page.type('#gh-ac', searchQuery); // Type the query into the search bar
await page.click('#gh-btn'); // Click the search button
await page.waitForNavigation(); // Wait for the results page to load
```

---

## Extracting Product Data

### Step 1: Identify Data Elements

Using DevTools (F12), inspect the search results to identify CSS selectors for the desired elements, such as:

- Product title: `.s-item__title`
- Price: `.s-item__price`
- Link: `.s-item__link`

### Step 2: Scrape the Data

Use `page.evaluate()` to execute JavaScript in the browser context and extract the data:

```javascript
const items = await page.evaluate(() => {
    return Array.from(document.querySelectorAll('.s-item')).map(item => ({
        title: item.querySelector('.s-item__title')?.innerText,
        price: item.querySelector('.s-item__price')?.innerText,
        link: item.querySelector('.s-item__link')?.href,
        image: item.querySelector('img')?.src,
        location: item.querySelector('.s-item__itemLocation')?.innerText.slice(5),
        shippingPrice: item.querySelector('.s-item__shipping')?.innerText
    }));
});

console.log(items); // Output the scraped data
```

---

## Scraping Multiple Pages

### Step 1: Modify URL Parameters

eBay uses query parameters for pagination. For example:

- `_ipg=240`: Number of items per page (e.g., 60, 120, or 240).
- `_pgn=2`: Page number.

To scrape multiple pages, update these parameters dynamically:

```javascript
async function scrapePage(page, pageNumber) {
    const url = new URL(page.url());
    url.searchParams.set('_ipg', '240');
    url.searchParams.set('_pgn', pageNumber);

    await page.goto(url.toString(), { waitUntil: "domcontentloaded" });

    return await page.evaluate(() => {
        return Array.from(document.querySelectorAll('.s-item')).map(item => ({
            title: item.querySelector('.s-item__title')?.innerText,
            price: item.querySelector('.s-item__price')?.innerText,
            link: item.querySelector('.s-item__link')?.href
        }));
    });
}

const results = [];
for (let i = 1; i <= 3; i++) {
    const pageResults = await scrapePage(page, i);
    results.push(...pageResults);
}
```

---

## Saving Data to CSV

Use the **Objects-to-CSV** library to save your data in a structured format:

1. Import the library:

```javascript
const ObjectsToCSV = require('objects-to-csv');
```

2. Create a function to save the data:

```javascript
async function saveToCSV(items) {
    const csv = new ObjectsToCSV(items);
    await csv.toDisk('./data.csv', { allColumns: true });
}

await saveToCSV(results); // Save the scraped data
```

The CSV file will be saved in the project directory.

---

## Conclusion

Congratulations! You’ve successfully built an eBay scraper using Puppeteer. In this guide, you’ve learned to:

- Set up Puppeteer for web scraping.
- Scrape product data from eBay.
- Navigate through multiple pages to gather more data.
- Save the scraped data to a CSV file.

With this scraper, you can automate data collection from eBay and gain valuable insights into product trends, pricing, and more. For enhanced scraping capabilities and handling challenges like CAPTCHAs, check out [ScraperAPI](https://bit.ly/Scraperapi). Happy scraping!
