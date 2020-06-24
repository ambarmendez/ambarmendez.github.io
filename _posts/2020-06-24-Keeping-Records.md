---
title: Keeping Records
published: true
---

In this opportunity, we are going to introduce another tool that help us to keep
a history of our code, which is called `git`, a version control system. Why this
is important? Because change is the only thing is permanent not only in life but
even more in programming. While we are learning there will be a lot of changes.

That takes me to another observation about the exercises; if you are working
along, trying to solve the challenges that we are facing during this journey,
please **keep it don't throw it away**, just because it's different from mine.

Remember, at the beginning

> 1. We play around trying to know a little bit more
> 2. Then, we start to build from there.

So with that being said, below you will find my first version of `MyHTMLParser`
which extract all quotes from all pages.

```python
from html.parser import HTMLParser

class MyHTMLParser(HTMLParser):
    def __init__(self):
        HTMLParser.__init__(self)
        self.nested_level = 0
        self.recording = False
        self.extracted_data = []
        self.next_page = ''

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
        if tag == 'li':
            if self.is_pager(attrs):
                self.nested_level += 1
                return
        if tag == 'a':
            if self.nested_level == 1:
                self.next_page = self.get_link(attrs)
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

    def is_pager(self, attrs):
        for attr, value in attrs:
            if attr == 'class':
                if value.find('next') != -1:
                    return True
        return False

    def get_link(self, attrs):
        print(attrs)
        if len(attrs) == 1 and attrs[0][0] == 'href':
            return attrs[0][1]

    def handle_data(self, data):
        if self.recording:
            self.extracted_data.append(data.lstrip('“').rstrip('”'))

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
        if tag == 'li' and self.nested_level == 1:
            self.nested_level = 0
            return


import urllib.request
import urllib.parse

with urllib.request.urlopen('http://quotes.toscrape.com/') as response:
    the_page = response.read()

    parser = MyHTMLParser()
    parser.feed(the_page.decode())

    print(parser.extracted_data)
    print('LOOP ... next_page=', parser.next_page)

    while parser.next_page:
        absolute_next_url = urllib.parse.urljoin(response.geturl(), parser.next_page)
        print(absolute_next_url)

        with urllib.request.urlopen(absolute_next_url) as response:
            the_page = response.read()

            parser = MyHTMLParser()
            parser.feed(the_page.decode())
            print(parser.extracted_data)

    print('end')
```

Lastly, we start keeping records from this moment on ...

```
$ mkdir quoting
$ mv MyHTMLParser.py quoting/
$ cd quoting
$ git init
$ echo '__pycache__/' > .gitignore
$ git add .
$ git commit -m 'Extract quotes from all pages'
```

BUT WHAAAAAAT??? Calm down people that's just simple stuff like always =D

> 1. Create a directory called `quoting/`
> 2. Move the file which contains the code that we've being using, in my case is called `MyHTMLParser.py`
> 3. Change to the directory we just created.
> 4. Initialize repository
> 5. Add `.gitignore` file since is not necesary to keep records of compiled python
> 6. Add changes to git
> 7. Commit the tracked changes.

Yes, it is! It is that how much you need to learn and the way is long but at the
same time is fun because there is always something new to learn.

As always it's not easy but be patience with yourself because there is ligth at
the end of the tunnel, or there isn't? C'mon lets find out!

Happy learning! =D or maybe not! =O

Ha ha ha ... Just kidding! It's up to you as always! =*
