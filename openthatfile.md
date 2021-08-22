# Open *that* file
## How to be explicit with filenames while still being implicit

---

Maybe you've been here before; you are coding up a small script that needs to open a file, read the contents, and take some actions.  You put your program (`reader.py`) in the same directory as the file (`source.txt`) and hit `Run` in your editor of choice...

```py
with open("source.txt", "r"): as infile:
    file_contexts = infile.read()

print(infile)
```

```py
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
FileNotFoundError: [Errno 2] No such file or directory: 'source.txt'
```

What?

Now we're sure the `reader.py` and the `source.txt` are together in a folder named `project`.  It looks like this:

```
C
└── Users
    └── preocts
        └── Documents
            └── project
                ├── reader.py
                └── source.txt
```

So, `reader.py` should totally be able to see `source.txt` and open it. Right? Well, if we add a few lines of code we see the cause of our issue.

```py
import pathlib

print(pathlib.Path().cwd())
print(pathlib.Path(__file__))

with open("source.txt", "r"): as infile:
    file_contexts = infile.read()

print(infile)
```

```py
C:\Users\preocts
C:\Users\preocts\Documents\project\reader.py

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
FileNotFoundError: [Errno 2] No such file or directory: 'source.txt'
```

Same error but check out the difference in directory paths here. Now, `pathlib.Path().cwd()` is printing our current working directory. This is where the Python interpreter was run. the `pathlib.Path(__file__)` is printing the full location of our `reader.py` file.  Python isn't "working" where our file is located.

This is a common issue for those programmers that use the built-in terminals of their editors to run the code.  The editor runs the code but has executed the it from a different working directory.  When python runs the `open` function, the interpreter isn't using the location of the module.  Instead, the interpreter uses the directory it was launched from.

That's worth saying again:

---
### The Python interpreter bases all relative file locations on the working directory, not the location of the `.py` file being run.
---

We know what is wrong. Now we just need a solution.

One such solution would be to use an absolute path to open our file. Using `C:\Users\preocts\Documents\project\reader.py` would get the job done, but it's not very portable. The code wouldn't work on anyone else's computer or if we moved the files.

Another solution would be to configure the editor to use the `.\project` directory as the working directory. This can be a good habit for development but if you shared the project, the next dev might have the same issue. Again, portability is lost.

Instead, we can leverage that special `__file__` dunder value and open `source.txt` *relative to the location of* `reader.py`. Meaning that so long as we know where `source.txt` is in relation to `reader.py` we will be able to find and open it. To do this, we'll use `pathlib`.

```py
filepath = pathlib.Path(__file__).parent / "source.txt"
```

This is using some of the magic of `pathlib`. The `parent` gives us everything of the path except the last piece (`C:\Users\preocts\Documents\project`). The `/` is used by `pathlib` to let us quickly create `Path` objects. Together we get `C:\Users\preocts\Documents\project\source.txt` which is the absolute path of our target file.

The neat thing is that if we move `reader.py` the code will still work so long as `source.txt` is with it.

Our end code looks like this:

```py
import pathlib

filepath = pathlib.Path(__file__).parent / "source.txt"

with open(filepath, "r"): as infile:
    file_contexts = infile.read()

print(infile)
```

With an expected output of our file without needing to worry about where the Python interpreter is running (working directory).

```
Eggs are amazing.
```
