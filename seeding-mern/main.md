# Introduction
# Technologies Covered
# Main
- hook
-

Seeding is the process of creating systemic dummy data within a database. This is a practice I first observed with the Ruby On Rails framework with [Faker](https://github.com/faker-ruby/faker), SQL and ActiveRecord, but sought to mimic that with MongoDb and Mongoose.


A database filled wth data is great for interacting with your data models and testing between 100s or 100000s of records. Let's establish data models:

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

