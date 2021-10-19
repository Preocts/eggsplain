# Why use virtual environments?

There are a ton of reasons you'll like be told on why to use virtual environments. This article will cover why **I** use them and how that could relate to your interests.

## What is a virtual environment?

Okay, so what **is** a virtual environment, or "venv" as we shorten it too? It is a self-contained directory that usually gets created in your project's root directory. In it is a complete set of files that python needs to run. These are often referred to as your "site-packages".

When you create a venv, the files are matched to the version of python used when creating the venv.  When you activate your virtual environment you are running a small script that temporarily replaces the system's path of these files with the venv's path.

The end result is that you are still running the Python installed on your system, but you are in an isolated environment. Nothing you install with pip in this environment will affect your system's default installation. In fact, no extra packages will be installed when you create a new venv. Within this environment you develop your code starting from a clean slate.

Back to the reasons of *why* to use a virtual environment...

## Isolate system dependent libraries

Windows users, feel free to read on to the rest. Anyone working in a linux environment or macOS environment, please consider the following.  Your systems will likely come with some version of Python already installed.  With luck, by the time you are reading this article Python 2.x has been laid to rest and is no more.  But that just means your systems now have Python 3.x dependencies *build into your operating system*.

Why is this a concern? These *could* be very critical sub-processes that allow your operating system to work. If you take an action such as upgrading a python package where the new version breaks backward compatibility or remove a critical package, your operating system might not be able to recover itself. You could just be cleaning up your `pip list` and unintentionally break something downstream.

## Controlled library versions

Version control, and specifically pinning a version, helps the development team ensure they remain in control of changes to how their software works. Without a virtual environment in place, work on one project that uses a common library such as `urllib3` will be impacted anytime any other project changes the version of that library. With a venv in place, the versions of imported libraries can be *whatever the project requires* without concern of impacting other projects.

It isn't just updates that can break libraries either. Sometimes you'll install a needed third-party library and find that it pins an old version of a common library. That old version, if installed at the system's level, could cause any number of odd behaviors in other projects. Experience has taught me that such things are more common than I would have thought and frustratingly vague to bug-hunt out.

## Clean slates

It was mentioned in the "what" but also ties into the why; using a venv gives the developer a clean slate to start with. Combine this clean slate with a `requirements.txt` file that is updated as new dependences are committed to a project and you have a fast way to reset the development environment. It is just a few commands to delete a virtual environment, recreate a new one, and use pip to install all the confirmed requirements.

So after that ninth hour of trying the sixteenth library to get the results you need there isn't a need to remember everything you installed. Just add the library you stuck with, wipe and rebuild your venv, and install your required libraries. Now you can continue to build and test with the peace of mind that the environment is clean.

## Works on my machine

Having a clean environment that is easy to set up and tear down yields another benefit to the developer. It becomes just as simple for other developers in a team or shared project to set up *the same environment*. This little benefit leads right into my favorite:

## Smaller requirements list

All of the above benefits combined gives you, the developer, the power to keep your requirements list efficiently small. Even if your method is to `pip freeze > requirements.txt`, the output is going to be cleaner and smaller than a single system level library pool.

---

*There is nothing wrong with using the pip freeze dump method either. I've just seen some 100+ line long requirement files for otherwise tiny projects and it may have left me scarred.*

---

Those are really all the selling points I needed to commit myself to using a separate venv for all my projects. I still have my playground project directory which shares a venv for the dozens of micro-ideas in it, but it still got a venv. Hopefully something in the list connected with you. Virtual environments really are, in my opinion, a best practice for Python development.

---

### Bonus: So, how do I setup a virtual environment?

There are, as we do in the development world, many different tools for creating virtual environments. I have found success with `venv` which usually comes packaged with a Python 3.x install. The steps to use it are simple:

Create and activate `venv`:
```bash
# Linux/MacOS
python3 -m venv venv
. venv/bin/activate

# Windows
py -m venv venv
# or, if py launcher isn't installed
python -m venv venv

venv\Scripts\activate.bat
```

*You can name the `venv` directory anything you like by changing the command. For example: `py -m venv virtenv` will create a `venv` in the directory `virtenv/`. This only affects the path to activate the `venv`.*

Your command prompt should now have a `(venv)` prefix on it.

Install requirements (optional, if available):
```bash
# Linux/MacOS
pip install -r requirements.txt

# Windows
py -m pip install -r requirements.txt
# or, if py launcher isn't installed
python -m pip install -r requirements.txt
```

To exit the `venv`:
```bash
deactivate
```

To delete the `venv` just remove the `venv` directory.

---

```
Eggs are amazing.
```

Preocts, 2021-18-10
