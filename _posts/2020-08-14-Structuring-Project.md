---
title: Structuring The Project
published: true
---

_What now that I've fulfilled my purpose, I do not know what to do?_ **Stop there for a moment.**
At this time, we solve half of the problem now it comes the chance that we should think about 
how to structure our project because as it is it looks crowded, repetitive and chaotic. We are doing 
too many things in the same place, it would be easier to follow making pieces of code as a representation 
of single instruction that flows in a list of specific things to do. Think about it, as it is easier 
to read from a bullet point list instead of a paragraph.

Here it is when [modules](https://docs.python.org/3/tutorial/modules.html) and packages come to the rescue.
But before that lets identify the three distintics main activities:
1. Store
2. Extract
3. Do something, because we should do something with it.

In addition, notice those fixed values, like database name and url. It would be better to leave that kind of 
information in a configuration file.

As you can see it is not just about the code itself, it is about what other files we should include into the 
project folder, for that moment that we would need a little reminder about how to use our solution. At this point, 
when we are kind of in the mood of we almost know what need to be done, it is a really good idea to start getting 
organise. For how to structure a project we are going to use 
[The Hitchhiker's Guide to Python](https://docs.python-guide.org/writing/structure/) as a reference.

Lets begin with the following simple structure, then we move or add things in case
is necessary:
- LICENSE
- README.md
- quoting/MyHTMLParser.py
- quoting/config.py
- setup.py
- tests.py

Wow! Our future self would be very grateful since adding functionalities will 
be a lot easier and not just for ourselves, if more people would like to join to our little but simple ~~plan to conquer the world~~
project. Easy! Easy! Don't fall for the heat of the moment, or do you? =0

Happy ~~falling~~ coding!  <:o)
