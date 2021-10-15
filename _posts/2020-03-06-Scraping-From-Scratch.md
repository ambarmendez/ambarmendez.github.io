---
layout: post
title: Scraping From Scratch
published: true
---

There is plenty of information about [Web Scraping with Python](https://realpython.com/python-web-scraping-practical-introduction/), it is just an example.

What it makes this one different is that we are going to do it just with what it comes available in [The Python Standard Library](https://docs.python.org/3/library/), which offers the basis for extracting data from the web.

So this process is a matter of:
1. Choosing a webpage that has the desired data which is going to be extracted.
2. Examing carefully where the data is located.
3. Finally, making the extraction.

Pretty match! That's all that needs to be done. But How?

Let's start with **Examing carefully where the data is located**. We are talking
about _web_ a term which is strongly related to [HTML](https://developer.mozilla.org/en-US/docs/Learn/HTML),
I am not going to go on that road there is plenty of resources talking about
that. Our focus will be about how to parse an HTML file and how The Python
Standard Library can help us with that, which is under [Structured Markup Processing Tools](https://docs.python.org/3/library/markup.html)
specifically [Simple HTML and XHTML parser](https://docs.python.org/3/library/html.parser.html). From
an example provided there, we are going to start this journey.

```python
from html.parser import HTMLParser

class MyHTMLParser(HTMLParser):
    def __init__(self):
        HTMLParser.__init__(self)
        self.nested_level = 0

    def handle_starttag(self, tag, attrs):
        self.tabs = ''
        for i in range(self.nested_level):
            self.tabs += ' '
        print(self.tabs, '<', tag.upper(), ' level="', self.nested_level, '">', sep='')

        self.nested_level += 1

        for attr, value in attrs:
            print(self.tabs + '\tattr:', attr, ', value:',value)

    def handle_endtag(self, tag):
        self.nested_level -= 1
        self.tabs = ''
        for i in range(self.nested_level):
            self.tabs += ' '

        print(self.tabs, '</', tag.upper(), ' level="', self.nested_level, '">', sep='')

    def handle_data(self, data):
        if data.isprintable():
              print(self.tabs, "\tData:", data)
```

C'mon fellas play with it!

```python
parser = MyHTMLParser()
parser.feed('<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" '
            '"http://www.w3.org/TR/html4/strict.dtd">'
            '<html><head><title>Test</title></head><body>'
            '<H1>This is a Header</H1>'
            '<style type="text/css">#python { color: green }</style>'
            '<span>buffered text</span>'
            '<p><a class=link href=#main>tag &gt;&#62;&#x3E; soup</p ></a>'
            '<script type="text/javascript">'
            '<!-- a comment inside script tags --> '
            'alert("<strong>hello!</strong>");</script>'
            '<!-- a comment -->'
            '<!--[if IE 9]>IE-specific content<![endif]-->'
            '<CENTER><a href="https://images.pexels.com/photos/1064727/pexels-photo-1064727.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=750&w=1260" ALIGN="BOTTOM">Hermosos gatitos</a></CENTER>'
            '<B><I>This is a new sentence without a paragraph break, in bold italics.</I></B>'
            '<h1>Parse me!</h1></body></html>')
```
See you next time,

Happy coding! =D
