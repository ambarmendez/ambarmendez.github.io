---
layout: post
title: Testing Level Zero Iteration Seven
published: true
---

At this moment, we have our precious data but just when the script is
executing. Once the script is over we are left with nothing, everything
was available in memory. If we want to make the data available later, we
need to store it. But before digging into that, let's organize, again, what
we already have. Last time, we were moving pieces of code belonging to
the test. This time let's apply something similar to the `parser.py` file.

The first part, **extraction** is acceptable to leave it for now in
the same module where the parsing process is taking place, `parser
module`. If the time comes when we need to separate them because again
is too crowded, many things in the same place, at that moment would be
a good idea to reorganize.

The new part, **persistence**. Let's create a new module and put
everything related to that in there. Also, put parser and
persistence modules inside a new folder called `quotation`. Execute tests and
update any reference.

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

        self.assertTrue(len(quote), 2)
```

Include that piece of code into a new file `test_persistence.py`. Run
the test and of course there is no get_one_quote function defined.

```python
import os
from tempfile import NamedTemporaryFile

def get_one_quote(filename='quotation.txt'):
    aux = NamedTemporaryFile(mode='w+t', suffix='.tmp', delete=False, dir='.')
    with open(filename) as file:
        quote = file.readline().rstrip('\n')
        author = file.readline().rstrip('\n')

        for line in file:
            aux.write(line)

    aux.close()
    os.rename(aux.name, filename)

    return quote, author
```

However, this time instead of solving the problem is giving us another
error which is `FileNotFoundError: [Errno 2] No such file or directory: 'quotation_test.txt'`.
Let's ask for that error in our test.

```python
def test_one(self):
  with self.assertRaises(FileNotFoundError):
      quote = get_one_quote()
```

Rerun the tests and everything is working. Now let's se what happened when the file
exists. For that, let's patch up reading from a file.

```python
def test_two(self):
    content = 'Some problems are better evaded than solved.\nC. A. R. Hoare\n'
    with patch('quotation.persistence.open', mock_open(read_data=content)) as file:
        quote = get_one_quote()

    self.assertTrue(len(quote), 2)
```

Run the test and everything is okey but the execution of the test left the directory
dirty, with a file that it should not be left there. The origin of that file is
because of `os.rename` so let's use a [temporary directory](https://docs.python.org/3/library/tempfile.html#tempfile.TemporaryDirectory)
where we can create files as we needed without patching out open a file and upon
context manager completition the directory and its content is removed from the
file system.

```python
def test_two(self):
    content = 'Some problems are better evaded than solved.\nC. A. R. Hoare\n'

    with TemporaryDirectory(dir='.') as tmpdirname:
        quote_file = os.path.join(tmpdirname, 'quotation_test.txt')

        with open(quote_file, 'w') as file:
            file.write(content)

        quote = get_one_quote(quote_file)

    self.assertTrue(len(quote), 2)
```

Rerun the test and everything is working. But is it really working as
it should be working? Something is missing.

So let's keep thinking about what should be done for making it better?

Happy coding! =D
