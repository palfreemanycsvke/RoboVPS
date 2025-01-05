
# How to Deploy Web Crawlers in Amazon Bedrock Knowledge Bases

## Overview

Web crawlers are a powerful tool for retrieving and indexing data from public websites, and when integrated with **Amazon Bedrock Knowledge Bases**, they provide a seamless way to build applications powered by Generative AI. This guide will walk you through the steps to use web crawlers within Amazon Bedrock Knowledge Bases, from setting up your data sources to configuring crawlers and testing the knowledge base.

---

Stop wasting time on proxies and CAPTCHAs! ScraperAPI's simple API handles millions of web scraping requests, so you can focus on the data. Get structured data from Amazon, Google, Walmart, and more. 👉 [Start your free trial today!](https://bit.ly/Scraperapi)

---

## Table of Contents

1. Introduction to Amazon Bedrock and Knowledge Bases  
2. Features of Web Crawlers in Knowledge Bases  
3. Configuring Web Crawler Data Sources  
4. Synchronization Scope and Filters  
5. Step-by-Step Solution Implementation  
6. Testing and Monitoring the Knowledge Base  
7. Using AWS SDK to Automate Knowledge Base Creation  
8. Monitoring and Resource Cleanup  

---

## 1. Introduction to Amazon Bedrock

