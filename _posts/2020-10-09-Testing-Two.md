---
title: Testing Level Zero Iteration Two
published: true
---

Before jump into action remember the idea is getting close to that ideal escenario
where there is no need to rely on any external dependencies and each test is self-sufficient
but still it should be very similar to the original environment. That's why in this opportunity a
[server will be emulated](https://realpython.com/testing-third-party-apis-with-mock-servers/), thanks to Real Python fellas =)

Now from the solution presented there to here what needs to be done is:

## 1. Execute tests before doing any change

As a general good habit, always execute test first just for the sake of a good sleep
that everything is working properly and there is no need of hunting down any ghost
of random code.

## 2. URL value into a variable

It will be easier to make changes over a URL variable rather than have it as a
fixed value into the method's call. Run tests and everything is okey, no big deal.

## 3. Instead of mocking the method, the server will be mocked

Remember the little piece of code that change everything `urlopen = Mock()`. Well
in this opportunity that piece of code and anything related to that will be removed,
`MockResponse()` and `response_value` just to be clear. As a result, `urlopen()`
must be declared as the real thing, `from urllib.request import urlopen`. Excelent,
run tests and now we have a `urllib.error.HTTPError: HTTP Error 404: Not Found`.

## 4. Include the Mock Server

Pretty much the server follows the same logic as [the simple server we did before](https://ambarmendez.github.io/Simple-Server).

```python
class MockServerRequestHandler(BaseHTTPRequestHandler):
     def do_GET(self):
      self.send_response(200)
      self.end_headers()

      response_content = """
          <html><head><title>Mock Server</title></head><body>

          <div class="quote">
              <span class="text" itemprop="text">“The world as we have created it is a process of our thinking. It cannot be changed without changing our thinking.”</span>
              <span>by <small class="author" itemprop="author">Albert Einstein</small>
              <a href="/author/Albert-Einstein">(about)</a>
              </span>
          </div>

          <div class="quote">
              <span class="text" itemprop="text">“It is our choices, Harry, that show what we truly are, far more than our abilities.”</span>
              <span>by <small class="author" itemprop="author">J.K. Rowling</small>
              <a href="/author/J-K-Rowling">(about)</a>
              </span>
          </div>

          <div class="quote">
              <span class="text" itemprop="text">“There are only two ways to live your life. One is as though nothing is a miracle. The other is as though everything is a miracle.”</span>
              <span>by <small class="author" itemprop="author">Albert Einstein</small>
              <a href="/author/Albert-Einstein">(about)</a>
              </span>
          </div>

          </body></html>
      """
      
      self.wfile.write(response_content.encode('utf-8'))

      return


def get_free_port():
    s = socket.socket(socket.AF_INET, type=socket.SOCK_STREAM)
    s.bind(('localhost', 0))
    address, port = s.getsockname()
    s.close()
    return port


def start_mock_server(port):
    mock_server = HTTPServer(('localhost', port), MockServerRequestHandler)

    # Start running mock server in a separate thread.
    # Daemon threads automatically shut down when the main process exits.
    mock_server_thread = Thread(target=mock_server.serve_forever)
    mock_server_thread.setDaemon(True)
    mock_server_thread.start()
```

The main difference is how will be executed since we need it to be up and running
when the tests are being executed as well that's when [Thread](https://docs.python.org/3.8/library/threading.html)
comes handy, think of it as a video game because as far as it can be seen everything is taking place at
once. Run tests and solve the missing libraries: `from http.server import BaseHTTPRequestHandler, HTTPServer`, `import socket` and `from threading import Thread`.

## 5. Test by using the mock server

Finally, making a request to a server that it is control by the test itself.

```python
mock_server_port = get_free_port()
start_mock_server(mock_server_port)

mock_quotes_url = 'http://localhost:{port}/'.format(port=mock_server_port)

with patch.dict('__main__.__dict__', {'QUOTES_URL': mock_quotes_url}):
    response = fetch_quotes_toscrape()

quote_list = extract_quotes(response)

self.assertEqual(len(quote_list), 3)
```

Beautiful! Happy coding! <:o)
