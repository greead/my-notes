[<< React](React) ‧ [MongoDB >>](MongoDB)

## Intro
```js
const app = express();

...

const port = 8080;
app.listen(port, () => {
    console.log(`Server running on port ${port}`);
});

```

```js
import express from 'express';
import cors from "cors";
import mongoose from 'mongoose';
import path from 'path';
import url from 'url';
```
## Routing

**reqs**
app.get()  
 Get a resource  
app.post()  
 Creation of new entities  
securely  
app.put()  
 Complete replacements or  
partial updates  
app.patch()  
 Partial updates  
app.delete()  
 Removal of existing entities  
app.head()  
 Identical to GET requests but  
without the body  
app.options()  
 Identify allowed methods  
 Like –h help flag for HTTP  
app.all(route,  
middleware)  
 Use a middleware for any  
method on a route

req.params: URL parameters  
req.query: URL query string parameters  
req.route: current route as a string  
req.cookies: cookies  
req.signedCookies: signed cookies  
 Both require cookie-parser  
req.body: body/payload  
req.headers: HTTP request headers  
13  
http://expressjs.com/en/4x/api.html#req

**res**
res.get(header) and  
res.set(header, value): Set or get  
HTTP header values  
res.type(type): Specific setter for  
HTTP “Content-Type” header  
res.status(code): Sets the HTTP  
response status  
- `res.cookie(name, value)`: Creates  
or replaces a cookie  
- `res.clearCookie(name)`: Deletes a  
cookie
http://expressjs.com/en/4x/api.html#res

res.send(data): Send response  
res.json(data): Send response as JSON  
data  
res.sendfile(path): Send a file  
res.download(path): Send a file (browser-  
prompted)  
res.redirect(path): Redirect to a different  
route  
http://expressjs.com/en/4x/api.html#res

- middleware
```js
app.use(cors());
app.use(express.json());

//server static React build files
app.use(express.static(path.join(__dirname, buildFolder)));
```

- routes

The following examples illustrate defining simple routes.

Respond with `Hello World!` on the homepage:

```javascript
app.get('/', (req, res) => {
  res.send('Hello World!')
})
```

Respond to POST request on the root route (`/`), the application’s home page:

```javascript
app.post('/', (req, res) => {
  res.send('Got a POST request')
})
```

Respond to a PUT request to the `/user` route:

```javascript
app.put('/user', (req, res) => {
  res.send('Got a PUT request at /user')
})
```

Respond to a DELETE request to the `/user` route:

```javascript
app.delete('/user', (req, res) => {
  res.send('Got a DELETE request at /user')
})
```

