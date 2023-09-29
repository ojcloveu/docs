# Hadoop Final Assigment
Topic: **Find the most frequent words in a collection of business news in khmertimeskh.com**
## Install Spark on top of Hadoop
## Web Scraped Data
### Preparation
#### Website
We are choosing khmertimeskh website to scraped data https://www.khmertimeskh.com.
We choose `Business` news category https://www.khmertimeskh.com/category/business/
### Coding 
```
import requests
import os
from bs4 import BeautifulSoup
```

```
getPage(2)
```

```
def getPage (n):
    for i in range(1,n+1):
        listPage (i)
```

```
def listPage (page_number):
        
    # Define the URL
    url = f"https://www.khmertimeskh.com/category/business/page/{page_number}/"

    # Send an HTTP GET request to the URL
    response = requests.get(url)

    # Check if the request was successful (status code 200)
    if response.status_code == 200:
        # Parse the HTML content of the page
        soup = BeautifulSoup(response.text, "html.parser")
        
        # Find all the article elements on the page
        articles = soup.find_all("article")

        # Loop through each article and extract the title and link
        for article in articles:
            # Extract the title
            title = article.find("h2", class_="item-title").text.strip()
            
            # Extract the link
            link = article.find("h2", class_="item-title").find("a")["href"]
            singlePage(link)
    else:
        print("Failed to retrieve the web page")

```

```
def singlePage (link):
    # Send an HTTP GET request to the URL
    response = requests.get(link)

    # Check if the request was successful (status code 200)
    if response.status_code == 200:
        # Parse the HTML content of the page
        soup = BeautifulSoup(response.text, "html.parser")
        
        # Extract the article title
        title = soup.find("h2", class_="entry-title").text.strip()
        
        for related_post in soup.find_all("div", class_="rp4wp-related-posts"):
            related_post.decompose()
        
        # Extract the article content
        content = soup.find("div", class_="entry-content").text.strip()
        
        # Print the title, date, and content
        writeFile (title, content)
    else:
        print("Failed to retrieve the web page")
```

```
def writeFile (title, content):
    # Create a filename based on the article title
    filename = f"out/{title}.txt"
    content = content.replace("Click here to get Khmer Times Breaking News direct into your Telegram", "")
    # Write the content to the file
    with open(filename, "w", encoding="utf-8") as file:
        # file.write(f"{title}\n")
        file.write(content)
    
    print(f"File '{filename}' created successfully.")
```
## Spark & Hadoop
