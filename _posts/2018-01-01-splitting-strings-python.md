---
layout: post
title:  Splitting strings in Python
categories: python string
icon: hand-spock-o
---

If you have ever dealt with Python strings, chances are you're already familiar with the `split` method that takes a string and breaks it into a multi-element list.

As an optional argument `split` can take a string indicating where exactly the division should occur. In the example below, this delimiter argument is a comma:

```
>>> 'alpa,beta,gamma,delta'.split(',')
['alpa', 'beta', 'gamma', 'delta']
```

If the delimiter isn't specified, the string is split on whitespace:

```
>>> 'alpha beta gamma delta'.split()
['alpha', 'beta', 'gamma', 'delta']
``` 

This might lead you to believe that splitting with no delimiter is equal to splitting on a space character, but have a look at the following examples:  

```
>>> '    hi there '.split(' ')
['', '', '', '', 'hi', 'there', '']
>>> '    hi there '.split()
['hi', 'there']
```

The operations output totally different lists, but why does this happen?

The thing is that two distinct algorithms are used by the `split` method: one of them is applied to splits with a delimiter and the other one to those without one. To fully understand how splitting works, it's a good idea to look at each type separately.

### Splitting with a delimiter

So, if a delimiter is specified, the string splits each time the delimiter occurs in it. The delimiter can consist of as many characters as you like and it is stripped out from the result. So basically, we use the delimiter to break the string into pieces and then just 'throw it away'.

```
>>> 'rococoesque'.split('o')
['r', 'c', 'c', 'esque']
>>> 'rococoesque'.split('oes')
['rococ', 'que']
```
`split` also takes an optional second argument called _maxsplit_ which specifies the maximum number of splits to be made.

```
>>> 'rococoesque'.split('o', 2)
['r', 'c', 'coesque']
```
In the previous example, the string has 3 o's, but since I set _maxsplit_ to 2, only 2 splits were made.

By far the most interesting thing about this splitting algorithm is that consecutive delimiters are treated as if they were separating empty strings. In other words, if the string happens to have two delimiters side by side, an empty string appears at the corresponding position of the list generated through splitting.

```
>>> 'cooool'.split('o')
['c', '', '', '', 'l']
```

In the previous example, my string has 4 o's in a row, so not only is 'c' separated from 'l', but also a split is made for each pair of consecutive o's and three empty strings are inserted as a result.

Also, if the delimiter happens to open or close the string, an empty string is inserted as the first or last element of the output list, respectively.

```
>>> 'kayak'.split('k')
['', 'aya', '']
```

What has been said is also true for splitting at a whitespace character, (but only if it is explicitely passed as an argument). That is, the algorithm applied by `s.split(' ')` is in no way different from the one applied by, say, `s.split('k')`.

```
>>> ' a b c   d '.split(' ')
['', 'a', 'b', 'c', '', '', 'd', '']
``` 

### Splitting without a delimiter

If the delimiter is not specified or is set to `None`, the string is split on whitespace. The important thing here is that by whitespace we refer not only to a regular space, but also to a bunch of newline and tab characters:

- \n line break
- \t horizontal tab
- \v vertical tab
- \r carriage retun
- \f formfeed

All of those are considered as delimiters by this splitting algorithm.

```
>>> 'a b\nc\td\ve\rf\f'.split()
['a', 'b', 'c', 'd', 'e', 'f']
```

According to Python [docs](https://docs.python.org/3/library/stdtypes.html#str.split), any combination of consecutive whitespace is regarded as a single delimiter. What this means is that `split` is smart enough to understand that for a sequence of 5 whitespace characters, I would generally prefer to get 1 split instead of 5. Also, if the string has leading or trailing whitespace, no empty string is inserted at the start or end.

```
>>> ' a\t \vb    c\n\n \r d\n'.split()
['a', 'b', 'c', 'd']
```

### Using regular expressions to split strings (a couple of examples) 

Python's `split` method is pretty handy, but it may fall short in more complex scenarios. A more powerful `split` method can be found in Python's `re` module which provides regular expression matching operations.

This post is not about regular expressions, but let me show you a couple of things that can be easily achieved with `re`. (And, of course, don't forget to do an `import re` first).

When you do a split, the delimiter string is not included in the output list by default. While normally this is the desired behavior, sometimes you may not want to get rid of the delimiter, and with regular expressions keeping it is as easy as putting parentheses around it.  

```
>>> re.split(r'2', '12321') #no parentheses, the '2' delimiter is stripped out
['1', '3', '1']
>>> re.split(r'(2)', '12321') #he '2' delimiter is inside parentheses and is not stripped out
['1', '2', '3', '2', '1']
```

Another thing you are likely to need is being able to simultaneously use several delimiters. For instance, you might want to split a string at ',' and at ';' at the same time. With a regular expression, it can be achieved by separating the delimiters with an '&#124;' character:

```
>>> re.split(r',|;', 'a,b;c;d,e')
['a', 'b', 'c', 'd', 'e']
```

If you're interested in doing more advanced operations with strings, I highly recommend you to have a closer look at the `re` module. There are many online resources, such as [PyMOTW tutorial](https://pymotw.com/2/re/), to help get you started. 
