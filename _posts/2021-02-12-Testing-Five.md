---
layout: post
title: Testing Level Zero Iteration Five
published: true
---

To close this set of test, we will add one more test and then clean the mess afterwards. Adding another error test will be similar to the one we already have. But instead of a timeout error, a page not found error will be tested. Remember to run all test beforehand. The code for the new test is shown below.

```python
def test_extraction_but_page_not_found_error(self):
    mock_quotes_url = 'http://localhost:{port}/'.format(port=get_free_port())

    with patch('tests.urlopen') as mock_urlopen:
        mock_urlopen.side_effect = HTTPError(mock_quotes_url, 404, '', {}, None)
        with self.assertRaises(HTTPError):
            quote_list = get_quotes(mock_quotes_url)
        mock_urlopen.assert_called_once_with(mock_quotes_url)
```

Everything is okay, more of the same, Nothing out of the ordinary.

To summarize what it is being tested until now:

* Extraction from all pages - [Iteration 3](https://ambarmendez.github.io/Testing-Three)
* Extraction when timeout error - [Iteration 3](https://ambarmendez.github.io/Testing-Three)
* Extraction when timeout error during second-page extraction - [Iteration 4](https://ambarmendez.github.io/Testing-Four)
* Extraction from the first-page response from the actual server - [Iteration 4](https://ambarmendez.github.io/Testing-Four)
* Extraction but a page not found error is thrown

Reorganizing and grouping the tests based on what is being tested, now we have:

* TestQuotingMockedServer.
  * Extraction from all pages

- TestQuotingError.
  - Extraction but a page not found error is thrown
  - Extraction when timeout error
  - Extraction when timeout error during second-page extraction

- TestHittingRealServer.
  - Extraction from the first-page response from the actual server

The code itself remains the same; what changed is where is located since all was attached to one class. In `TestQuotingError`, a new method class needs to be added to make mock_quotes_url variable available for everyone inside the class.

```python
def setUp(self):
    self.mock_quotes_url = 'http://localhost:{port}/'.format(port=get_free_port())
```

Happy coding! =)
