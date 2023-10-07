# Hadoop Final Assigment
Topic: **Find the most frequent words in a collection of business news in khmertimeskh.com**
## 1. Install Spark on top of Hadoop
### Download
- Visit the Apache Spark website (https://spark.apache.org/downloads.html).
```
wget  [https://www.apache.org/dyn/closer.lua/spark/spark-3.5.0/spark-3.5.0-bin-hadoop3.tgz](https://dlcdn.apache.org/spark/spark-3.5.0/spark-3.5.0-bin-hadoop3.tgz)
```
### Extract

```
tar -zxvf spark-3.5.0-bin-hadoop3.tgz
```
### Move to /usr/local
```
mv spark-3.5.0-bin-hadoop3 spark
sudo cp -r spark /usr/local
```
### Add Path
bin and sbin
SARK_HOME
PATH

```
echo "export SPARK_HOME=/usr/local/spark" >> ~/.bashrc
echo "export HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop" >> ~/.bashrc
source ~/.bashrc
echo "export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin" >> ~/.bashrc

source ~/.bashrc
# to verfiy
echo $SPARK_HOME
echo $PATH
```

To verify
```
spark-shell
```

```

Spark context Web UI available at http://10.15.0.8:4040
Spark context available as 'sc' (master = local[*], app id = local-1696682400617).
Spark session available as 'spark'.
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /___/ .__/\_,_/_/ /_/\_\   version 3.5.0
      /_/
         
Using Scala version 2.12.18 (OpenJDK 64-Bit Server VM, Java 1.8.0_382)
Type in expressions to have them evaluated.
Type :help for more information.

```

## 2. Web Scraped Data
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
## 3. Spark & Hadoop
