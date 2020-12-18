---
title: Keeping Records
published: false
---

What now? Before answering that we have to ask ourselves what we have. When
the execution of our [MyHTMLParser](https://ambarmendez.github.io/Keeping-History)
finished, we are left with nothing, not even the batch of quotes from the last page.
In other words, our precious data should be available afterwards for whatever we want
to do with it. For this, The Python Standard Library provides
[sqlite3 module](https://docs.python.org/3/library/sqlite3.html). But before that, let's
identify when and what we need to do:

 when | action
----- | ---------
at the beginning | database creation
after db creation | quote insertion
after db creation | table inspection

In addition to the **database creation** we're going to include the creation of
the table for storing the quotes extracted from the website, and it would be
straightforward, just the quote itself and the author.

```python
import sqlite3

connection = sqlite3.connect('quotation.db')

with connection:
  connection.execute('CREATE TABLE quotes (quotation, author)')

connection.close()
```

But before **quote insertion** a cleaning must be done since we need to provide
a list where each item on the list needs to be (quotation, author) tuple so we
have to redefine the elements extracted from the parser.

```python
quote_list = []

for index in range(0,len(extracted_data),2):
    quote_list.append((extracted_data[index], extracted_data[index+1]))
```

Then the data is ready for the insertion in the database.

```python
connection = sqlite3.connect('quotation.db')

with connection:
    connection.executemany(
        'INSERT INTO quotes (quotation, author) VALUES (?, ?);',
        [('The eyes those silent tongues of love.', 'Miguel de Cervantes')])

connection.close()
```

Finally, for the **table inspection** and just for the sake of sleeping in peace.

```python
connection = sqlite3.connect('quotation.db')

for row in connection.execute('SELECT * FROM quotes;'):
    print(row)

connection.close()
```

But WAIT! WHAT? This is it? But This is just pieces of code, even some people
would say: _`This is nothing!`_ and if that so what would you do to make it
better, that possibility is always on the table friends!

So let's keep thinking what should be done for making it better?

Happy coding! =D
