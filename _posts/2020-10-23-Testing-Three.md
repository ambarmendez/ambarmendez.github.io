---
title: Testing Level Zero Iteration Three
published: true
---
Guys! Thi is it! At least for now, for sure still will need more improvement
but that is what is all about, to keep doing it until we got it, right? So, in this
opportunity in which we feel our mocked server pretty much similar to the real server,
we must be well aware of what we have here: _an implementation that behaves similar to
a web server_, in which we should exploit for our benefits since we count on:
  * an URL
  * the ability to request a second page
  * Normal errors during an HTTP connection.

What we do not have:

> A forever parser for the real content of the website that is our target for
scraping because generally websites changes over time, as a result of a new look
and feel refresh, for example.

With that being said. Let's keep working with our tests scenarios. The following
case will replace `test_extracting_quotes_from_first_page` the extraction not just for the first page but
also for the next one, which means this case will be trying if the parser is
able to extract all the quotation and author values and location of the next page.

```python
def test_extracting_quotes_from_all_pages(self):
    mock_server_port = get_free_port()
    start_mock_server(mock_server_port)

    mock_quotes_url = 'http://localhost:{port}/'.format(port=mock_server_port)

    quote_list = get_quotes(mock_quotes_url)

    self.assertEqual(len(quote_list), 4)
```

Excellent! We have a `NameError: name 'get_quotes' is not defined`.

```python
def get_quotes(url):
    quote_list = []

    while url:
        with urlopen(url) as response:
            if response.getcode() == 200:
                quotes, next_page = extract_all(response.read())
                quote_list.extend(quotes)

                url = urljoin(response.geturl(), next_page) if next_page else None

        print(f'{url=}')

    return quote_list

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

Also, the response to the new request should be added.

```python
class MockServerRequestHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.end_headers()

        if self.path == '/page/2':
            response_content = """
                <html><head><title>Mock Server</title></head><body>
                <div class="quote">
                    <span class="text" itemprop="text">“Surely the day will come when color means nothing more than the skin tone, when religion is seen uniquely as a way to speak one's soul, when birth places have the weight of a throw of the dice and all men are born free, when understanding breeds love and brotherhood.”</span>
                    <span>by <small class="author" itemprop="author">Josephine Baker</small>
                    <a href="/author/Josephine-Baker">(about)</a>
                    </span>
                </div> </body></html>
            """

            self.wfile.write(response_content.encode('utf-8'))

            return
```
And the reference to the second page inside the response for the home or first page, just before the body closing tag.
```html
  <nav>
      <ul class="pager">
          <li class="next">
              <a href="/page/2">Next <span aria-hidden="true">&rarr;</span></a>
          </li>
      </ul>
  </nav>
</body></html>
```

Run the test and solve the missing library: `from urllib.parse import urljoin`.

All test passed! Perfect! But there are two methods very similar:
`extract_quotes(page)` from [Testing Level Zero](https://ambarmendez.github.io/Testing-Zero)
and `extract_all(page)`, which we just used. The only difference is what is returned. At
that moment we did it in that way because of how was simulated the response
just for one page. Now that scenario is completely different we can get rid of it,
including `test_extracting_quotes_from_first_page`, `fetch_quotes_toscrape` and `extract_quotes`.

Now it's time for another test. One that takes into account a situation that
could happen.

```python
def test_extracting_quotes_when_timeout_error(self):
    mock_quotes_url = 'http://localhost:{port}/'.format(port=get_free_port())

    with patch('tests.urlopen') as mock_urlopen:
        mock_urlopen.side_effect = URLError(408)
        with self.assertRaises(URLError):
            quote_list = get_quotes(mock_quotes_url)
        mock_urlopen.assert_called_once_with(mock_quotes_url)
```
Remember, patch up depends on how the tests are going to be run and how it is made
the import. In this case, the tests are being executed by `python -m unittest`,
and `tests` is the module where the test cases are.

Run the test and solve the missing library: `from urllib.error import URLError`.

Happy coding! =)
