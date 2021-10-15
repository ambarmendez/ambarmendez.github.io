---
layout: post
title: Simple Server
published: true
---

Before keep going with our mini-project about [Scraping from Scratch](https://ambarmendez.github.io/Scraping-From-Scratch); in this opportunity, we are going to use an elemental server from [PyMOTW-3](https://pymotw.com/3/http.server/). So have ready to use two command-line interfaces: one for the server and another for the client.

```python
from http.server import BaseHTTPRequestHandler, HTTPServer
from urllib import parse

class GetHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        parsed_path = parse.urlparse(self.path)
        message_parts = [
            'SERVER VALUES:',
            'server_version={}'.format(self.server_version),
            'sys_version={}'.format(self.sys_version),
            'protocol_version={}'.format(self.protocol_version),
            '',
            'CLIENT VALUES:',
            'client_address={}'.format(
                self.client_address),
            'command={}'.format(self.command),
            'path={}'.format(self.path),
            'real_path={}'.format(parsed_path.path),
            'query={}'.format(parsed_path.query),
            'request_version={}'.format(self.request_version),
            '',
            'HEADERS RECEIVED:',
        ]
        for name, value in sorted(self.headers.items()):
            message_parts.append(
                '{}={}'.format(name, value.rstrip())
            )
        message_parts.append('')
        message = '\r\n'.join(message_parts)
        self.send_response(200)
        self.end_headers()
        self.wfile.write(bytes(message, "utf8"))

def run(server_class=HTTPServer, handler_class=GetHandler):
    server_address = ('localhost', 8080)
    httpd = server_class(server_address, handler_class)

    print('Starting server, use <Ctrl-C> to stop')
    httpd.serve_forever()


run()
```

And our old and straightforward client. As you can see from our [previous example](https://ambarmendez.github.io/Scraping-Quotes) compare to the one is shown below the URL is completely different, which means the server is our computer. Remember, this will help us see what information and location inside the message is receiving the server.

```python
import urllib.request

url = 'http://localhost:8080/'

data = {}
data['name'] = 'Somebody Here'
data['location'] = 'Northampton'
data['language'] = 'Python'

url_values = urllib.parse.urlencode(data)
full_url = f'{url}?{url_values}'

print(f'url_values={url_values}')
print(f'full_url={full_url}')

user_agent = 'Mozilla/5.0 (Windows NT 6.1; Win64; x64)'
headers = {'User-Agent': user_agent}

req = urllib.request.Request(full_url, headers=headers)

with urllib.request.urlopen(req) as response:
   the_page = response.read()
   print(the_page.decode())
```

In an environment of scraping a website, the server where we are trying to extract data awaits a specific request. We need to build what the server for that website is waiting to receive then it will send back the appropriate response.

C'mon fellas! Explore, change the parameters, what about to use the browser on your phone. In this opportunity, we are going beyond the regular `Hello world`, but this is a little step further. Be patient with yourself, of course, there will be mistakes, a lot of error and panic, but it is ok that's what makes this life worth living.

See you later and remember

Keep calm! Keep coding! =D
