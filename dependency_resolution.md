## when i run the the `uv pip install -r requirements.txt`. The package list out inside the requirements.txt are not installs inside the `.venv\lib\site-packages` folder.

This is common uv gotcha, it is not bug.

What happens:
- When you do `uv pip install` the packages does not automatically install into `.venv` unless uv knows which virtual environment to target
- Unlike `pip`, uv does not assuume the currently activated venv

so what happn is:
- `uv` is installing into its own managed environment or cache. Actually it is installing in your global environment
- NOT into `.venv\Lib\site-packages`
- Therefore venv looks empty

> uv ignores actived virtual environments unless you explicitly tell it to use them
>> This is international rule of uv for speed and reproducibility.

correct ways to install pacakages inside `.venv`:
1. Option 1 (recommended): Let `uv` manage the venv
```bash
uv venv .venv
uv pip install -r requirements.txt
```


- `uv venv .venv` will register the virtual environment `.venv` with uv
- `uv pip` now knows exactly where to install


2. Option 2: Explicitly tell uv which python to use
- if `.venv` already exists:
```bash
uv pip install -r requirements.txt --python .venv/Scripts/python.exe
```

This forces to install into `.venv`

3. Option 3: Use uv sync
If you're using `pyproject.toml` :
```bash
uv sync
```
This will:
- create `.venv`automatically
- install dependencies

This command is used when you take a python project from github to your local computer. 

----


What does not work ❌
```bash
.venv\Scripts\activate
uv pip install -r requirements.txt
```
Activation alone is not enough for `uv`. This works for `pip`, not for `uv`

**To verify where does the package actually install**

After activating virtual environment run :
```bash
python -c "import site; print(site.getsitepackages())"
```

Strong recommendation stop using `requirements.txt` with uv package manager. Use the `pyproject.toml + uv sync`
OR you can convert the requirements.txt to pyproject.toml



----


Without `uv venv .venv`. If i run the `uv pip install -r requirements.txt` then packages were install in global python's site-packages. 

Another thing is, To use pip with uv

You need to activate the virtual environment and first add the pip to uv . by `uv add pip`



-----


The `langchain-huggingface` gives an error regarding python dependency. 
The active python environment is **version 3.12.11**


inside the uv's `pyproject.toml` file. The project decalars that 

```bash
requires-python = ">=3.8"
```

uv does the the dependency resolution in such a way so that it resolves ALL Python versions allowed by project. that means it resolves all the python versions above and equal to 3.8 in our case

But the langchain-huggingface requirements are as 


langchain-huggingface latest version(v 1.0.0) required minimum python version ( V.  >=3.10)

Your project allows python version >=3.8 and uv tries to resolve the dependency in such a way so that all the installed packages supports python v3.8 and above. 

Solution
```bash
[project]
requires-python = ">=3.10,<4.0"
```

`uv add langchain-huggingface`

What command line error suggest and you should not do:
1. Command line suggest you to use `--prerelease=allow`
- but avoid this because pre-release version are generally not stable and error-prone.
- try to be on stable version.

`>=3.8` will resolve the dependency as if it will support **Python version 3.8 to 3.12**. But langchain-huggingface **strictly requires** python version 3.10 

2. It suggest use `--frozen`

`uv add langchain-huggingface`

uv will:
1. Read `pyproject.toml`
2. Consider `requires-python`
3. Solve dependency constraint 
4. Pick compatible versions
5. Update `uv.lock`
6. Sync the environment

This is **safe mode**



`uv add langchain langchain-huggingface --frozen`

uv will:
1. uv will skip dependency resolution
2. Ignore python compatibility checks
3. not update `uv.lock`
4. basically, force-install the packages into `.venv`


Why this is created,
That thing is used in Production `uv sync --frozen` **Install what was tested and locked without giving any surprises(unexpected error)**. This is most common use


Another thing is during testing time, This kind of **bypass dependency errors** thing works. 


---
---

**Q. When we do the `uv pip install -r requirements.txt` sso it is obvious i have not run the `uv add pack_name` so it will not getting listed in `pyproject.toml` so , is there any way so that after `uv pip install -r requirements.txt` i can list out packages in pyproject.toml**

Answer:
`uv pip install -r requirements.txt`
Acts like pip. Just install these wheels into environment. It will not update the `pyproject.toml`. In future it may create a dependency error. Do not create a lock file that locks package version.


uv can not auto populate `pyproject.toml`. Like the way requirements.txt is created `pip freeze > requirements.txt`.


Solution is to use `uv add -r requirements.txt`
- read the file
- add package to project.dendencies
- resolve versions 
- create `uv.lock` file

This is correct bridge(in other words, correct way to switch) for pip-style package to uv style package 

Good for migration from `pip`

```bash
uv add -r requirements.txt
uv sync
```



