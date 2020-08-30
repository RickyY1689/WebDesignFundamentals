# Uploading and Retrieving Images In a MERN Stack 

So you wanna learn how to upload and retrieve images eh? "How hard could it be?" you ask yourself. So, so hard. This should make things a little easier though! Actually, if I do this right, it should make it a **LOT** easier. 

The issue with finding a good tutorial online was finding something that covered everything. There were a lot of tutorials detailing how to work with image upload and retrieval **but** it was using Firebase. There were a lot of tutorials explaining how to sent HTTP requests to MongoDB in NodeJS **but** they wern't making a react frontend and meaning no CORS and Axios as well. So trying to work image upload and retrieval into a MERN stack project was...difficult...to say the least. 

This is where this little tutorial comes in! I'll go over all the steps we need do in order to set up a fully functioning image upload and retrieval infrastructure within your MERN stack. Also, image upload and retrieval is becoming a mouthful to say so lets just call it **IUR**. 

## The Game Plan

To store our images in MongoDB, we'll be sending them over as binary data. This works as long as our images are smaller than 16 or so megabytes which should be the case for most images we upload. If in future projects you plan on uploading files that exceed this size limitation then you'll want to look into MongoDB's GridFS, but thats another subject entirely. 

To retrieve and display our data we will grab the data previously stored in the MongoDB, convert the binary data back into an image, and display it on our webpage. Let's get started!

## Setting Up Our Mongoose Schema 

I'll assume you already know what mongoose schemas are and won't be covering them in their entirety as for the most part the way we write our schema undergoes little change. After all, remember that the issue is sending and receiving the data. The *method* the data is stored remains the same. Just make sure the variable that's meant to hold our image is set as a buffer datatype. Here's a basic example of a schema storing an image as well as some descriptors for the image. 

``` javascript
var mongoose = require('mongoose'); 
var imageSchema = new mongoose.Schema({ 
    name: String, 
    desc: String, 
    img: 
    { 
        data: Buffer, 
        contentType: String 
    } 
}); 
module.exports = new mongoose.model('Image', imageSchema);
```

## Setting Up Our Backend Server

Setting up our backend is fairly standard. We'll be employing the usual culprits being *express, cors, mongoose, bodyparser and dotenv*. We can add them to our backend by running the following command. The new addition comes in the form of **multer** which helps us deal with a new way of sending data to our database: **multiform data**!. The reason for this new middleware is because we can't send information through the generic JSON bodyparser as JSON is unable to properly handle binary data. 

(A reoccurring theme throughout this tutorial will be the inability of JSONs to carry binary data.)

To get all these packages set up just run the following code.

``` powershell
npm install express cors mongoose bodyparser dotenv multer --save 
```

As an aside, if you've ever wondered why it's good practice to add `--save` it's because this extra argument will also add these packages and version numbers into your dependencies file saving you an extra step when publishing your code. 

Once the packages are installed we can create a new file called `server.js`.  We'll also add two other packages called `fs` and `path` which will work with `multer`.  

``` javascript
var express = require('express'); 
var app = express(); 
var cors = require('cors');
var bodyParser = require('body-parser'); 
var mongoose = require('mongoose'); 
var fs = require('fs'); 
var path = require('path'); 
var multer = require('multer'); 
require('dotenv/config'); 

app.use(cors());
app.use(bodyParser.urlencoded({ extended: false })) 
app.use(bodyParser.json()) 
```

Next, go ahead and create a `.env` file within the backend and throw in your MongoDB Atlas URI. It should look something like this. 

``` 
ATLAS_URI=mongodb+srv://{user}:{password}@cluster0.woa0n.gcp.mongodb.net/{collection name}?retryWrites=true&w=majority
```

Additionally, make sure you've whitelisted your computer's IP within your MongoDB cluster otherwise it won't allow your computer to connect. Once these steps are done, we can establish our connection with MongoDB via the following code. 

``` javascript
// Connecting to the database  
mongoose.connect(process.env.ATLAS_URI, 
    { useNewUrlParser: true, useUnifiedTopology: true }, () => { 
        console.log('MongoDB databse connection established successfully') 
    }); 
```

Next we'll set up the infrastructure for `multer`. Create a folder called "uploads" within backend. This will store some of the data while we upload the photo. 

``` javascript
var storage = multer.diskStorage({ 
    destination: (req, file, cb) => { 
        cb(null, 'uploads') 
    }, 
    filename: (req, file, cb) => { 
        cb(null, file.fieldname + '-' + Date.now()) 
    } 
}); 
var upload = multer({ storage: storage }); 
```

Next, we can link up our schema to our `server.js`.

``` javascript
var imgModel = require({Path to schema file}); 
```

Finally we'll add our GET and POST requests. GET requests remain the same as we're simply retrieving data we've already stored. The POST is where everything we've done with `multer` comes in. In addition to the usual `req` and `res` we'll add a new `upload` argument which will contain our image file. 

``` Javascript
app.post('/', upload.single('image'), (req, res, next) => { 
    var obj = { 
        name: req.body.name, 
        desc: req.body.desc, 
        img: { 
            data: fs.readFileSync(path.join(__dirname + '/uploads/' + req.file.filename)), 
            contentType: 'image/png'
        } 
    } 
    imgModel.create(obj, (err, item) => { 
        if (err) console.log(err)
        else console.log("successful!")
    }); 
}); 
```

 The last step we'll need is to start up our server with a few simple lines of code and we'll have set up our backend!

``` javascript
app.listen('5000' || process.env.PORT, err => { 
    if (err) 
        throw err 
    console.log('Server started') 
}) 
```

Feel free to test out your backend with something like Postman to verify everything works. 

## Performing CRUD Operations With Our Frontend 

Now that our backend is working I won't bother covering how you should go about setting up your frontend as there's tons of ways to set up this scenario. Just be sure to have some button in charge of opening file explorer and allowing the user to select a file to upload. Once all the all the data you wish to upload have been stored in their various state objects we'll work with Axios to send our **multipart form data**. 

First we create a new variable of `FormData` which we will append all the data we want to send into it. Then send the data via Axios as we would with any other Axios POST request. The GET request remains the same as other GET requests. 

``` jsx
var bodyFormData = new FormData();
bodyFormData.append('name', name);
bodyFormData.append('disc', disc);
bodyFormData.append('image', image);

axios({
    method: 'post',
    url: 'http://localhost:5000/',
    data: bodyFormData,
    headers: {'Content-Type': 'multipart/form-data' }
})
    .then(function (response) {
    //handle success
    console.log(response);
})
    .catch(function (response) {
    //handle error
    console.log(response);
});
```

``` JSX
  axios.get('http://localhost:5000/')
    .then(res => {
        console.log(res)
    })
    .catch(err => {
        console.log("Error: " + err)
    });
```

As we mentioned earlier, this process saves your image file as a binary string within MongoDB, you can see for yourself by viewing the data within your collection on your MongoDB cluster! However, when we use Axios to sent our get request the data received is no longer in binary due to JSON's inability to carry binary data. This just means we need to convert the buffer data into a binary string to display as an image. This can be done as follows where `imageData.img.contentType` houses the image extension (png, jpg, etc.) and `imageDaata.img.data` holds the buffer data. 

``` JSX
<img src={`data:image/${imageData.img.contentType};base64,${Buffer.from(imageData.img.data, 'binary').toString('base64')}`} />
```

And thats it! The process is a little lengthy but hopefully my explanations helped make things a little clearer and showed not only **what** we do but more importantly **why** we do it. Best of luck! 

