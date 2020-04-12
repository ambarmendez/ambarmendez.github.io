---
title: Scraping Quotes
published: true
---

After playing a little bit with the enhace code that was shown in [Scraping From Scratch](https://ambarmendez.github.io/Scraping-From-Scratch), we realized what the simple parser provided in the Standard Python Library is able to do. Now, let's see from where the data is going to be extracted and in this case we will be using [Quotes to Scrape](http://quotes.toscrape.com/) which is very convenient, don't you think?

The Python Standard Library offers [`urllib`](https://docs.python.org/3.7/library/urllib.html) which is the URL handling module, and the promise still will be kept about just using what we have at hand. As usual, the code to be used is given as a example in a [HOW TO](https://docs.python.org/3.7/howto/urllib2.html) provided in the Python Software Foundation documentation.

So, let's put together our old friend `MyHTMLParser` and the `urllib.request` to retrieve quotes from the website already mentioned.

```python
import urllib.request

parser = MyHTMLParser()

with urllib.request.urlopen('http://quotes.toscrape.com/') as response:
   the_page = response.read()
   parser.feed(the_page.decode())
```

At this point, it is your turn to play again with the code before and see what happens. For example, what about if `the_page` is replaced by `response.read()` instead. Or  `MyHTMLParser` works as it is expected.

C'mon guys break it, make mistakes, wonder and be curious. Be afraid to be too confortable with perfection.

~~Happy coding!~~ Or it should be said in this case ... Lets break it! =D
