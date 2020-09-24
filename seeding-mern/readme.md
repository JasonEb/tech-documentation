# Introduction

Seeding is the process of creating systemic dummy data within a database, typically for testing purposes. A database filled wth data is great for interacting with data models and testing between 100s or 100000s of records. MongoDB is a NoSQL database, and relies on a "collection of documents" model to organize data.

There are primarily two approaches when it comes to related models in MongoDB and Mongoose: `embedded Documents` and `nested reference`. Inserting data with nested references will be the focus of this article. Embedded documents allows related documents to be saved at the same time as parent. However a nested reference is a design approach that allows a child object to not be as coupled to the parent. In the case of a `User` and `Post` object, a Post object with a nested reference allows the Post collection to be more indepedent of the User collection, while preserving it's relationship.

The issue with seeding is respecting the MongoDB behavior when interacting with the database. MongoDB's designs it's [bulk writing API](https://docs.mongodb.com/manual/core/bulk-write-operations/) with one collection in mind, so seeding collections with nested references will require a coordinated approach.

## Main Technologies

- node.js, express, Mongodb, mongoose

### Project Data Models

```
+-----------------+         +-------------+
|User             |         |Post         |
|                 |         |             |
+-----------------+ <------ +-------------+
|username : String|         |user : userId|
|email    : String|         |text : String|
|password : String|         |date : Date  |
+-----------------+         +-------------+
// built with asciiflow
```

`User` has many `Post`s, and within MongoDB utilizing a nested reference. Each collection will be saved independently than the other in MongODB.

## Seed File Development

### Foundation

The `seed.js` file is a plain javascript script, and it will be executed with `node` in the command line. The most important aspect is connecting to the MongoDB database and performing the seeding while the connection is alive.

```javascript
const mongoose = require("mongoose");
let db = 'yourMongAtlasURL'

mongoose
  .connect(db, { useNewUrlParser: true })
  .then((conn) => {

    console.log(`Seed file connected to MongoDB successfully`);

  })
  .catch((err) => console.log(err));
```

This is the basic structure for connecting to MongoDB. It is utilizing a `then` approach rather than sending a callback, and the returned response will be `conn`. The Mongo Atlas URL is sensitive information, so don't expose it in a public repo for bots to collect. This can be achieved with making a private js file with .gitignore.

Seeding a collection will be utilized with the Mongoose Model, and will also be utilizing faker for interesting seed data. Faker provides a library of flavorful dummy data, and it's a great way to interact with the database at scale. 

```javascript
const mongoose = require("mongoose");
let db = require("./keys.js").db;
const faker = require("faker");
const User = require("../models/User");
const { ObjectId } = mongoose.Types;

mongoose
  .connect(db, { useNewUrlParser: true })
  .then((conn) => {
    // WARNING! This code below will delete the database.
    conn.connection.db.dropDatabase(
      console.log(`${conn.connection.db.databaseName} database cleared.`)
    );
    // disable for seed data to persist

    let users = [];

    for (let x = 0; x < 10; x++) {
      newUser = {
        _id: new ObjectId(x),
        username: faker.internet.userName(),
        email: faker.internet.email(),
        password: "abc123",
      };
      users.push(newUser);
    }

    User.insertMany(users).then(() => mongoose.connection.close());
  })
  .catch((err) => console.log(err));
```

The MongoDB connection must remain active while updating the database. A naive implementation would have the final calls be

```javascript
User.insertMany(users);
mongoose.connection.close();
```

But these are asynchronous functions executed consecutively, and thus mongoose will close the connection before the `insertMany` could finish interacting with the database. Considering these are promises, the calls can be chained.

Note the addition of `ObjectId`. Although ids are generated when it's processed to the database, MongoDB will accept explicit id's as well. This is enabled by importing `ObjectId`, which will accept a string and assign a `_id` value. These will be utilized when generating associated `Posts` with a nested reference.

```
<Picture of Mongo Atlas Database>
```

Running the code should create a User collection on MongoAtlas. The ID's will still be obfuscated, but it will still be based on the initial string. Generating the associated `Post` data with a nested reference will be similar, but with one crucial difference.

```javascript
const faker = require("faker");
const User = require("../models/User");
const Post = require("../models/Post");
const db = require("../config/keys").mongoURI;
const mongoose = require("mongoose");
const bcrypt = require("bcryptjs");
const _ = require("lodash");

const { ObjectId } = mongoose.Types;

mongoose
  .connect(db, { useNewUrlParser: true })
  .then((conn) => {
    let users = [];
    let newUser = {};
    let posts = [];
    let newPost = {};
    let randomuser = {};

    console.log(`Seed file connected to MongoDB successfully`);

    // WARNING! This code below will delete the database.
    conn.connection.db.dropDatabase(
      console.log(`${conn.connection.db.databaseName} database cleared.`)
    );
    // disable for seed data to persist

    for (let x = 0; x < 10; x++) {
      newUser = {
        _id: new ObjectId(x),
        username: faker.internet.userName(),
        email: faker.internet.email(),
        password: bcrypt.hashSync("test123", 10),
      };
      users.push(newUser);
    }

    let user_ids = users.map((user) => user._id)
    for (let y = 0; y < 100; y++) {
      randomUser = _.sample(users_ids);
      newPost = new Post({
        _id: new ObjectId(y),
        text: faker.lorem.sentence(),
        user: randomUser,
      });
      posts.push(newPost);
    }

    let promises = [User.insertMany(users), Post.insertMany(posts)];
    Promise.all(promises).then(() => mongoose.connection.close());
  })
  .catch((err) => console.log(err));
```

Lodash's `sample` will pick a random user ID to assign to a Post. Of course, a simpler approach will suffice, but it's an excellent opportunity to test validations. The final couple lines of code introduces `Promise.all`, a method that will execute promises concurrently and wait for resolution. Chaining another `then` will work as well - the responses will return the confirmed data like `users` and `posts`. Ulizing a nested reference along with Promise.all is a flexible approach that's also performant.

## Installation Instructions

- git clone
- setup config
  - keys
  - passport should be included

## MongoDB and Mongoose

## Set up Mongo Atlas

- manage keys
- demonstrate different databases

## Actual Seed File

## Debugging

# References

- app academy
- that one seeding article on dev.to
- [What's New in Mongoose 5: Improved Connectionsn](http://thecodebarbarian.com/whats-new-in-mongoose-5-improved-connections.html)

# Rough Draft

- grammarly

# Final Draft

- grammarly

## Follow-Up Topics

- Auth FLow
- API Routes
