
# WebMagic: Java Web Scraping Framework

WebMagic is a Java-based web scraping framework inspired by Scrapy. It integrates mature tools like HttpClient and Jsoup, providing a powerful and flexible solution for building web crawlers.

---

## Why Use ScraperAPI for Efficient Web Scraping?

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Core Components of WebMagic

WebMagic consists of four primary components:
1. **Downloader**: Handles HTTP requests and responses.
2. **PageProcessor**: Defines how to process downloaded content.
3. **Scheduler**: Manages URLs and tasks.
4. **Pipeline**: Saves extracted data.

### Key Data Flow Objects
- **Request**: Represents a single URL to scrape. It's the primary control for the `PageProcessor`.
- **Page**: Contains content downloaded by the `Downloader`.
- **ResultItems**: A map-like structure holding processed results for the `Pipeline`.

---

## Spider: The Heart of WebMagic

The `Spider` class is the core of WebMagic, controlling the entire crawling process. It integrates the four components mentioned above and provides features like:
- Multi-threading
- Crawler lifecycle management (start/stop)
- Task scheduling

---

## Getting Started with WebMagic

### Adding Dependencies
For Maven users, include the following dependency:

```xml
<dependency>
    <groupId>us.codecraft</groupId>
    <artifactId>webmagic-extension</artifactId>
    <version>0.7.3</version>
    <exclusions>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

Alternatively, you can download the latest JAR file from [WebMagic's official site](http://webmagic.io) and import it into your project.

---

## Building Your First Web Crawler

To build a basic crawler, create a class that implements the `PageProcessor` interface. The process involves three key steps:
1. **Configuring the Crawler**:
   - Define HTTP headers, timeouts, retry strategies, proxies, etc., using the `Site` class.
   - For proxy settings, use the `HttpClientDownloader` with the `ProxyProvider` API.
2. **Extracting Page Elements**:
   - Use techniques like XPath, regex, CSS selectors, or JSONPath for parsing data.
3. **Discovering and Adding Links**:
   - Extract links using methods like `page.getHtml().links().regex("")`.
   - Add links to the crawl queue with `page.addTargetRequests(urls)`.

---

## Saving Data with Pipelines

`Pipeline` is used to store the extracted results. For example:
- Use `ConsolePipeline` to display results in the console.
- Implement custom pipelines to save results in JSON, databases, or other formats.

### Example: Save Results as JSON
Define a pipeline that outputs the scraped data in JSON format.

---

## Sending POST Requests

From version 0.7.1, WebMagic uses the `Request` object to define POST requests. You can initialize `HttpRequestBody` with built-in methods for common tasks like form submission or JSON payloads.

---

## Advanced Features

1. **Multi-threading**: WebMagic supports concurrent scraping by setting the number of threads in `Spider`.
2. **Customizable Scheduler**: Manage URL queues with built-in or custom schedulers.
3. **Plugin Integration**: Extend functionality with additional plugins or libraries.

---

## Conclusion

WebMagic is a powerful framework for building Java web crawlers. By integrating flexible components and supporting advanced features, it simplifies the process of extracting, processing, and saving data from web pages.

---
👉 [Start your free trial with ScraperAPI today!](https://bit.ly/Scraperapi)