[Amazon Bedrock](https://aws.amazon.com/bedrock/) is a fully managed service that enables you to build, deploy, and scale Generative AI applications using high-performing foundational models (FMs) from top providers like AI21 Labs, Anthropic, and Amazon. It also supports **Knowledge Bases for Amazon Bedrock**, allowing you to aggregate data sources and integrate Retrieval Augmented Generation (RAG) techniques to enhance AI-driven applications.

For many AI applications, access to accurate and relevant data from public websites is critical. By integrating web crawlers with Knowledge Bases, you can efficiently retrieve and utilize website data.

---

## 2. Features of Web Crawlers in Knowledge Bases

Web crawlers in Amazon Bedrock Knowledge Bases allow you to fetch, index, and process data from public websites. Key features include:

- **Seed URLs**: Crawlers start with provided URLs and traverse sublinks under the same top-level domain.
- **File Handling**: Supports non-HTML files like PDFs, Markdown, CSVs, and text files unless excluded.
- **Exclusion and Inclusion Filters**: Use regex patterns to limit or target specific URLs.
- **Rate Limits**: Control crawling speed to comply with website policies.
- **Compliance**: Adheres to robots.txt rules for allowed/disallowed pages and crawling speeds.

> **Note:** Crawlers only handle non-authenticated URLs and require seed URLs to start with `http://` or `https://`.

---

## 3. Configuring Web Crawler Data Sources

When creating a Knowledge Base with a web crawler data source, you can configure synchronization settings to control crawling behavior:

- **Sync Scope**: Choose from options like `Host Only` or `Subdomain`.
- **Inclusion Filters**: Specify regex patterns to include URLs, e.g., `.*products.*`.
- **Exclusion Filters**: Exclude unwanted URLs, e.g., `.*\.pdf$`.
- **Robust Control**: Use Amazon CloudWatch logs to monitor and troubleshoot crawler activities.

### Example Synchronization Scenarios

| **Source URL**                 | **Sync Scope**  | **Crawled URLs**                          |
|---------------------------------|-----------------|-------------------------------------------|
| `https://example.com`           | Host Only       | `https://example.com/page1`               |
| `https://example.com`           | Subdomain       | `https://sub.example.com/page1`           |

---

## 4. Synchronization Scope and Filters

When configuring synchronization, you can control the depth and breadth of the crawler’s reach. Here’s how:

1. **Rate Limiting**: Set limits to control crawling speed. This helps comply with robots.txt rules.
2. **Filters**:
   - **Inclusion**: Crawl only URLs that match specific patterns.
   - **Exclusion**: Ignore URLs that match certain patterns, such as specific file types or directories.

### Regex Examples
- To **exclude PDF files**: `^.*\.pdf$`  
- To **include URLs with "products"**: `.*products.*`

---

## 5. Step-by-Step Solution Implementation

Follow these steps to integrate web crawlers with your Knowledge Base:

### Step 1: Create a Knowledge Base
1. Open the **Amazon Bedrock Console**.
2. Navigate to **Knowledge Bases** and click **Create Knowledge Base**.
3. Provide the following configurations:
   - **Name**: Assign a descriptive name.
   - **IAM Permissions**: Select **Create and use a new service role**.
   - **Data Source**: Choose **Web Crawler**.

---

### Step 2: Configure the Data Source
1. Specify the **Seed URLs**, e.g., `https://www.example.com`.
2. Select the **Sync Scope** (e.g., `Host Only`).
3. Configure filters:
   - **Include Patterns**: `^https?://www.example.com/.*$`.
   - **Exclude Patterns**: `.*plants.*`.
4. Enable **Content Chunking and Parsing** and click **Next**.

---

### Step 3: Configure the Embedding Model
1. Select **Titan Text Embeddings v2** for the embeddings model.
2. Set **Vector Dimensions** to `1024`.
3. Choose **Quick Create a New Vector Store**.

---

### Step 4: Finalize and Create
Review all configurations and click **Create Knowledge Base**. Your Knowledge Base will now start indexing the specified data sources.

---

## 6. Testing and Monitoring the Knowledge Base

### Testing Steps
1. Navigate to your Knowledge Base in the Amazon Bedrock console.
2. Select the data source and click **Sync**.
3. After synchronization, test the Knowledge Base by entering a query, such as:
   - *What are the features of Amazon’s Seattle office?*
   - *Provide details about Amazon's New York offices.*

### Monitoring
Use **Amazon CloudWatch Logs** to track:
- Crawling status.
- URLs accessed.
- Success and failure rates.

---

## 7. Using AWS SDK to Automate Knowledge Base Creation

### Example: Python Code for Knowledge Base Creation
```python
import boto3

client = boto3.client('bedrock-agent')

response = client.create_knowledge_base(
    name='example-kb',
    roleArn='your-role-arn',
    knowledgeBaseConfiguration={
        'type': 'VECTOR',
        'vectorKnowledgeBaseConfiguration': {
            'embeddingModelArn': 'arn:aws:bedrock:your-region::foundation-model/amazon.titan-embed-text-v2:0'
        }
    },
    storageConfiguration={
        'type': 'OPENSEARCH_SERVERLESS',
        'opensearchServerlessConfiguration': {
            'collectionArn': 'your-opensearch-collection-arn',
            'vectorIndexName': 'example_index',
            'fieldMapping': {
                'vectorField': 'documentid',
                'textField': 'data',
                'metadataField': 'metadata'
            }
        }
    }
)
```

### Example: Configure Web Crawler with Filters
```python
response = client.create_data_source(
    knowledgeBaseId='knowledge-base-id',
    name='example-web-crawler',
    dataSourceConfiguration={
        'type': 'WEB',
        'webConfiguration': {
            'sourceConfiguration': {
                'urlConfiguration': {
                    'seedUrls': [{'url': 'https://example.com'}]
                }
            },
            'crawlerConfiguration': {
                'crawlerLimits': {'rateLimit': 300},
                'inclusionFilters': ['.*products.*'],
                'exclusionFilters': ['.*\\.pdf$'],
                'scope': 'HOST_ONLY'
            }
        }
    }
)
```

---

## 8. Monitoring and Cleanup

### Monitoring
Use **Amazon CloudWatch** logs to:
- Monitor crawling activities.
- Debug failures and optimize performance.

### Cleanup
1. Delete the Knowledge Base:
   - Navigate to the Knowledge Bases section in the console and delete the Knowledge Base.
2. Delete the vector database and IAM roles to free up resources.

---

## Conclusion

By integrating web crawlers with **Amazon Bedrock Knowledge Bases**, you can efficiently retrieve, index, and leverage data from public websites. This capability enables AI applications to provide more accurate and relevant responses by utilizing the latest information. Coupled with **Amazon OpenSearch Service**, Bedrock streamlines the process of managing, ingesting, and querying data for AI-driven insights.

Ready to scale your web scraping and data collection efforts? 👉 [Start your free trial today!](https://bit.ly/Scraperapi)
