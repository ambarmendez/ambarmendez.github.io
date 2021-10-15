---
layout: post
title: Testing Level Zero Iteration Eight
published: true
---


Hold on! Ideally, interaction with database, file system or any external source
should be avoided since tests should be self-sufficient, and most important, it is
about what we want to solve in the application. In fact, it is not about testing
if the data is being stored or not. It is completely out of boundary to include
tests related to that since external resources count on an extensive set of
tests on their own. For that reason, let's mock writing to a file.

```python
def test_storing_extracted_quotes(self):
    default_content = [
        ('We read Knuth so you don’t have to.', 'Tim Peters'),
        ('The purpose of computing is insight, not numbers.', 'R. Hamming')
    ]

    write_file = mock_open()
    with patch('quotation.persistence.open', write_file):
        save_quotes(default_content)

        write_file.assert_called_with('quotation.txt', 'w')
        write_file.assert_has_calls([
         call().write('We read Knuth so you don’t have to.\n'),
         call().write('Tim Peters\n'),
         call().write('The purpose of computing is insight, not numbers.\n'),
         call().write('R. Hamming\n')
        ])
```

The previous scenario was based on quotes already extracted which was recreated
with the setUp method, but it is not necessary to have it for now since it is
needed just for this particular scenario. So let's remove setUp and tearDown methods.
Rerun the test, two errors. Let's focus on the one we already know what is happening,
import and create save_quotes method, run the test, still failing `AssertionError`,
include the implementation for save_quotes method, and finally one more error
to go.

```python
def save_quotes(quote_list, filename='quotation.txt'):
    with open(filename, 'w') as file:
        for quote in quote_list:
            file.write(f'{quote[0]}\n')
            file.write(f'{quote[1]}\n')
```


## What about the get_quote method?

Firstly, let's eliminated `_filename` attribute class, and we have a *FileNotFoundError*
but this is because the extraction was not executed before calling this method. For
this situation, validating that the file does not exist and raising the exception
is good enough to call it a day.

```python
if not os.path.exists(filename):
    raise FileNotFoundError('Extraction should be done before asking for a quote')
```

Happy coding! =D
