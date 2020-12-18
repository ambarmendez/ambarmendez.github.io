---
title: Testing Level Zero
published: true
---

The time has come when we need to start coding our solution. But before we get
into it, we are going to start for the simplest thing of them all as we did it
when we started this journey.
It is vital to keep in mind that, in the beginning, there was chaos and, now here
we are. That's right fellas! We are going to do this slowly, baby steps, let's take
advantage of this time of learning because we have plenty of time to be curious, experiment, fail and start again;
it is the best of them all. Besides, we are going to do it by testing over and over again, so the process is:

|  step  | action | comment |
| :----: | :----: | ------- |
| 1 | **_Add one test_** | It will fail because there is nothing yet. What we did before was just a figment of our imagination. Again, start with the simplest test as you can. |
| 2 | **_Make it pass_** | Please, just enough code to make the test pass no more. Even though you think you will need it in a not too far away future. Don't do it, please! The time will come, or should I said, the test will come when you would put that extra code. Always remember baby steps. |
| 3 | **_Refactoring_** | Just housekeeping for now, again the easiest way of refactoring: renaming variables and, extracting code to a method, either if the piece of code is repetitive or just for the sake of making the process clearer. |

Enough of talking and more action. Bellow the first test.

```python
import unittest


def extract_quotes(page):
    quote_list = []

    return quote_list


class TestQuoting(unittest.TestCase):

    def test_extracting_quotes_from_first_page(self):
        with open('example.html', 'r+b') as html_file:
            first_page = html_file.read()

        quote_list = extract_quotes(first_page)

        self.assertEqual(len(quote_list), 3)

if __name__ == '__main__':
    unittest.main()
```

Wait! WHAAAAAT? But why? Why reading from a file instead of making a request? For
writing the first and easy test, I believe that reading from a [file](https://ambarmendez.github.io/files/example.html) in
our first attempt was the simplest way of building it without complicated even
more than already is because of the nature of our problem which is relying on an
external resource. Thinking again while I am writing this, it is still
an external resource but a little bit more reliable and self-contain. Another
detail to take into consideration is the conditional after the test
case, `if __name__ == '__main__':`. There is a good article
in [freeCodeCamp](https://www.freecodecamp.org/news/if-name-main-python-example/) that
will help you with that.

Let's try our first case.

```
$ python tests.py
F
======================================================================
FAIL: test_extracting_quotes_from_first_page (__main__.TestQuoting)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "tests.py", line 17, in test_extracting_quotes_from_first_page
    self.assertEqual(len(quote_list), 3)
AssertionError: 0 != 3

----------------------------------------------------------------------
Ran 1 test in 0.000s

FAILED (failures=1)

```

Perfect! Adding just the necessary code for making the test pass.

```python
def extract_quotes(page):
    quote_list = []

    parser = MyHTMLParser()
    parser.feed(page.decode())

    print(parser.extracted_data)

    for index in range(0,len(parser.extracted_data),2):
        quote_list.append(
            (parser.extracted_data[index],
            parser.extracted_data[index+1])
        )

    return quote_list
```

Rerun the test, and we have a `NameError: name 'MyHTMLParser' is not defined`. Let's
bring our old pal `parser.py` into play by adding `from parser import MyHTMLParser`
in the first line of the file tests.py. If you are not sure about what happens before,
take a look at [Modules section](https://docs.python.org/3/tutorial/modules.html) from
The Standard Library Documentation. Remember to leave just the class definition, remove
everything else afterwards.

Yay! We did it! We start our long journey through **Test-Driven Development**
(TDD) since this is just for getting used to the idea of this useful way of coding.
We would go through this process many times until we have a significant range of
tests that will make a pretty good job when we need it the most and that would be by
helping us to find an issue or any error as a result of including new features or making
changes.

For the next level, it is a good idea to take a look at
- [Mock object library](https://docs.python.org/3/library/unittest.mock.html) and
- [Testing your code](https://docs.python-guide.org/writing/tests/)

Happy coding! =D
