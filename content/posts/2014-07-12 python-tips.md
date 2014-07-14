Title: Idiomatic Python →
Date: 2014-07-16
Tags: python
Category: code
Slug: idiomatic-python
Author: Zunayed Morsalin
Summary: Notes from Raymond Hettinger talk - Transforming Code into Beautiful, Idiomatic Python

## Table of Contents  
[Looping over a range of numbers](#loop_range_num)   
[Code and Syntax Highlightingg](#code)    

<a name="code"/>
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

## Custom sort order
```python
colors = ['red', 'green', 'blue', 'yellow']

def compare_length(c1, c2):
    if len(c1) < len(c2): return -1
    if len(c1) > len(c2): return 1
    return 0

print sorted(colors, cmp=compare_length)
```

So lets say you have a list of a million items and you're doing a sort the number of comparisons is nlog(n). 

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

