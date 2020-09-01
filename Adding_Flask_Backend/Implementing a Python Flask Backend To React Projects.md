# **Implementing a Python Flask Backend To React Projects**

*Based on the [video tutorial](https://www.youtube.com/watch?v=sOVxkPvAHjI) by NonStop and Ben Awad on Youtube*

# Initial Setup

## Create a Basic React App 

We can create the default react app via the following command:

``` javascript
npx create-react-app {name of project}
```

## Set up Flask Backend 

To set up the python flask backend, first create a new folder within the react project called `api`. In it, we will create a virtual environment which will allow us to specify certain dependencies, python versions, and so on into it allowing us to program to things we want to program. The process should look a little something like this:

``` powershell
mkdir api 
cd api 
virturalenv -p python3 venv 
```

The last line of code sets up our virtual environment and specified the python version (python 3) as well as the name of our virtual environment (venv). Now that we've created our virtual environment we're going to need to activate it. We can do this with a lil razzle dazzle and more importantly this line of code:

``` powershell
source './venv/bin/activate'
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

Once you've verified everything has been properly installed, create a new python app via the `touch` command. 

``` powershell
touch api.py
```

# Creating Our Flask Backend

Great! You've set up the groundworks of our backend, now it's time to take everything we've downloaded and put them to work. For any flask backend we'll first need to initialize flask within our API. We can do so with the following: 

``` python
import flask from Flask 
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

## Running Our Flask Backend

At this time we're ready to start up our flask backend, **Amazing**! To run it all we have to do is write `flask run` in the command line and you should receive a few responses which will should indicate the server has started running. 