For more details about routing, see the [routing guide](https://expressjs.com/en/guide/routing.html).


```js
//upload route
app.post("/animals/upload", async (req, res) => {
    try {
        const newAnimal = new Animal({ name: req.body.name, pictureURL: req.body.pictureURL });
        await newAnimal.save();
        const message = `Animal ${req.body.name} uploaded!`
        console.log(message);
        res.send(message);
    }
    catch (err) {
        console.log(err.message);
        res.send(err.message);
    }
})

//search route
app.get("/animals/search", async (req, res) => {
    console.log(req.query.name);
    const animal = await Animal.findOne({ name: req.query.name });
    if (animal) {
        res.send(animal.pictureURL);
    }
    else {
        res.status(404).send("Animal not found.");
    }
})

//clear route
app.delete("/animals/clear", async (req, res) => {
    await Animal.deleteMany({});
    res.send("All animals deleted.")
})
```
## Mongoose
```jsx
import mongoose from 'mongoose';
```

- Schemas
```jsx
const animalSchema = new mongoose.Schema({
    name: { type: String, required: true, unique: true },
    pictureURL: { type: String, required: true }
});
```

The permitted SchemaTypes are:

- [String](https://mongoosejs.com/docs/schematypes.html#strings)
- [Number](https://mongoosejs.com/docs/schematypes.html#numbers)
- [Date](https://mongoosejs.com/docs/schematypes.html#dates)
- [Buffer](https://mongoosejs.com/docs/schematypes.html#buffers)
- [Boolean](https://mongoosejs.com/docs/schematypes.html#booleans)
- [Mixed](https://mongoosejs.com/docs/schematypes.html#mixed)
- [ObjectId](https://mongoosejs.com/docs/schematypes.html#objectids)
- [Array](https://mongoosejs.com/docs/schematypes.html#arrays)
- [Decimal128](https://mongoosejs.com/docs/api/mongoose.html#mongoose_Mongoose-Decimal128)
- [Map](https://mongoosejs.com/docs/schematypes.html#maps)
- [UUID](https://mongoosejs.com/docs/schematypes.html#uuid)
- [Double](https://mongoosejs.com/docs/schematypes.html#double)
- [Int32](https://mongoosejs.com/docs/schematypes.html#int32)

- Models
```jsx
const Animal = mongoose.model("Animal", animalSchema);
```

Model.create(data, [callback (error, doc)]): creates a new  
Mongoose document and saves it to the database  
 Model.remove(query, [callback(error)]): removes documents  
from the collection that match the query; when finished, calls  
callback with error  
 Model.find(query, [fields], [options], [callback(error, docs)]):  
finds documents that match the query (as a JSON object);  
possible to select fields and use options  
 Model.update(query, update, [options], [callback(error,  
affectedCount, raw)]): updates documents, similar to native  
update  
 Model.populate(docs, options, [callback(error, doc)]): populates  
documents using references to other collections; an alternative  
to another approach described in the next section
Model.findOne(query, [fields], [options], [callback(error, doc)]): finds  
the first document that matches the query  
 Model.findById(id, [fields], [options], [callback(error, doc)]): finds the  
first element for which _id equals the id argument (cast based on the  
schema)  
 Model.findOneAndUpdate([query], [update], [options],  
[callback(error, doc)]): finds the first document that matches the query  
(if present) and updates it, returning the document; uses findAndModify  
 Model.findOneAndRemove(query, [options], [callback(error, doc)]):  
finds the first document that matches the query and removes it when  
returning the document  
 Model.findByIdAndUpdate(id, [update], [options], [callback(error,  
doc)]): similar to findOneAndUpdate using only the ID  
 Model.findByIdAndRemove(id, [options], [callback(error, doc)]):  
similar to findOneAndRemove using only the ID

**docs**
doc.model(name): returns another Mongoose model  
 doc.remove([callback(error, doc)]): removes this document  
 doc.save([callback(error, doc, affectedCount)]): saves this  
document  
 doc.update(doc, [options], [callback(error, affectedCount,  
raw)]): updates the document with doc properties, and  
options parameters, and then upon completion fires a  
callback with error, number of affectedCount and the  
database output  
 doc.toJSON([option]): converts a Mongoose document to  
JSON (options are listed later)  
 doc.toObject([option]): converts a Mongoose document to a  
plain JavaScript object (options are listed later)
doc.isModified([path]): true/false, respectively, if some parts  
(or the specific path) of the document are or are not modified  
 doc.markModified(path): marks a path manually as modified  
which is useful for mixed (Schema.Types.Mixed) data types  
because they don't trigger the modified flag automatically  
 doc.isNew: true/false, respectively, whether the document is  
new or not new  
 doc.id: returns the document ID  
 doc.set(path, value, [type], [options]): sets value at a path  
 doc.validate(callback(error)): checks validation manually  
(triggered automatically before save())  
Mongoose Instance (Document)  
Methods

- Connect
```js
const dbURL = "mongodb+srv://final-exam-practice:final-exam-practice@pfw-cs.ctovaum.mongodb.net/final-exam-practice?retryWrites=true&w=majority";

await mongoose.connect(dbURL);

console.log("Connected to database!");
```

- CRUD

An instance of a model is called a [document](https://mongoosejs.com/docs/documents.html). Creating them and saving to the database is easy.

```js
const yourSchema = new mongoose.Schema({ name: String, size: String });
const Tank = mongoose.model('Tank', yourSchema);

const small = new Tank({ size: 'small' });
await small.save();

// or

await Tank.create({ size: 'small' });

// or, for inserting large batches of documents
await Tank.insertMany([{ size: 'small' }]);
```

```js
await Tank.find({ size: 'small' })
	.where('createdDate')
	.gt(oneYearAgo)
	.exec();
```

```js
await Tank.deleteOne({ size: 'large' });
```

```js
// Updated at most one doc, `res.nModified` contains the number
// of docs that MongoDB updated
await Tank.updateOne({ size: 'large' }, { name: 'T-90' });
```

Mongoose [models](https://mongoosejs.com/docs/models.html) provide several static helper functions for [CRUD operations](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete). Each of these functions returns a [mongoose `Query` object](https://mongoosejs.com/docs/api/query.html#Query).

- [`Model.deleteMany()`](https://mongoosejs.com/docs/api.html#model_Model-deleteMany)
- [`Model.deleteOne()`](https://mongoosejs.com/docs/api.html#model_Model-deleteOne)
- [`Model.find()`](https://mongoosejs.com/docs/api.html#model_Model-find)
- [`Model.findById()`](https://mongoosejs.com/docs/api.html#model_Model-findById)
- [`Model.findByIdAndDelete()`](https://mongoosejs.com/docs/api.html#model_Model-findByIdAndDelete)
- [`Model.findByIdAndRemove()`](https://mongoosejs.com/docs/api.html#model_Model-findByIdAndRemove)
- [`Model.findByIdAndUpdate()`](https://mongoosejs.com/docs/api.html#model_Model-findByIdAndUpdate)
- [`Model.findOne()`](https://mongoosejs.com/docs/api.html#model_Model-findOne)
- [`Model.findOneAndDelete()`](https://mongoosejs.com/docs/api.html#model_Model-findOneAndDelete)
- [`Model.findOneAndReplace()`](https://mongoosejs.com/docs/api.html#model_Model-findOneAndReplace)
- [`Model.findOneAndUpdate()`](https://mongoosejs.com/docs/api.html#model_Model-findOneAndUpdate)
- [`Model.replaceOne()`](https://mongoosejs.com/docs/api.html#model_Model-replaceOne)
- [`Model.updateMany()`](https://mongoosejs.com/docs/api.html#model_Model-updateMany)
- [`Model.updateOne()`](https://mongoosejs.com/docs/api.html#model_Model-updateOne)

## Static
To serve static files such as images, CSS files, and JavaScript files, use the `express.static` built-in middleware function in Express.

The function signature is:

```javascript
express.static(root, [options])
```

The `root` argument specifies the root directory from which to serve static assets. For more information on the `options` argument, see [express.static](https://expressjs.com/en/4x/api.html#express.static).

For example, use the following code to serve images, CSS files, and JavaScript files in a directory named `public`:

```javascript
app.use(express.static('public'))
```

Now, you can load the files that are in the `public` directory:

```plain-text
http://localhost:3000/images/kitten.jpg
http://localhost:3000/css/style.css
http://localhost:3000/js/app.js
http://localhost:3000/images/bg.png
http://localhost:3000/hello.html
```

build files
```js
//server static React build files
const __dirname = path.dirname(url.fileURLToPath(import.meta.url));

const buildFolder = "../dist";

...

app.use(express.static(path.join(__dirname, buildFolder)));
```
## Requests
- Parsing
- ![[Pasted image 20241216060208.png]]

- URL Params
**Route parameters**

Route parameters are named URL segments that are used to capture the values specified at their position in the URL. The captured values are populated in the `req.params` object, with the name of the route parameter specified in the path as their respective keys.

```
Route path: /users/:userId/books/:bookId
Request URL: http://localhost:3000/users/34/books/8989
req.params: { "userId": "34", "bookId": "8989" }
```

To define routes with route parameters, simply specify the route parameters in the path of the route as shown below.

```javascript
app.get('/users/:userId/books/:bookId', (req, res) => {
  res.send(req.params) // parameter obj
})

// { userID, bookId } = req.params
```

## express.Router

Use the `express.Router` class to create modular, mountable route handlers. A `Router` instance is a complete middleware and routing system; for this reason, it is often referred to as a “mini-app”.

The following example creates a router as a module, loads a middleware function in it, defines some routes, and mounts the router module on a path in the main app.

Create a router file named `birds.js` in the app directory, with the following content:

```javascript
const express = require('express')
const router = express.Router()

// middleware that is specific to this router
const timeLog = (req, res, next) => {
  console.log('Time: ', Date.now())
  next()
}
router.use(timeLog)

// define the home page route
router.get('/', (req, res) => {
  res.send('Birds home page')
})
// define the about route
router.get('/about', (req, res) => {
  res.send('About birds')
})

module.exports = router
```

Then, load the router module in the app:

```javascript
const birds = require('./birds')

// ...

app.use('/birds', birds)
```

The app will now be able to handle requests to `/birds` and `/birds/about`, as well as call the `timeLog` middleware function that is specific to the route.

But if the parent route `/birds` has path parameters, it will not be accessible by default from the sub-routes. To make it accessible, you will need to pass the `mergeParams` option to the Router constructor [reference](https://expressjs.com/en/4x/api.html#app.use).

```javascript
const router = express.Router({ mergeParams: true })
```