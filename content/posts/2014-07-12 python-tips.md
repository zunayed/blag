Title: Idiomatic Python →
Date: 2014-07-18
Tags: python
Category: code
Slug: idiomatic-python
Author: Zunayed Morsalin
Summary: Notes from Raymond Hettinger talk - Transforming Code into Beautiful, Idiomatic Python


I recently watched Raymond Hettinger talk - Transforming Code into Beautiful, Idiomatic Python(https://www.youtube.com/watch?v=OSGv2VnC0go)  and found out a bunch of helpful tips. I highly recommend everyone to check it out. If you don’t have an hour to kill you can check out my notes for a quick read and pasteable code 

## Table of Contents  
[Looping over a range of numbers](#loop1)   
[Looping over a collection](#loop2)    
[Looping backwards](#loop3)    
[Looping over a collection and indices's](#loop4)    
[Looping over two collection](#loop5)    
[Custom sort order](#customsort)    
[Counting with dictionaries](#count1)    
[Grouping with dictionaries](#group)    
[Unpacking sequences](#unpack)    
[Updating multiple state variables](#state)  
[Concatenating strings](#deque)    
[Open and close files](#file)    
[List comprehension and generator expressions](#listgen)    

----------------------------------
<a name="loop1"/>
## Looping over a range of numbers

```python
for i in [0, 1, 2, 3, 4, 5]:
    print i**2

for i in range(6):
    print i**2

```
Given a large range(1,000,000) it will consume 32mb of memory on a x64 system

```python
for i in xrange(6):
    print i**2

```
xrange creates an iterator over the range producing the value one at a time
In python 3 range is replaced with xrange
----------------------------------
<a name="loop2"/>
## Looping over a collection

```python
colors = ['red', 'green', 'blue', 'yellow']

## Don't do this
for i in range(len(color)):
    print colors[i]

## Do this
for color in colors:
    print color
```
----------------------------------
<a name="loop3"/>
## Looping backwards

```python
colors = ['red', 'green', 'blue', 'yellow']

## Don't do this
for i in range(len(color)-1, -1, -1):
    print colors[i]

## Do this - Also its faster
for color in reversed(colors):
    print color
```
----------------------------------
<a name="loop4"/>
## Looping over a collection and indices's

```python
colors = ['red', 'green', 'blue', 'yellow']

## Don't do this
for i in range(len(color)):
    print i, '-->', colors[i]

## Do this to save you from tracking the indices's 
for i, color in enumerate(colors):
    print i, '-->', colors[i]
```
----------------------------------
<a name="loop5"/>
## Looping over two collection 

```python
names = ['raymond', 'rachel', 'matthew']
colors = ['red', 'green', 'blue', 'yellow']

## Don't do this
n = min(len(names), len(colors))
for i in range(n):
    print names[i], '-->', colors[i]

## Do this
for name, color in zip(names, colors):
    print name, '-->', color
```

Zip is great but it creates a third list in memory consisting of tuples with pointers back to the original. So we use izip:

```python
for name, color in izip(names, colors):
    print name, '-->', color
```
----------------------------------
<a name="customsort"/>
## Custom sort order
```python
colors = ['red', 'green', 'blue', 'yellow']

def compare_length(c1, c2):
    if len(c1) < len(c2): return -1
    if len(c1) > len(c2): return 1
    return 0

print sorted(colors, cmp=compare_length)

## Do this instead
print sorted(colors, key=len)
```
----------------------------------

<a name="count1"/>
## Counting with dictionaries

```python
colors = ['red', 'green', 'red', 'blue', 'green', 'red']

## Default way
d = {}
for color in colors:
    if color not in d:
        d[color] = 0
    d[color] += 1

## A better way using get
for color in colors:
    d[color] = d.get(color, 0) + 1


## a even better way
d = defaultdict(int)
for color in colors:
    d[color] += 1

```
----------------------------------
<a name="group"/>
## Grouping with dictionaries

```python
names = ['raymond', 'rachel', 'matthew', 'roger', 
         'betty', 'melissa', 'judith', 'charlie']

## standard way
d = {}
for name in names:
    key = len(name)
    if key not in d:
        d[key] = []
    d[key].append(name)

## a better way
d = {}
for name in names:
    key = len(names)
    d.setdefault(key, []).append(name)

## better way
d = defaultdict(list)
for names in names:
    key = len(names)
    d[key].append(name)
```
----------------------------------
<a name="link"/>
## Linking dictionary
```python
defaults = {'color':'red','user':'guest'}
os_args = os.environ
command_line_args = {'color':'blue'}

## normally you take your defaults and replace it with 
## os.environments and then again replace it if you have 
## command line args

d = defaults.copy()
d.update(os_args)
d.update(command_line_args)

## The problem with this is its making a ton of dictionaries 
## We can simplify it with
d = ChainMap(command_line_args, os.environ, defaults)

```
----------------------------------
<a name="unpack"/>
## Unpacking sequences

```python
p = 'Rymound', 'Hettinger', 0x30, 'python@example.com'

## instead of this
fname = p[0]
lname = p[1]
age = p[2]
email = p[3]

## do this instead
fname, lname, age, email = p

```
----------------------------------
<a name="state"/>
## Updating multiple state variables 

```python
def fibonacci(n):
    x = 0
    y = 1
    for i in range(n):
        print x
        t = y
        y = x + y
        x = t

## Better way - This uses tuple packing to allow 
## for simultaneous variable updates
def fibonacci(n):
    x, y = 0, 1
    for i in range(n):
        print x
        x, y = y, x+y
```

----------------------------------
<a name="strings"/>
## Concatenating strings

```python
names = ['raymond', 'rachel', 'matthew', 'roger', 
         'betty', 'melissa', 'judith', 'charlie']

## don't do this since its is quadratic behavior and creates multiple copies of the string
s = names[0]
for name in names[1:]:
    s += ', ' + name
print s

## use join instead
print ', '.join(names)
```

----------------------------------
<a name="deque"/>
## Updating sequences with deque

```python
names = ['raymond', 'rachel', 'matthew', 'roger', 
         'betty', 'melissa', 'judith', 'charlie']

## using the wrong data structure for these task
del names[0]
names.pop(0)
names.insert(0, 'mark')

## the correct data structure to use is deque
names = deque(['raymond', 'rachel', 'matthew', 'roger', 
         'betty', 'melissa', 'judith', 'charlie'])

del name[0]
names.popleft()
names.appendleft('mark')
```

Deque (a doubly-linked list of block nodes) are thread-safe, the contents can even be consumed from both ends at the same time from separate thread

From stackoverflow - 
> Python lists are much better for random-access and fixed-length operations, including slicing, while deques are much more useful for pushing and popping things off the ends, with indexing (but not slicing, interestingly) being possible but slower than with lists"

Quote break

----------------------------------
<a name="file"/>
## Open and close files

```python
## don't do this
f = open('data.txt')
try:
    data = f.read()
finally:
    f.close()

## use this instead
with open('data.txt') as f:
    data = f.read()
```

----------------------------------
<a name="listgen"/>
## List comprehension and generator expressions

```python
## hell naw
results = []
for i in range(10):
    s = i ** 2
    results.append(s)
print sum(results)

## hell yes
print sum([i ** 2 for i in xrange(10)])

print sum(i ** 2 for i in xrange(10))
```

