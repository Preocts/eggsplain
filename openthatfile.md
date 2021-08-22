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

We import `pathlib` which is a great standard library for working with filepaths. The first print is showing us what is called the "current working directory".  This is where the Python interpreter was run.

The second print is telling us where the `reader.py` is. `__file__` is a special value set by Python which holds the location of the file.

Notice our paths here do not match! Python isn't "working" where our file is located.

This is a common issue for anyone that uses the built-in terminal of their editors to run the code.  The editor runs the code but executes (or calls) the interpreter from an unexpected working directory.  When python then runs the `open` function, the interpreter isn't looking where we expect.  Instead of looking at where the `.py` is, the interpreter uses the directory it was launched from.

That's worth saying again:

---
### The Python interpreter bases all relative file locations on the working directory, not the location of the `.py` file being run.
---

So that's what is wrong with our program. Now we just need a solution.

A fast and easy solution would be to use an absolute path to open our file. Using `C:\Users\preocts\Documents\project\reader.py` would get the job done, but it's not very portable. The code wouldn't work on anyone else's computer or if we moved the files.

Another solution would be to configure the editor to use the `.\project` directory as the working directory. This can be a good habit for development but if you shared the project, the next dev might have the same issue. Again, portability is lost.

Instead, we can leverage that special `__file__` value and open `source.txt` *relative to the location of* `reader.py`. Meaning that so long as we know where `source.txt` is in relation to `reader.py` we will be able to find and open it. To do this, we'll, again, use `pathlib`.

```py
filepath = pathlib.Path(__file__).parent / "source.txt"
```

This looks complex if you aren't familiar with `pathlib` but it is actually very straight-forward.

- `pathlib.Path(__file__)` gives us the full path to our file: `C:\Users\preocts\Documents\project\reader.py`
- `.parent` removes the right-most piece of the path, leaving us with just a location: `C:\Users\preocts\Documents\project`
- `/ "source.txt` is how we add to a `Path` object.
- Together we get `C:\Users\preocts\Documents\project\source.txt` which is the absolute path of our target file.

The neat thing is that if we move `reader.py` the code will still work so long as we move `source.txt` with it.

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
