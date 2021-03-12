---
title: Keeping Records
published: true
---
At this moment, we have our precious data but just when the script is
executing. Once the script is over we are left with nothing, everything
was available in memory. If we want to make the data available later, we
need to store it. But before digging into that, let's organize, again, what
we already have. Last time, we were moving pieces of code belonging to
the test. This time let's apply something similar to the `parser.py` file.

The first part, **extraction** is acceptable to leave for now  in
the same module where the parsing process is taking place, `parser
module`. If the time comes where we need to separate them because again
is too crowded, many things in the same place, at that moment would be
a good idea to reorganize.

The new part, **persistence**. Let's create a new module and put
everything related to that in there. Also, put parser and
persistence modules inside a new folder called `quotation`.

Now, let's start building our test case for persistence. As usual, this
will be our first attempt, there will be mistakes and mess but there
will be the tests and intuition perhaps to keep us on track. Do not worry if you
are just a beginner and do not have intuition yet. That is the reason
we are doing this slowly and many times because that will help us to
build that intuition. Enough talking and more action.

```python
class TestPersistence(unittest.TestCase):
    def test_one(self):
        quote = get_one_quote()
        print(quote)
        self.assertTrue(len(quote), 2)
```

Include that piece of code into a new file `test_persistence.py`. Run
the test and of course there is no get_one_quote function defined.

```python
import os


def get_one_quote(filename='quotation_test.txt'):
    with open(filename) as file, open('aux.txt', mode='w') as aux:
        quote = file.readline().rstrip('\n')
        author = file.readline().rstrip('\n')

        for line in file:
            aux.write(line)

    os.remove(filename)
    os.rename('aux.txt', filename)

    return quote, author
```

However, this time instead of solving the problem is giving us another
error which is `FileNotFoundError: [Errno 2] No such file or directory: 'quotation_test.txt'` and that is completely true. There is
no such file. In this case, for the file to exist the extraction must
be done beforehand. There are two possible solutions:

1. If there is no such file, execute the extraction automatically. Or

2. Translate the error to a message for the user which would tell him: "Extraction should be done before asking for a quote".


Let's go for the first one. But as a test, let's simulate that the
extraction was made at some point, for this the setUp and tearDown
method definitions would help us with that.

```python
class TestPersistence(unittest.TestCase):

    def setUp(self):
        self._filename = 'quotation_test.txt'
        default_content = [
            ('We read Knuth so you don’t have to.', 'Tim Peters'),
            ('The purpose of computing is insight, not numbers.', 'R. Hamming'),
            ('Unix never says "please."','Rob Pike'),
            ('Some problems are better evaded than solved.', 'C. A. R. Hoare')
        ]
        with open(self._filename, 'w') as file:
            for quote in default_content:
                file.write(f'{quote[0]}\n')
                file.write(f'{quote[1]}\n')

    def tearDown(self):
        os.remove(self._filename)
```

Rerun the test and everything is working. But is it really working as
it should be working? Something is missing.

So let's keep thinking about what should be done for making it better?

Happy coding! =D