and for production
```bash
uv add -r requirements.txt
uv sync --frozen
```

----
----


**Error**

```bash
(.venv) Medical-ChatBot> uv add -r requirements.txt
error: Requirement name `medical-chatbot` matches project name `medical-chatbot`, but self-dependencies are not permitted without the `--dev` or `--optional` flags. If your project name (`medical-chatbot`) is shadowing that of a third-party dependency, consider renaming the project.
```


The requirement's dependency name(required_package=medical-chatbot) is actually dependends on projectname=medical-chatbot

> "This project depends on itself"

The reason for this problem your `requirements.txt` contains
```text
medical-chatbot
-e .
medical-chatbot @ file://...
```


solution remove those things from requirements.txt


**Q. `-e .` this creates a package automatically and save to site-packages if i remove that then i loss the abillity to create the folder as package . how i do that automation again**

You do NOT lose anything by removing `-e . ` when using uv + pyproject.toml
uv already installs your project as a package automatically.


When you run `pip install -e .` where `-e` standas for **editable mode**

pip did two things:
1. Build your project
2. Create an editable install in `site-packages`
    - via `.pth` or `.egg-link` or symlink
    - pointing like pointer to your source directiory

After installing lots of package using `uv add` you install what packages you need. and when you run `uv sync` it will install the folder as packages.

👉 After you finish uv add ..., you run uv sync, and your project is installed into the .venv in editable mode.


To verify the folder is installed as package or not
```bash
python -c "import medical_chatbot; print(medical_chatbot.__file__)"
```

---


> Recap:

```bash
uv sync   # ← installs deps + your project
```







<br>
<br>
<br>
<br>


---


**Q. question**
```bash
pip install -e .
```

will install the package in site-packages but the folder it create .egg-link folder in current working directory.

That thing is very weird can you explain the flow of it's working in simple way,because what i expect the .egg-file should be created inside site-package, not in my current working directiory


**Answer**

The meaning of `pip install -e .`: Don't copy the code into the `site-packages`. Instead keep using my source code(as i keep updating), and tell python to treat it like installed package in `site-packages`. so that i can be used in import. like`import medical_chatbot` 

Flow for the `pip install -e .`:
1. pip read the project metadata from `pyproject.toml`(modern) or `setup.py`(legecy). 
- pip remembers package_name, version, where source code live(path)
2. pip create a link, do not copy
- `.egg-link` file (legacy)
- `.pth` file (modern)
site-packages` folder

3.  What egg link is actully
The `.egg-link` is created inside the site-packages

To verify
```python
import my_package
print(my_package.__file__)
```

Inside the working directiory the `egg-info` folder is created when the package is listed in site-packages

> `.egg-info` != `.egg-link`

**.egg-info** is **metadata** ,not link. It answers questions like 
- what package name, version, dependencies, file belong to this package. 

It contains files such as: 
- `PKG-INFO` -> name, version, author
- `requires.txt` -> dependencies
- `top_level.txt` -> import name
- `SOURCES.txt` -> file list

This folder `medical_chatbot.egg-info/` is created inside your project directiory when. You run `pip install -e .` or `python setyup.py develop` or `pip install .` 

This is legacy setup tools behaviour. 

The folder `medical_chatbot.egg-info/` will never show `.egg-link`

---


```text
medical_chatbot.egg-info : metadata
medical_chatbot.pth : link
medical_chatbot-0.1.0.dist-info/ : actual package
```

`medical_chatbot.egg-info/` is used for what ?
> It is package's identity card + dependency record. It tells python and pip what this package is, what it needs, and how it was installed. 

**Why `.egg-info` exist at all**

When you install a package (normal or editable), tools like pip,setuptools, and importlib(Used to import python module inside variable) need metadata to:
- know the package name & version
- know dependencies
- know what to uninstall
- know what file belongs to this package 

> `uv` avoids creating `.egg-info` not because it avoids `pip / setuptools / importlib`, but because it avoids the legacy setuptools editable workflow.

It is not like uv doesn't use pip/setuptools/importlib. uv uses pip-compatible build backeds and importlib but it force modern standards(PEP - Python Exposed Proposals) and skip legecy behaviour.

uv internationally
- Disable legacy `setup.py` flows
- Enforces PEP 517 for build isolation 

(Here, 517 is just like Rule no. 517, and **PEP** is the rulebook(constitution) of python for standards(rules) and good practices)

- Enforces PEP 600 for editable installs 

The tool
- pip easily follows rules
- uv strictly follows rules



----

The final suggestion is remove `-e .` 

Take free chatbot template from the internet and integrate to the `app.py`
- then we create `static/` folder.  inside that we put `*.css` files

and create **app.py**

----


# Sample questions
- what is acromegly and gigantism
- what is acne
- what is treatment of acne