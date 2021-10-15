---
layout: post
title: Testing Level Zero Iteration Six
published: true
---

Before moving on to the next set of test. Let's reorganize what we have done since
everything is in the same file making it crowded and difficult to follow. There are
three detectable sections:

- proper tests
- mock server and response
- methods subject to test

Python provides a way to distribute pieces of code into separate files, called
modules. Then use the import statement where we need to use that code. Until now, we
have two files, `parser.py`, which contain only the `MyHTMLParser` definition, and
`tests.py`, the crowded one. The main idea is to leave just the proper tests alone
and import the rest.

## 1. Execute tests before doing any change

That's right, friends! Make it a habit, a good one. Now an excellent question will
be but how because remember that it could be done as a script `python tests.py`
or throw simple test discovery `python -m unittest`, which will be our target. To
accomplish that, we have to transform our `tests.py` into a package.

## 2. Tests into a package

Firstly, let's create a directory called `tests` and move `tests.py` file to that
directory. Then inside `tests` directory create two new files:

- **__init__.py** -> empty for now. Convert the directory into a package
- **mockserver.py** -> `MockResponse`, `get_free_port`, `start_mock_server` and `MockServerRequestHandler` definitions

Run tests, and everything falls apart. To begin with, easy errors, the ones that we
know we have. For example: `NameError: name 'get_free_port' is not defined` include
`from .mockserver import get_free_port, start_mock_server, MockResponse` inside tests.py
file, by using the dot before module's name, also known as [relative import](https://docs.python.org/3/reference/import.html#package-relative-imports).
Please be aware of this detail since it just makes sense inside a package. A clue
to keep in mind when the following errors are thrown `ValueError: attempted relative import beyond top-level package`
or `ImportError: attempted relative import with no known parent package`. Try it
just by adding two dots before the name's module, in this case `from ..parser import get_quotes`.

Rerun tests, and it keeps broken. The other error `AttributeError: <module 'tests' from '~/project/tests/__init__.py'> does not have the attribute 'urlopen'`
is related to the mocking part, which it cannot find `urlopen`. Since we moved
the tests.py file inside a directory called with the same name `tests`, we have to add
another `tests` inside the patch context manager
`with patch('tests.tests.urlopen')`. Hit it again, and great, everything is marvellous. Yay!

## 3. The rest into parser

Remember, the idea is to leave just the tests inside the `tests.py` file, so we already moved
mock server and response, which means `get_quote` and `extract_all` still needed to be
relocated. Those functions definitions are part of the extraction process, so there better
off inside the `parser` module. Don't forget that variable definition as well, `QUOTES_URL`.

Run tests, and everything is broken again. But, wait! it is the same error we solve before
`AttributeError: <module 'tests.tests' from '~/project/tests/tests.py'> does not have the attribute 'urlopen'`. Although this time is looking up inside the `tests.tests` module instead of the `parser` module, which
is now where it is being used. So we have to update again where have to be made the patching up
`with patch('parser.urlopen')`. Rerun the tests, and everything makes sense now.

Happy coding! =)
