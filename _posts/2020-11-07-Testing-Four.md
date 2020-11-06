---
title: Testing Version Zero Iterarion One
published: true
---

What about execution? Because until now we have tested just for the implementation
but nothing about how it will be executed. Without too much thinking, let's stick
with what have done til now, as a command line application which means executing
the python file as a script. In addition, let's keep the python files in the same directory, one for the tests and one for the script to be executed.

Let's begin with the execution test

```python
def test_extract_execution_by_cli(self):
    mock_server_port = get_free_port()
    start_mock_server(mock_server_port)

    mock_quotes_url = 'http://localhost:{port}/'.format(port=mock_server_port)

    result = subprocess.run(['python3', 'MyHTMLParser.py'], env={'QUOTES_URL': mock_quotes_url}, text=True, capture_output=True)

    self.assertEqual('4 quotes extracted', result.stdout)
```

In this first attempt, an environment variable is being set for `QUOTES_URL`. As
result, the variable value changes to `os.getenv('QUOTES_URL', 'http://quotes.toscrape.com/')`. Remember to add the following code in `MyHTMLParser.py` in the end of the file.

```python
if __name__ == '__main__':
    get_quotes()
```

Executing the test and we have our glorius fail:

```
FAIL: test_extract_execution_by_cli (__main__.TestMockServer)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "tests.py", line 390, in test_extract_execution_by_cli
    self.assertEqual('4 quotes extracted\n', result.stdout)
AssertionError: '4 quotes extracted\n' != "url='http://localhost:48313/page/2'\nurl=None\n"
- 4 quotes extracted
+ url='http://localhost:48313/page/2'
+ url=None
```

Notice the messaje after the `AssertionError`, there are two things to notice.
First one, it is printing `url=...` so `print(f'{url=}')` should change to what
we want to print in order to success the assertion `print(f'{len(quote_list)} quotes extracted')`
but align with the while statement. Lastly, after None there is `\n` as part of
the string capture for the print during the execution of the method. So let's
add the same character inside the assertion `self.assertEqual('4 quotes extracted\n', result.stdout)`.

YAY! But Hold your horses! Still we need to add another test.

```python
def test_extract_execution_return_code_as_process(self):
    mock_server_port = get_free_port()
    start_mock_server(mock_server_port)

    mock_quotes_url = 'http://localhost:{port}/'.format(port=mock_server_port)
    result = subprocess.run(['python3', 'MyHTMLParser.py'], env={'QUOTES_URL': mock_quotes_url}, capture_output=True)

    self.assertEqual(result.returncode, 0)
```
After a script terminates, it should return a exist status, 0 on success by
convention.

Thanks to [Anthony](https://www.youtube.com/watch?v=sv46294LoP8) for the idea,
by the way.

Happy coding!
