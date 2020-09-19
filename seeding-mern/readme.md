# Introduction
Seeding is the process of creating systemic dummy data within a database. This is a practice I first observed with the Ruby On Rails framework with [Faker](https://github.com/faker-ruby/faker), SQL and ActiveRecord, but sought to mimic that with MongoDb and Mongoose. A database filled wth data is great for interacting with your data models and testing between 100s or 100000s of records. MongoDB is a NoSQL database, and relies on a collection of documents to organize data. 

There are primarily two approaches when it comes to related models in MongoDB and Mongoose: `embedded Documents` and `nested reference`. With the advent of cloud services, developers can now 

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

`User` has many `Post`s, and within MongoDB, this is achieved with Mongoose's `populate`. 

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

# Rough Draft
- grammarly
# Final Draft
- grammarly

## Follow-Up Topics
- Auth FLow
- API Routes