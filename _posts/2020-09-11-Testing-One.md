---
layout: post
title: Testing Level Zero Iteration One
published: true
---

So little for our first attempt and still so many things to improve because something
doesn't feel right. But before tackle that we should recap what are we trying
to achieve with our little project, want we want to do, in other words how we want
to behave which is _**`scraping quotes and do something with that`**_. For now and
many more revisions to come we are going to focus our concentration just in the
scraping part because it is the foundation where our solution stands, in that scenario
where it receives the response, a response that we know how it should be structured so
using the same name but faking its return value it is a good simulation.

This new approach is being served by
[Mock](https://docs.python.org/3/library/unittest.mock.html#the-mock-class) and
carried through [Context Managers](https://docs.python.org/3/reference/datamodel.html#context-managers),
the reason for the last friend is because of
[the with statement](https://docs.python.org/3/reference/compound_stmts.html#the-with-statement)
which is being used when urlopen is called, do you remember that in our playtime?

```python
import urllib.request

with urllib.request.urlopen('http://quotes.toscrape.com/') as response:
   the_page = response.read()
```

The new look for our test method will be:

```python
response_value = bytes("""
  <div class="quote">
        <span class="text" itemprop="text">“The world as we have created it is a process of our thinking. It cannot be changed without changing our thinking.”</span>
        <span>by <small class="author" itemprop="author">Albert Einstein</small>
        <a href="/author/Albert-Einstein">(about)</a>
        </span>
    </div>

    <div class="quote">
        <span class="text" itemprop="text">“It is our choices, Harry, that show what we truly are, far more than our abilities.”</span>
        <span>by <small class="author" itemprop="author">J.K. Rowling</small>
        <a href="/author/J-K-Rowling">(about)</a>
        </span>
    </div>

    <div class="quote">
        <span class="text" itemprop="text">“There are only two ways to live your life. One is as though nothing is a miracle. The other is as though everything is a miracle.”</span>
        <span>by <small class="author" itemprop="author">Albert Einstein</small>
        <a href="/author/Albert-Einstein">(about)</a>
        </span>
    </div>
""", 'utf-8')

urlopen.return_value = MockResponse(response_value)

response = fetch_quotes_toscrape()

quote_list = extract_quotes(response)

self.assertEqual(len(quote_list), 3)
```

WAIT! But the response value content is not equal to the real response and again
that's right, but our parser doesn't take into account those extra tags necessary
for the HTML content but dispensable for our consume.

```python
class MockResponse():
    def __init__(self, resp_data, code=200):
        self.resp_data = resp_data
        self.code = code
        self.headers = {'content-type':'text/plain; charset=utf-8'}

    def __enter__(self):
        return self

    def __exit__(self, exc_type,exc_value, exc_traceback):
        pass

    def read(self):
        return self.resp_data

    def getcode(self):
        return self.code
```

The tricky part `MockResponse` which resembles more or less what the real response
would have in combination with what is required for the with statement

```python
import unittest
from unittest.mock import Mock

urlopen = Mock()

def fetch_quotes_toscrape():
    with urlopen('http://quotes.toscrape.com/') as response:
        if response.getcode() == 200:
            return response.read()
        return None
```

Finally, the little piece of code that changes everything `urlopen = Mock()` with
that we are replacing the real call for the one we want which is the fake one of
course.

Little by little, we're getting there, we have to be patient with ourselves.

Happy coding! =*
