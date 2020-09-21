# Introduction
Seeding is the process of creating systemic dummy data within a database, typically for testing purposes. This is a practice I first observed with the Ruby On Rails framework with [Faker](https://github.com/faker-ruby/faker), SQL and ActiveRecord, but sought to mimic that with MongoDb and Mongoose. A database filled wth data is great for interacting with data models and testing between 100s or 100000s of records. MongoDB is a NoSQL database, and relies on a "collection of documents" model to organize data. 

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
let db = 'yourMongAtlasURL`

mongoose
  .connect(db, { useNewUrlParser: true })
  .then((conn) => {

    console.log(`Seed file connected to MongoDB successfully`);

  })
  .catch((err) => console.log(err));
```

That's the basic structure for connecting to MongoDB. It is utilizing a `then` approach rather than sending a callback, and the response will be `conn`. The Mongo Atlas URL is sensitive information, so don't expose it in a public repo for bots to collect. This can be achieved with a private js file with .gitignore. 

- mongoose.connect
- create with users
    - creating with faker.js
    - auth note
- 

## Seed File
```javascript
const faker = require("faker");
const User = require("../models/User");
const Tweet = require("../models/Tweet");
const db = require("../config/keys").mongoURI;
const mongoose = require("mongoose");
const bcrypt = require("bcryptjs");
const _ = require("lodash");

const { ObjectId } = mongoose.Types

mongoose
  .connect(db, { useNewUrlParser: true })
  .then((conn) => {
    let users = [];
    let newUser = {};
    let tweets = [];

    console.log(`Seed file connected to MongoDB successfully`);

    // WARNING! This code below will delete the database. 
    // Uncomment to enable resetting the database each time the script is ran.
    conn.connection.db.dropDatabase(
      console.log(`${conn.connection.db.databaseName} database dropped.`)
    );
    
    for (let x = 0; x < 10; x++) {
      newUser = {
        _id: new ObjectId(x),
        username: faker.internet.userName(),
        email: faker.internet.email(),
        password: bcrypt.hashSync("test123", 10),
      };
      users.push(newUser);
    }

    for (let y = 0; y < 100; y++) {
      const newTweet = new Tweet({
        _id: new ObjectId(y),
        text: faker.lorem.sentence(),
        user: _.sample(users.map((user) => user._id)),
      });
      tweets.push(newTweet);
    } 

    let promises = [ User.insertMany(users), Tweet.insertMany(tweets) ]

    Promise.all(promises).then ( () => mongoose.connection.close() )
  })
  .catch((err) => console.log(err));
  ```

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