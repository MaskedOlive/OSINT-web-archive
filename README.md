# Web Archive Research in OSINT

Web archive research is a crucial technique in Open Source Intelligence (OSINT). By examining archived versions of websites, investigators can uncover valuable data that may no longer be available on the live web. Web archives allow you to explore content that may have been deleted, changed, or hidden, aiding you in tracking down important evidence or insights.

This guide will cover the use of web archives, focusing primarily on the Wayback Machine and other tools to retrieve historical versions of websites. We'll dive into methods, best practices, and coding examples to help with your research.

## Table of Contents
- [Introduction to Web Archives](#introduction-to-web-archives)
- [The Wayback Machine](#the-wayback-machine)
  - [Using the Wayback Machine API](#using-the-wayback-machine-api)
- [Advanced Techniques](#advanced-techniques)
  - [Searching with Specific Date Ranges](#searching-with-specific-date-ranges)
  - [Handling URL Variants](#handling-url-variants)
- [Extracting Data from Archived Pages](#extracting-data-from-archived-pages)
- [Checking Robots.txt](#checking-robotstxt)
- [Using Other Web Archives](#using-other-web-archives)
- [Best Practices](#best-practices)
- [Conclusion](#conclusion)

---

## Introduction to Web Archives

Web archives are digital repositories that store snapshots of web pages from various points in time. One of the most well-known web archives is the **Wayback Machine**, hosted by *archive.org*. These archives can be invaluable for OSINT, as they allow users to view websites in their previous forms, even if the live site no longer exists or has been modified.

Other web archives include:
- [UK Web Archive](https://www.webarchive.org.uk/)
- [Archive.today](https://archive.ph/)
- [Memento Web](https://mementoweb.org/)

These archives serve as a time machine for websites, offering an opportunity to retrieve lost or hidden information that could aid investigations.

---

## The Wayback Machine

The **Wayback Machine** is the most popular web archive and contains billions of snapshots of web pages dating back to 1996. It provides an easy-to-use interface for browsing historical versions of websites.

### Using the Wayback Machine Interface

1. Navigate to [archive.org/web](https://archive.org/web).
2. Enter the URL of the website you're investigating into the search bar.
3. Browse the available snapshots by selecting a specific date from the calendar.

You can explore different versions of a page over time and check if certain content (such as text, images, or files) was present or altered.

### Using the Wayback Machine API

The Wayback Machine also provides an API that you can use to automate retrieval of archived pages. Below is an example of how to interact with the Wayback Machine API using Python.

```python
import requests

def get_wayback_snapshot(url):
    api_url = f'http://archive.org/wayback/available?url={url}'
    response = requests.get(api_url)
    
    if response.status_code == 200:
        data = response.json()
        if 'archived_snapshots' in data:
            snapshot = data['archived_snapshots'].get('closest', {})
            return snapshot.get('url', 'No snapshot available')
        else:
            return "No archived snapshots found"
    else:
        return "Failed to fetch data"

# Example usage
url = 'http://example.com'
snapshot_url = get_wayback_snapshot(url)
print(f'Snapshot URL: {snapshot_url}')

import requests

def get_snapshot_by_date_range(url, start_date, end_date):
    api_url = f'http://archive.org/wayback/available?url={url}&from={start_date}&to={end_date}'
    response = requests.get(api_url)
    
    if response.status_code == 200:
        data = response.json()
        if 'archived_snapshots' in data:
            snapshot = data['archived_snapshots'].get('closest', {})
            return snapshot.get('url', 'No snapshot found within the range')
        else:
            return "No archived snapshots found"
    else:
        return "Failed to fetch data"

# Example usage
url = 'http://example.com'
start_date = '20230101'  # YYYYMMDD format
end_date = '20231231'
snapshot_url = get_snapshot_by_date_range(url, start_date, end_date)
print(f'Snapshot URL within date range: {snapshot_url}')
```
In this example, replace start_date and end_date with the desired date range in YYYYMMDD format.

## Handling URL Variants
Websites often have different URL structures, such as http://www.example.com and http://example.com, or https://example.com versus http://example.com. When using web archives, make sure to test both the "www" and non-"www" versions, as well as HTTP and HTTPS, to ensure you're capturing all available snapshots.

## Extracting Data from Archived Pages
After identifying the relevant archived pages, you may need to extract specific data for analysis. This could include extracting images, text, or even downloading files.

You can use libraries such as BeautifulSoup to scrape data from archived pages:
```python
import requests
from bs4 import BeautifulSoup

def extract_data_from_snapshot(snapshot_url):
    response = requests.get(snapshot_url)
    soup = BeautifulSoup(response.content, 'html.parser')
    
    # Extract text
    text_content = soup.get_text()
    
    # Extract all images
    images = [img['src'] for img in soup.find_all('img', src=True)]
    
    return text_content, images

# Example usage
snapshot_url = 'https://web.archive.org/web/20230101010101/http://example.com'
text, images = extract_data_from_snapshot(snapshot_url)
print(f"Extracted text: {text[:500]}...")  # Print the first 500 characters of the text
print(f"Images found: {images[:5]}")  # Show the first 5 image sources
```
This script scrapes the text and images from an archived page using the BeautifulSoup library. (or should anyways if I have done it right)

## Checking Robots.txt
Many websites include a robots.txt file that dictates which pages should be crawled or not. It's worth checking these files in the Wayback Machine to see if certain pages were deliberately excluded from crawling and archiving.

To view a robots.txt file, simply add /robots.txt to the base URL. For example: http://example.com/robots.txt

## Using Other Web Archives
In addition to the Wayback Machine, other web archives can be useful:

UK Web Archive: Contains UK-based web pages.
UK Web Archive
Archive.today: An alternative to the Wayback Machine that also archives social media and other platforms.
Archive.today
You can interact with these archives using their respective APIs or simply browse their interfaces to retrieve snapshots.

## Best Practices
- Multiple Snapshots: Always check multiple versions of a page to ensure you're getting the most accurate historical data.
- Backup Important Data: If you find useful data in an archived page, consider backing it up as archives may eventually become inaccessible.
- Cross-verify: Use multiple web archives to cross-verify data, especially when dealing with critical information.
- Understand Limitations: Web archives might not have captured every page, and some data (like dynamic content) might not be present.

## Conclusion
Web archive research is a powerful tool in OSINT. By using the Wayback Machine and other archives, you can retrieve valuable historical data that can be crucial in investigations. Whether you're tracking changes to a website, uncovering deleted content, or verifying the presence of certain information, web archives can provide critical insights.

Remember to explore various techniques, such as filtering by date range, handling URL variants, and extracting useful data programmatically. These strategies will enhance your OSINT capabilities and provide more comprehensive insights from archived web data.
