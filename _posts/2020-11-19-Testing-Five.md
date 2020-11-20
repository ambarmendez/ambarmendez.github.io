---
title: Oh, What a Mess! Time for Housekeeping
published: true
---

We have been doing so many corrections that are difficult to know which tests are 
going to stay for good, with what implementation, even the names of the function 
does not help either, and that's okay there is no need for going fast. The main 
idea is learning together, being curious, and that my friends imply making a lot 
of mistakes, keep calm, and have fun. With that being said, let's make a quick 
review of what we have done and building up what is going to stay.

`no` -> **Iteration 0**: Reading from a file, and implementation in the same test file.

`not yet` -> **Iteration 1**: Using a context manager class-based, and implementation in the same test file.

`WINNER` -> **Iteration 2**: Using a mocked server, and still implementation in the same file.

Note that, step *2. URL value into a variable* is refering to a variable =P. In case
the variable gets forgotten, the following error is shown:

```
$ python tests.py
E
======================================================================
ERROR: test_extracting_quotes_from_first_page (__main__.TestQuoting)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "tests.py", line 105, in test_extracting_quotes_from_first_page
    response = fetch_quotes_toscrape()
  File "tests.py", line 32, in fetch_quotes_toscrape
    with urlopen('http://localhost/quotes') as response:
  File "/usr/lib/python3.8/urllib/request.py", line 222, in urlopen
    return opener.open(url, data, timeout)
  File "/usr/lib/python3.8/urllib/request.py", line 531, in open
    response = meth(req, response)
  File "/usr/lib/python3.8/urllib/request.py", line 640, in http_response
    response = self.parent.error(
  File "/usr/lib/python3.8/urllib/request.py", line 569, in error
    return self._call_chain(*args)
  File "/usr/lib/python3.8/urllib/request.py", line 502, in _call_chain
    result = func(*args)
  File "/usr/lib/python3.8/urllib/request.py", line 649, in http_error_default
    raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 404: Not Found

----------------------------------------------------------------------
Ran 1 test in 0.005s

FAILED (errors=1)
```
Use the traceback to understand what is happening with the code:

1. In this case, before `Traceback (most recent call last):` tells you which method and class is failing. Then, after that what file is, the number of the line, and specifically which sentence.
2. As you can see, I use `'http://localhost/quotes'` instead of **`'http://quotes.toscrape.com/'`** because it is easier for me to validate if it is really trying to reach a real website, which will give you a good response and making you believe that perhaps is using the mocked server.

Remember to add `QUOTES_URL = 'http://localhost/quotes'` and change `with urlopen(QUOTES_URL) as response:`.

**Iteration 3**: Adding response for a second page, and still implementation in the same file.

What I do every time that a new test is added, little by little I add instructions
the run tests and see what is the result. Let me show you.

```
$ python tests.py
127.0.0.1 - - [18/Nov/2020 21:34:34] "GET / HTTP/1.1" 200 -
E127.0.0.1 - - [18/Nov/2020 21:34:34] "GET / HTTP/1.1" 200 -
.
======================================================================
ERROR: test_extracting_quotes_from_all_pages (__main__.TestQuoting)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "tests.py", line 219, in test_extracting_quotes_from_all_pages
    quote_list = get_quotes(mock_quotes_url)
  File "tests.py", line 133, in get_quotes
    quotes, next_page = extract_all(response.read())
NameError: name 'extract_all' is not defined

----------------------------------------------------------------------
Ran 2 tests in 0.007s

FAILED (errors=1)
```

Ooopsie ... I forgot to include the definition of `extract_all(...)` method. Hope
that you guys could have figure out since from the traceback the method should
return a tuple of quotes and value for the next page.

```python
def extract_all(page):
    quote_list = []

    parser = MyHTMLParser()
    parser.feed(page.decode())

    for index in range(0, len(parser.extracted_data), 2):
        quote_list.append(
            (parser.extracted_data[index],
            parser.extracted_data[index+1])
        )

    return quote_list, parser.next_page
```

After remove `extract_quotes(...)` both test are failing:

1. `NameError: name 'extract_quotes' is not defined` from *test_extracting_quotes_from_first_page* which should be removed since the mocked server is able to give a second page response, therefore there is no more use for
`fetch_quotes_toscrape()` it's been removed as well.
2. `self.assertEqual(len(quote_list), 4)` in *test_extracting_quotes_from_all_pages* specifically `AssertionError: 3 != 4` which means the mocked server still does not have a response for a second page and the reference to it

Here comes the tricky part! The second test is made by using patch, and it is
very important [where to patch](https://docs.python.org/3.8/library/unittest.mock.html#where-to-patch)
since it dependes how the import sentence was made.

**Iteration 4**: Adding execution tests, and different files for implementation and test.

At last, testing for the execution. Firstly, before separating the implementation from 
the tests, execute tests, just to be sure and be sure. Next, make the separation and 
execute tests again, to keep our sanity. Now, let's add the first test `test_extract_execution_by_cli(...)`, 
execute tests, obviously, it fails, there is nothing implemented yet. Now, we add the 
simplest implementation to make the test pass.  

Take into account that, in this iteration the `get_quotes()` call does not have a 
value given as a parmeter since in the method definition a default value is set.

Happy coding!
