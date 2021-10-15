---
layout: post
title: Testing Level Zero Iteration Four
published: true
---

Wait! WHAT? We have just two test cases! Yet we have been doing so many
corrections. Remember, the main idea is learning together, being curious, and
making a lot of mistakes and that my friends take time. So keep calm,
and have fun. When the feeling isn't fun anymore, it is better to take a break,
go ahead and do something different.

Now back to business. What about a timeout error when fetching a second page? Or
verify that the website keeps the same HTML rendering? For this iteration, we
are going to tackle the first situation mention before.

```python
def test_extracting_quotes_when_timeout_error_during_second_page_extraction(self):
    response_value = bytes("""
      <div class="quote">
            <span class="text" itemprop="text">“The greatest enemy of knowledge is
              not ignorance, it is the illusion of knowledge.”
            </span>
            <span>by <small class="author" itemprop="author">Stephen Hawking</small>
            <a href="/author/Stephen-Hawking">(about)</a>
            </span>
        </div>

        <div class="quote">
            <span class="text" itemprop="text">“People think that computer science is the art
              of geniuses but the actual reality is the opposite, just many people doing things
              that build on each other, like a wall of mini stones.”
            </span>
            <span>by <small class="author" itemprop="author">Donald Knuth</small>
            <a href="/author/Donald-Knuth">(about)</a>
            </span>
        </div>

        <div class="quote">
            <span class="text" itemprop="text">“Imagination is more important than knowledge.  For
              knowledge is limited, whereas imagination embraces the entire world, stimulating
              progress, giving birth to evolution.”
            </span>
            <span>by <small class="author" itemprop="author">Albert Einstein</small>
            <a href="/author/Albert-Einstein">(about)</a>
            </span>
        </div>

        <nav>
            <ul class="pager">
                <li class="next">
                    <a href="/next/1">Next <span aria-hidden="true">&rarr;</span></a>
                </li>
            </ul>
        </nav>
    """, 'utf-8')

    with patch('tests.urlopen') as mock_urlopen: # (1)
        values = { # (2)
          'http://quotes.toscrape.com/': MockResponse(response_value),
          'http://quotes.toscrape.com/next/1': MockResponse('', code=408)
        }

        def side_effect(arg): # (3)
            return values[arg]

        mock_urlopen.side_effect = side_effect # (4)

        self.assertEqual(mock_urlopen.call_args_list, [])

        with self.assertRaises(URLError):
            quote_list = get_quotes()

            self.assertEqual(quote_list, [])

        self.assertNotEqual(mock_urlopen.call_args_list, [])
        mock_urlopen.assert_has_calls([
          call('http://quotes.toscrape.com/'), call('http://quotes.toscrape.com/next/1')],
          any_order=False
        )
```

My, oh, my, so many things are happening in just one test.

(1) Replacing the real `urlopen` method for a mocked one.
(2) Pairing value of the argument during the call with an instance of [`MockResponse(...)`](https://ambarmendez.github.io/Testing-One) for the first time, and an exception through `MockResponse('', code=408)` for the second one.
(3) Using an inner function enables us to have access to variables, `values`, inside the outer function. Even more, it enables us to have access to that parameter received by the mocked method `urlopen` as well and return the class of interest. In a simplify action:

```
>>> def outer(first):
...     def inner(second):
...             return f'inner({second}), outer({first})'
...     return inner
...
>>> example_one = outer('AAA')
>>> example_two = outer('BBB')
>>> example_one('second arg')
'inner(second arg), outer(AAA)'
>>> example_two('just 4 fun')
'inner(just 4 fun), outer(BBB)'

```

(4) Finally, assigning the inner function which will return the class for the actions to be in place.

The result for the excecution test:

```
$ python -m unittest
127.0.0.1 - - [18/Dec/2020 08:11:39] "GET / HTTP/1.1" 200 -
url='http://localhost:40591/page/2'
127.0.0.1 - - [18/Dec/2020 08:11:39] "GET /page/2 HTTP/1.1" 200 -
url=None
..E
======================================================================
ERROR: test_extracting_quotes_when_timeout_error_during_second_page_extraction (tests.TestQuoting)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/tests.py", line 223, in test_extracting_quotes_when_timeout_error_during_second_page_extraction
    quote_list = get_quotes()
TypeError: get_quotes() missing 1 required positional argument: 'url'

----------------------------------------------------------------------
Ran 3 tests in 0.009s

FAILED (errors=1)
```

What about passing a default value to url, something like `def get_quotes(url=QUOTES_URL):`,
that would fix the error instantly. Hit it again.

```
$ python -m unittest
127.0.0.1 - - [18/Dec/2020 08:24:14] "GET / HTTP/1.1" 200 -
url='http://localhost:55187/page/2'
127.0.0.1 - - [18/Dec/2020 08:24:14] "GET /page/2 HTTP/1.1" 200 -
url=None
..E
======================================================================
ERROR: test_extracting_quotes_when_timeout_error_during_second_page_extraction (tests.TestQuoting)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "/tests.py", line 223, in test_extracting_quotes_when_timeout_error_during_second_page_extraction
    quote_list = get_quotes()
  File "/tests.py", line 20, in get_quotes
    url = urljoin(response.geturl(), next_page) if next_page else None
AttributeError: 'MockResponse' object has no attribute 'geturl'

----------------------------------------------------------------------
Ran 3 tests in 0.008s

FAILED (errors=1)
```

Now, include the method definition for `geturl` and change the `__enter__(...)`
method to raise the exception inside `class MockResponse:`.

```python
def geturl(self):
    return 'http://quotes.toscrape.com/'

def __enter__(self):
    if self.code == 408:
        raise URLError(408)
    return self
```

Rerun the tests and solve the missing library: `from unittest.mock import patch, call`.

And the last test for now.

```python
def test_extracting_quotes_first_page_response_from_real_server(self):
    with urlopen('http://quotes.toscrape.com/') as response:
        quotes, next_page = extract_all(response.read())

    self.assertIsNotNone(next_page)
    self.assertEqual(len(quotes), 10)
```

Since more often than not, websites change how they look. This test is crucial
because if the website changes how it looks then the parser would not be able to
get the quotes and not because of a bug, but for something beyond a predictable
scenario.

Happy coding! <:o)
