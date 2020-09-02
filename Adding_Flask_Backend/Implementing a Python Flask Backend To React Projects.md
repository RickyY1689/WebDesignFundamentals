# **Implementing a Python Flask Backend To React Projects**

*Based on the [video tutorial](https://www.youtube.com/watch?v=sOVxkPvAHjI) by NonStop and Ben Awad on Youtube*

# Initial Setup

## Create a Basic React App 

We can create the default react app via the following command:

``` javascript
npx create-react-app {name of project}
```

## Set up Flask Backend 

To set up the python flask backend, first create a new folder within the react project called `api`. In it, we will create a virtual environment which will allow us to specify certain dependencies, python versions, and so on into it allowing us to program to things we want to program. If you don't have `virtualenv` installed just run pip install for it. The process should look a little something like this:

``` powershell
mkdir api 
cd api 
virturalenv -p python3 venv 
```

The last line of code sets up our virtual environment and specified the python version (python 3) as well as the name of our virtual environment (venv). Now that we've created our virtual environment we're going to need to activate it. We can do this with a lil razzle dazzle and more importantly this line of code for Linux:

``` powershell
source './venv/bin/activate'
```

In Windows run the following line: 

``` powershell
venv\Scripts\activate
```

To verify you've properly activated the virtual environment, your terminal should be displaying a little `(venv)` on the left side of the command line or whatever you happened to name your virtual environment. It should look identical to when you activate virtual environments via anaconda if that's a helpful comparison to draw. 

Actually, this whole thing is basically akin to what you do in anaconda, just setting up a virtual environment and then installing the things you need inside it. 

## Adding Dependencies to Our Virtual Environment

Now that we've got our virtual environment activated it's time to populate it with some packages we'll need to build our project. While each project will require their own packages one package everyone flask backend is unsurprisingly **flask**. 

Again, similar to conda we can run `pip list` to view our currently installed packages and install packages via `pip install`. Go ahead and install `flask` and verify it's installation with `pip list`. While you're at it, you'll also want to install `dotenv` which we'll use to store all of our secret keys and nuclear launch codes. 

``` powershell
pip install flask 
pip install python-dotenv
pip list
```

Once you've verified everything has been properly installed, create a new python app via the `touch` command on Linux. On Windows you can just go ahead and create a new file in your IDE or something of that nature.   

``` powershell
touch api.py
```

# Creating Our Flask Backend

Great! You've set up the groundworks of our backend, now it's time to take everything we've downloaded and put them to work. For any flask backend we'll first need to initialize flask within our API. We can do so with the following: 

``` python
from flask import Flask
app = Flask(__name__)
```

Next we can get to the good stuff, actually setting our routes to handle requests from our frontend and writing accompanying functions to run when said routes are triggered. This is where we begin actually building our flask backend. **Welcome to Flask**. 

## Writing Routes 

Routes have two arguments: the URL extension, and the method. By default the method will be a GET request and the URL extension can be made to whatever you'd like. Here's an example!

``` python
@app.route('/api', methods=['GET'])
def api():
    return {
        ...
    }
```

## Establishing Our dotenv File 

We can also create our `.env` file at this stage. In here we'll declare which files will be associated with certain variables such as our flask app itself. To begin, we can add the following: 

``` 
FLASK_APP = api.py
FLASK_ENV = development
```

**Note:** Starting our app in the development environment will allow it to refresh everything it detects a new change within the program (think `nodemon` for `node.js`) which is quite helpful. 

**Note:** If you'd prefer a centralized `.env` file within the surface level directory of your project just set `FLASK_APP = {foldername containing flask backend}`, for me this value would be `api`. If you go with this approach it is imperative you **set you're flask initialization file to be `__init__.py`** .

## Running Our Flask Backend

At this time we're ready to start up our flask backend, **Amazing**! To run it all we have to do is write `flask run` in the command line and you should receive a few responses which will should indicate the server has started running. 

# Adding Complexity to Our Flask Backend

For most backend structures we'll probably want something with multiple different CRUD request handlers as well as different types of data our backend can take in. Since this is usually the case, we can't throw everything into our single python file. So we'll need some new files to help tidy things up. This will be reminiscent of `mongoose` and it's utilization of schema models and routes. Here we refer to them as **models** and **views**. Take this section of the tutorial as a theoretical overview rather than a tutorial. We'll focus on making sense of the SQL database and the code in the next section. The important part here is understanding how everything is connected. 

## Setting Up A View File 

Your views file will contain all the routes you'd like to handle. This will be where all the CRUD requests are defined and their functions written. Here's an example. 

``` python
from flask import Blueprint, jsonify
main = Blueprint('main', __name__)

@main.route('/add_movie', methods=['POST'])
def add_movie():
    return 'Done', 201 

# Recall if methods is unspecified it takes the form of a GET request
@main.route('/movies')
def movies():
    movies = [] 
    return jsonify({'movies': movies})
```

## Setting Up A Models File 

Models define the framework of the various data structures you'll be planning to store into your database. Similar to mongoose schemas you'll be defining the model name, and defining the contents it holds. Here's a simple example of this. 

 ``` python
from . import db 

class Movie(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(50))
    rating = db.Column(db.Integer)
 ```

## Combining Views, Models, and Your Main Flask File 

``` python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()

def create_app():
    app = Flask(__name__)
    app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///database.db'
    db.init_app(app)
    from .views import main 
    app.register_blueprint(main)
    return app
```

The first thing you'll do is add your views file to your main flask file (`api.js` or `__init__.js`, etc), wherever you're initializing your flask app. You can disregard the SQL stuff here if you'd like but I'll touch more on that later as an example project. 

You should also find something quite strange. The fact that the views import is placed in the middle of all the code. The reason for this is our `views.py` file relies on the initialization of our flask app. Thus placing it at the beginning would cause it to fail due to the sequential nature of programming. 

[NEED TO BE CONTINUED - EXPLAIN MODEL CONNECTION]

# Working With SQL 

In this exercise I'll go through the process of setting up an SQL environment using SQLite3 and have an operational backend database. 

## Setup 

First off, you'll need to `pip install flask-sqlalchemy` into your virtual environment. Next you'll need to configure the settings in your main flask initialization file (`api.js`, `__init__.js`, etc). 

``` python
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()

def create_app():
    app = Flask(__name__)
    app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///database.db'
    db.init_app(app)
    ...

    return app
```

## Adding Tables Into Our Database

It's important that the database `db` is initialized outside of the function so that it can be imported on its own in other python files. Speaking of, now we'll be importing our `db` into our models file which will help create our first table within our database. 

 ``` python
from . import db 

class Movie(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(50))
    rating = db.Column(db.Integer)
 ```

**Note:** The `.` in the import is just a single dot as my flask initialization file is the "dunder init" file (`__init__.js`) which is referred to as a dot. If you'd named it something else you'd have to write `from .{file name}`, trust me, its just more convenient as sensible to table the file `__init__.js`. 

## Starting Our Database 

First, for this process to work, `cd` into the directory containing your `api` folder (or just modify my code to fit the relative location of your terminal). If that sounds confusing type in `ls` into your terminal to see if it contains your `api` folder. If it does you're solid. 

Now, you'll want to run python from your PowerShell.

``` powershell
python
>>> from api.models import Movie
>>> db.create_all(app=create_app())
>>> sqlite3 api/database.db 
```

 There are two ways to verify you've set this up correctly. The quick method is to check your directory within your `api` folder, it should now contain a new `database.db` file. The second more foolproof method is launching `sqlite3` in your terminal and checking the database manually. 

``` powershell
sqlite3 api/database.db  
sqlite> .tables
movie {<-- THIS SHOULD BE THE DESIRED RESPONSE}
sqlite> select * from movie; {THIS SHOULD RETURN NOTHING AS OUR MOVIE TABLE IS CURRENTLY EMPTY}
sqlite>
```

 **Note** that everything surrounded with a `{}` are meant to be comments. 

If everything has reported back positive then our database is ready to go and we can connect our view with our model!

## Connecting the View and the Model 



