Title: Debugging with python
Date: 2014-09-14
Tags: python
Category: code, python, debugging
Slug: debug-python
Author: Zunayed Morsalin
Summary: A quick look at some of my debugging techniques

Once I was tired of scattering print statements trying to debug some code I began researching better tools to help me. Here's what I came up with 

### Pdb


The built in [python debugger](https://docs.python.org/2/library/pdb.html) was a good place to start. It allows you to set breakpoints but most importantly set_trace()

```python
test.py
x = 'foo'
y = 'bar'

z = x + y 
import pdb; pdb.set_trace()


```

Whats great about set_trace is it will drop you into a REPL session and you can experiment and look at the current state. You can even step line by line trough the code. Type `h` to get a list of commands and functions available to you. Typing n will execute the current line and set the cursor to the next line.

```bash
> python test.py
> ~/test.py(5)<module>()->None
-> import pdb; pdb.set_trace()
(Pdb) x
'foo'
(Pdb) y
'bar'
(Pdb) 
'foobar'
(Pdb) locals()
{'__builtins__': <module '__builtin__' (built-in)>, '__file__': 'test.py',
 '__package__': None, '__return__': None, 'pdb': <module 'pdb' from 
 '/System/Library/Frameworks/Python.framework/Versions/2.7/lib/
 python2.7/pdb.pyc'>, 'x': 'foo', 'y': 'bar', '__name__': '__main__', 
 'z': 'foobar', '__doc__': None}

```
### Ipdb

Great now we have access to this pretty neat tool to drop into our code. Lets never use it again because we can use the cleverly named [ipdb](https://github.com/gotcha/ipdb) package. Ipdb allows you to leverage the excellent ipython REPL when debugging which gives you tab completion and syntax highlighting. I sometimes use it to rapidly prototype and test new functions and methods. 

<img class="align-center" width="960" src="/images/debug_terminal.gif"  title="ipdb repl session" />


### Pycharm

Ipdb/pdb has served me very well over the last year but one thing that is still annoying about it is you still have to type the variable name or locals() to see values. You also have to retype them as you step through your code. It would be awesome if we could just watch the variables in a window as you execute. Thats where Pycharm's debugger helps. 

<img class="align-center" height="750" src="/images/debug-pycharm.gif"  title="pycharm debugging session" />

If you don't have/want pycharms there are some free alternatives I found like [winpdb](http://winpdb.org/about/) and [PUDB](https://pypi.python.org/pypi/pudb) that look promising. Happy debugging. 