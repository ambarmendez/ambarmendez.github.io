---
title: Testing Zero Version Zero
published: true
---
Guys! Thi is it! At least for now, for sure still will need more improvement
but that is what is all about, to keep doing it until we got it, right? So, in this
opportunity in which we feel our mocked server pretty much similar to the real server, 
we must be well aware of what we have here: _an implementation that behave similar to 
a web server_, in which we should exploit for our benefits since we count on:
  * an URL
  * the ability to request a second page
  * Normal errors during a HTTP conection.

In addition, what we do not have here:

> A forever parser for the real content of the website that is our target for
scraping because generally websites changes over time, as a result of new look
and feel refresh, for example.

The following case will test the extraction not just for the first page but
also for a next one, which means this case will be testing if the parser is
able to extract all the quotation and autthor values and location of a next page.

```python
def test_extracting_quotes_from_all_pages(self):
    mock_server_port = get_free_port()
    start_mock_server(mock_server_port)

    mock_quotes_url = 'http://localhost:{port}/'.format(port=mock_server_port)

    quote_list = get_quotes(mock_quotes_url)

    self.assertEqual(len(quote_list), 4)
```

Excelent! We have a `NameError: name 'get_quotes' is not defined`.

```python
def get_quotes(url):
    quote_list = []

    while url:
        with urllib.request.urlopen(url) as response:
            if response.getcode() == 200:
                quotes, next_page = extract_all(response.read())
                quote_list.extend(quotes)

                url = urljoin(response.geturl(), next_page) if next_page else None

        print(f'{url=}')

    return quote_list
```

In addition, the response for the new request should be added.

```python
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
All test passed! Perfect! But there are two methods very similar:
`extract_quotes(page)` from [Testing Level Zero](https://ambarmendez.github.io/Testing-Zero) and `extract_all(page)`,
which we just used. In fact, the only difference is what is returned. Although
in that moment we did it in that way because of how was simulated the response
just for one page. Now that scenario is completely different, we can get rid of it.

Now it's time for another test. One that takes into account a situation that
could happen.

```python
def test_extracting_quotes_when_timeout_error(self):
    mock_quotes_url = 'http://localhost:{port}/'.format(port=get_free_port())

    with patch('urllib.request.urlopen') as mock_urlopen:
        mock_urlopen.side_effect = URLError(408)
        with self.assertRaises(URLError):
            quote_list = get_quotes(mock_quotes_url)
            mock_urlopen.assert_called_once_with(mock_quotes_url)
            self.assertEqual(quote_list, [])
```

Happy coding! =)
