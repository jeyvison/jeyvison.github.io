---
title: "Crawling the Web With Scrapy"
date: 2017-11-12T22:17:05Z
draft: false
---

[Scrapy](https://scrapy.org/) is a web crawler tool developed in Python with a high-level and simple syntax to get data from websites.

## Installation

You can install it using pip:

```
pip install scrapy
```

or Anaconda

```
conda install -c conda-forge scrapy
```

or you can download the source.

## Developing a spider

In this post, we’ll get the title and the link of the articles in the first page of the BBC Website.

We will use the following script to crawl it:

```
import scrapy
class FirstPage(scrapy.Spider):
 	name = ‘firstpage’
 	start_urls = [‘http://www.bbc.com']
def parse(self, response):
 	for article in response.css(‘a.block-link__overlay-link’):
 	yield{
 		‘text’: article.css(‘::text’).extract_first(),
 		‘link’: article.css(‘::attr(href)’).extract_first(),
	}
	
```

First things first, let me explain you what this is script is doing:

**1 — import scrapy** :
First , we need to import scrapy so we can use it in our code.
This line guarantees that we can use the scrapy features in our script.

**2 — class FirstPage(scrapy.Spider)**:
We have to create a classe where our logic will be executed. You may note the scrapy.Spider class we are Inheriting. This Spyder class will provide some attributes and methods we will need and is responsible to do the actual crawling.

**3 — name = ‘firstpage** :
An unique name that identifies our Spider.

**4 — start_urls = [‘http://www.bbc.com']**:
I mentioned above about some methods that we need and the Spyder class can provide for us. One of them is called start_requests.

It simply gets the list of urls that we defined as start_urls (thats the required name for this attribute if we want the Spyder to get it for us), make a request to each url and redirect the response to the parse method.

All this with one line of code ;).

**5 — def parse(self, response)**:
This is where our logic goes.

This method is the only one we must have in our script for our crawling.The response object holds the methods we will use to get the information we want from the web page

**6 —for article in response.css(‘a.block-link__overlay-link’)**:
yield{
‘text’: article.css(‘::text’).extract_first(),
‘link’: article.css(‘::attr(href)’).extract_first(),
}

First we iterate over all the a html elements that has the css class **block-link__overlay-link**.

For each element we find, we get two informations from it: the text of the article (the value of the html element) and the href attribute.

Once these two informations are retrived these values are returned as an iterable list(‘yield keyword’).

## Running the script

Now that we understand our script, let's see it running :)

Create a file named **first_page.py** and add our script to it.

Then run **scrapy runspider first_page.py** and check the output in console.

## Conclusion

Scrapy is a powerful tool with lots of other cool features that you can check [here](https://docs.scrapy.org/en/latest/).Also, the above script is available on this [link](https://github.com/jeyvison/scrapy-tutorial).

