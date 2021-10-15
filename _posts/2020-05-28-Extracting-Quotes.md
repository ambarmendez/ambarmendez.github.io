---
layout: post
title: Extracting Quotes
published: true
---

Now it is time to do some work fellas. For this part, we require some help, and it will come from a browser, there is an option which enables us to inspect the HTML and locate where is the data.

1. Just click on [quotes.toscrape.com](http://quotes.toscrape.com/).
2. Then move the mouse over a quote right-click and select `Inspect` for [Chrome](https://developers.google.com/web/tools/chrome-devtools/dom#inspect) or `Inspect Element` for [Firefox](https://developer.mozilla.org/en-US/docs/Tools/Page_Inspector/How_to/Open_the_Inspector)

The content in the HTML received from the server is like a tree, each branch is a tag, and we want some specific leaves so inspecting the code will help us to find out which path we have to follow. The levels allow us to identify where is located the value, let's take the author as an example, we want the `<span>` that have `class="author"` and with a branch labelled by `<small>` and `class="author"` as well.

| level | type | content
| :---: |  ---:|--------
|   1   | start tag | \<div class="quote"\> |
|   2   | start tag | \<span class="text"\> |
|       | data | “I have not failed. I've just found 10,000 ways that won't work.” |
|       | end tag | \</span\> |
|       | start tag | \<span\> |
|       | data | by |
|   3   | start tag | \<small class="author"\> |
|       | data | Thomas A. Edison |
|       | end tag | \</small\> |
|       | start tag | \<a href="/author/Thomas-A-Edison"\> |
|       | data | (about) |
|       | end tag | \</a\> |
|       | start tag | \</span\>|
|       | end tag | \</div\> |

Please, be aware that I just took a piece from the whole HTML document to illustrate the point, and I have thrown away everything else. As you can see, we are only interested in the specific pattern that was shown in the table. So, let's take our first version of [MyHTMLParser](https://ambarmendez.github.io/Scraping-From-Scratch), and after playing again with it, we have our first attempt to track down those precious leaves.

```python
from html.parser import HTMLParser

class MyHTMLParser(HTMLParser):
    def __init__(self):
        HTMLParser.__init__(self)
        self.nested_level = 0
        self.recording = False
        self.extracted_data = []

    def handle_starttag(self, tag, attrs):
        if tag == 'div':
            if self.is_job_card(attrs):
                self.nested_level += 1
                return
        if tag == 'span':
            if self.nested_level == 1:
                self.nested_level += 1
                if self.is_quote_within_span(attrs):
                    self.recording = True
                    return
        if tag == 'small':
            if self.is_author_within_span(attrs):
                self.nested_level += 1
                self.recording = True
                return

    def is_job_card(self, attrs):
        for attr, value in attrs:
            if attr == 'class':
                if value.find('quote') != -1:
                    return True
        return False

    def is_quote_within_span(self, attrs):
        for attr, value in attrs:
            if attr == 'class':
                if value.find('text') != -1:
                    return True
        return False

    def is_author_within_span(self, attrs):
        for attr, value in attrs:
            if attr == 'class':
                if value.find('author') != -1:
                    return True
        return False

    def handle_data(self, data):
        if self.recording:
            self.extracted_data.append(data)
            print(data)

    def handle_endtag(self, tag):
        if tag == 'span' and self.nested_level == 2:
            self.recording = False
            self.nested_level -= 1
            return
        if tag == 'small' and self.nested_level == 3:
            self.recording = False
            self.nested_level -= 1
            return
        if tag == 'div' and self.nested_level == 1:
            self.recording = False
            self.nested_level = 0
            return


import urllib.request

parser = MyHTMLParser()

with urllib.request.urlopen('http://quotes.toscrape.com/') as response:
   the_page = response.read()
   parser.feed(the_page.decode())
```

As you can see, we are just extracting quotes from the first page. What about all the other pages? Now, it is your time to toy around. See what can be done to get the remaining quotes from the rest of the pages.

Happy coding! <:o)
