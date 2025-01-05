
# Managing Web Crawlers with Scrapyd: A Comprehensive Guide

Scrapyd is an official tool provided by Scrapy for managing web crawlers. It simplifies the process of uploading, controlling, and monitoring crawlers while offering the ability to view logs. With Scrapyd, you can schedule and deploy crawlers from any computer connected to the server, all via its JSON web service.

---

## Why Use ScraperAPI for Your Web Scraping Projects?

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, allowing you to focus on data collection. Get structured data from Amazon, Google, Walmart, and more.  
👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Introduction to Scrapyd

Unlike directly running the `scrapy crawl myspider` command, Scrapyd provides an additional layer of convenience with its JSON web service. This service allows you to:
- Run or stop crawlers remotely.
- Deploy new crawlers without logging into the server.
- Monitor logs and crawler statuses via a web interface.

---

## Installing Scrapyd

You can install Scrapyd using pip:

```bash
pip install scrapyd
```

### Reference Documentation:
[Scrapyd-Client GitHub Repository](https://github.com/scrapy/scrapyd-client)

---

## Running the Scrapyd Service

Start Scrapyd by simply running the following command:

```bash
scrapyd
```

By default, Scrapyd listens on `0.0.0.0:6800`. Once running, visit `http://localhost:6800/` in your browser to view available projects.

### Key Features:
- Web Interface: Accessible at `http://localhost:6800/`.

![Scrapyd Interface](https://piaosanlang.gitbooks.io/spiders/content/photos/scrapyd.png)

---

## Deploying Scrapy Projects with Scrapyd

To deploy a Scrapy project, use the `scrapyd-client` tool:

```bash
pip install scrapyd-client
```

### Configuring the Deployment
Modify the `scrapy.cfg` file in your project directory:

```ini
[deploy:my_server]
url = http://your-server-address:6800/
project = my_project
username = your_username
password = your_password
```

- `url`: The address of the server where you want to deploy the project.
- `username` and `password`: Credentials for HTTP basic authentication.

---

### Deploying the Project
Run the deployment command from your project's root directory:

#### For Windows:
```bash
python c:\Python27\Scripts\scrapyd-deploy
```

#### For Ubuntu:
```bash
scrapyd-deploy my_server -p my_project
```

This process packages your project and deploys it to the specified server.

---

## Viewing Available Spiders

List all available spiders for a project using the following commands:

```bash
scrapy list
scrapyd-deploy -l
```

Or, check the web interface at `http://localhost:6800/` to see the available projects.

---

## Scrapyd API

Scrapyd's web interface is primarily for monitoring, while all scheduling and deployment tasks are performed using its API. Here are some key API endpoints:

### Start a Spider
Schedule a spider to run using the `schedule.json` endpoint:

```bash
curl http://localhost:6800/schedule.json -d project=my_project -d spider=my_spider
```

Response Example:
```json
{"status": "ok", "jobid": "94bd8ce041fd11e6af1a000c2969bafd", "node_name": "ubuntu"}
```

---

### Stop a Spider
Cancel a running spider job using the `cancel.json` endpoint:

```bash
curl http://localhost:6800/cancel.json -d project=my_project -d job=94bd8ce041fd11e6af1a000c2969bafd
```

---

### List All Spiders
Retrieve a list of all spiders in a project:

```bash
curl http://localhost:6800/listspiders.json?project=my_project
```

---

### Delete a Project
Remove a deployed project using the `delproject.json` endpoint:

```bash
curl http://localhost:6800/delproject.json -d project=my_project
```

---

## Updating Scrapyd Projects

Scrapyd defaults to deploying projects under a "default" project name if not specified. Here's how to update configurations:

### Example Configuration 1
```ini
[deploy]
url = http://192.168.17.129:6800/
project = my_project
username = admin
password = admin_pass
```

### Example Configuration 2
```ini
[deploy:my_custom_deploy]
url = http://192.168.17.129:6800/
project = my_project
username = admin
password = admin_pass
```

### Notes:
- Always execute commands from the project's root directory.
- Ensure your configurations are correctly defined in the `scrapy.cfg` file.

---

## Conclusion

Scrapyd simplifies the deployment and management of Scrapy projects with its JSON API and web interface. Whether you're scheduling spiders, monitoring their status, or deploying new projects, Scrapyd provides a streamlined approach to managing web crawlers at scale.

For those looking to scale their scraping projects even further, tools like **ScraperAPI** offer robust solutions for handling proxies, CAPTCHAs, and more.  
👉 [Start your free trial today!](https://bit.ly/Scraperapi)
