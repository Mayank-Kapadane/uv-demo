
If only one person create video on that topic then it may be sponsored video. but if more than one person create video on that topic then it means it is really a new technology that i has to learn.


# --by Krish naik



It is an extremely fast Python package and project manager written in Rust.

This is the official documentation of uv that shows how much less time it takes to download any package
https://docs.astral.sh/uv/

it is faster than `pip install` and reason behind it is written in Rust

what are the benefit of using it that are also listed on offical site of uv as **Highlights** section


creating virutal environment using anaconda, project configuration that you really need to do

install it using pip
```shell
pip install uv
```

by typing the command `uv` you can see what are the commands you can execute such as 
- uv intialize project    
- uv create project
- remove project 
- update project 


Here we take and example of creating MCP server using UV


```shell
uv init uvdemo
```
 
> `uvdemo` is the project name. It will give some set of files 

The main thing is that for one entire project python version is pertocular
if you want to go ahead and see it then open the `.python-version` file
it follows file system as linux. in linux the file name starts with dot(.) means it is hidden file

The project.toml file contains basic information about the package. 
There you can see 


<hr/>

### What is toml ?
it is used for configuration. It has easy to read and write syntax

it is generally used tin Rust project for configurations
it store the configuration data in structured way. key-value pairs, table, array
TOML is often chosen over JSON or YAML when readability and minimalism are desired.


> Why use TOML ?
- Simple syntax (compared to YAML)
- Strict typing rules (unlike JSON)
- Human readable 
<hr/>


> The Question aries how can we install specific python version or python libraries ?

on github's readme you will see all the command in Readme.md of repository
https://github.com/astral-sh/uv

uv allows you to quickly switching between different python versions

to install a perticular python version
```shell
uv venv --python 3.12.0
```

What we do is,
create a virtual environment using uv `uv venv` by running that much command you can create virtual environment. it is very fast.
It is optional to write the python version

to active venv it provide command and just copy and paste to active the venv. 
select it and copy by ctrl+shift+c and paste by ctrl+shift+v or right click.




To install some dependency(package)
```shell
uv add numpy
```

if you have requirements.txt then the command to install packages from it
```shell
uv add -r requirements.txt
```

in order to any python file using the same virtual environment
```shell
uv run main.py
```


The `uv sync` command is used to synchronize the project's dependencies based on the configuration files such as pyproject.toml or requirements.txt.
```shell
uv sync
```

The `uv lock` generate or update a lock file for your project. This lock file is crucial for ensuring that the exact versions of all dependencies and their transitive dependencies are recorded.
---

Another ways to install uv is by powershell command provided by official site of uv
```powershell
powershell -ExecutionPolicy Bypass -c "irm https://astral.sh/uv/install.ps1 | iex"
```
In plain terms: It downloads and runs the official installation script for uv

It is faster than installed uv using `pip install uv`

# --by Corey Schafer

There are different ways to install
in mac the easiest way to install is by using brew.
mac users also called the brew as homebrew


on linux we use apt, windows we either use winget or choco, and on mac we use brew
but curl and wget is something that works on every platform

in old way we create a virtual environment
activate it and install packages inside it and work with it

in new way we use uv and we just need to run `uv init`


uv offers different projects to create
- `uv init myapp --app` : it is used by default. it create a simple project structure for application, server, things like that
- `uv init myapp --lib` : it is used to create a project which is designed to build as python package

> uv automatically intialize the project as git repository for us.

The `uv.lock` file is exact version of all the packages installed including sub-dependencies or we can say trasistive dependency.


uv has one more feature to visualizing an understanding the dependencies 
```shell
uv tree
```

It is not need to create the virtual environment manually when we first time install any package or dependency the venv is automatically being created

check which python interpreatrer our program is using

```python
import sys
print(sys.executable)
```

in vs code select the venv from the status bar then it will easy to run it using code runner's play button

To run a python script using command line no neeed to active the virtual env .venv just fire the command `uv run main.py`



if we delete the .venv folder
All the information to run our project is also stored in pyproject.toml
just fire the command `uv run main.py`
it will detect vev doesn't exist. it will create venv install dependencies and run the code. and show the output

Not only that is extremely faster than pip.



if someone want to set the environment without runnin the application then we can send the pyproject.toml file and they can simply use `uv sync`



to uninstall a package  `uv remove flask` that will update the pyproject and lock file.
fast and easy specially for beginners intead of running 5-10 command and understanding virtual environment instead they can simply focus on code. instead of installing and play with packages. save time and efforts. 

uv has smart caching system. if we have 10 different project with same flask version then uv will store that flask in cache which saves a lot time. evironments are completely isolated.

with pip venv one environment may have packages related to different projects 

you can use `uv pip` commands if you want to use pip
```powershell
uv pip install numpy
uv pip list
```

uv has so many functionality that pip can not never do. creating a pyproject.yml


already using python project want to make as uv project simply fire `uv init`
then we have to add packaages from requirements.txt file
`uv add -r requirements.txt`


uv can replace pipx
for installing python based command line tools
pipx basically allows you to install python command line tool. that you can use anywhere in your systema not just in a specific project so, linters, formatters things like that
likely you just need to install once and you can use it in multiple different projectsas opposed to installing them in every project individually

let's say we want to install Ruff a popular python linter which also made by astral.
`uv tool install ruff`

it will download ruff in isolated env. but make avaible in your path.
let's confirm that we can access it from anywhere 

`which ruff`
run `ruff check`

remove a tool `uv tool remove ruff`



you can use tools without installing them locally on our system that is one of most important thing about uv. 
`uv tool run ruff check ` that works

alternative short hand command is 
`uvx ruff check`


Running tools this way is common thing with uv.

`uv tool list` list out tools. 
update tools all at once you can update them individually `uv tool upgrade --all`



uv also has to build optimized docker containers


# --by Hitesh Choudhary
in python generally we do 2 things 
1. Project
2. script

when we work with project it is good to go with uv project by `uv init` or `uv init myapp` 

we can actually create a distrubution project that will be easy to share or deploy. `uv build`


when it comes to script(file) you can run a script without creating uv venv 
actully uv creates  venv but that is somewhere else not in our working directiory
you can see that by 
```python
import sys
print(sys.executable)
```

> it uses default interpreter of system


to run a python script which is not part of any venv project created by uv
but it uses some external libraries 
```powershell
uv run --with 'flask'  main.py
uv run --with 'flask' --with 'requests' --with 'pandas'  main.py

```



Another way is 
```powershell
uv add --script main.py 'flask' 'requests' 'pandas' 

# it will add documentation like thing at the top of the file, after that each time to run a script you just need to fore
uv run main.py 

# instead of 
uv run --with 'flask' --with 'requests' --with 'pandas'  main.py
```



Learn and Know what and how to do more

**Freeapi**
 is product created by hitesh chaudhary for that offers so many free Apis.




 ---





The difference between installing uv using python and powershell is,
you can install it using python also
you can install it using powershell also if you don't have python or you want to download the uv directly to system. not connected to the python











----

uv doesn't contain pip by default. 
if you want to install package using `pip install pandas` this way 
then acter activating .venv you can not do.

even if you run that command. Your system(windows) what may do is, it is not able to find pip.exe in your active virtual environment. so it try to see it in global python which is present in environment PATH variable. it's pip is globally accessible so, access that and via  command `pip install pandas` it will install pandas in globally setted python's site-packages

soltion: make sure .venv is active. Then `uv add pip`
make sure it is downloaded by checking in pyproject.toml's **dependency** list. 



## Download all packages from `requirements.txt`
uv pip install -r requirements.txt
