In the previous sections of the course we used MongoDB for storing data, which is a so called NoSQL database. NoSQL databases became very common just over 10 years ago, when the scaling of the internet started to produce problems for relational databases that utilized the older generation SQL query language.

Relational databases have since then experienced a new beginning. Problems with scalability have been partially resolved and they have also adopted some of the features of NoSQL databases. In this section we explore different NodeJS applications that use relational databases, we will focus on using the database PostgreSQL which is the number one in the open source world.

# Table of Content

# Part 13

## Part 13a - Using relational databases with Sequelize

In this section we will explore node applications that use relational databases. During the section we will build a Node-backend using a relational database for a familiar note application from sections 3-5. To complete this part, you will need a reasonable knowledge of relational databases and SQL. There are many online courses on SQL databases, eg. [SQLbolt](https://sqlbolt.com/) and [Intro to SQL by Khan Academy](https://www.khanacademy.org/computing/computer-programming/sql).

There are 24 exercises in this part, and you need to complete each exercise for completing the course. Exercises are submitted via the [submissions system](https://studies.cs.helsinki.fi/stats/courses/fs-psql) just like in the previous parts, but unlike parts 0 to 7, the submission goes to a different "course instance".

### Advantages and disadvantages of document databases

We have used the MongoDB database in all the previous sections of the course. Mongo is a [document database](https://en.wikipedia.org/wiki/Document-oriented_database) and one of its most characteristic features is that it is *schemaless*, i.e. the database has only a very limited awareness of what kind of data is stored in its collections. The schema of the database exists only in the program code, which interprets the data in a specific way, e.g. by identifying that some of the fields are references to objects in another collection.

In the example application of parts 3 and 4, the database stores notes and users.

A collection of *notes* that stores notes looks like the following:

```json
[
  {
    "_id": "600c0e410d10256466898a6c",
    "content": "HTML is easy"
    "date": 2021-01-23T11:53:37.292+00:00,
    "important": false
    "__v": 0
  },
  {
    "_id": "600c0edde86c7264ace9bb78",
    "content": "CSS is hard"
    "date": 2021-01-23T11:56:13.912+00:00,
    "important": true
    "__v": 0
  },
]
```

Users saved in the *users* collection looks like the following:

```json
[
  {
    "_id": "600c0e410d10256466883a6a",
    "username": "mluukkai",
    "name": "Matti Luukkainen",
    "passwordHash" : "$2b$10$Df1yYJRiQuu3Sr4tUrk.SerVz1JKtBHlBOARfY0PBn/Uo7qr8Ocou",
    "__v": 9,
    notes: [
      "600c0edde86c7264ace9bb78",
      "600c0e410d10256466898a6c"
    ]
  },
]
```

MongoDB does know the types of the fields of the stored entities, but it has no information about which collection of entities the user record ids are referring to. MongoDB also does not care what fields the entities stored in the collections have. Therefore MongoDB leaves it entirely up to the programmer to ensure that the correct information is being stored in the database.

There are both advantages and disadvantages to not having a schema. One of the advantages is the flexibility that schema agnosticism brings: since the schema does not need to be defined at the database level, application development may be faster in certain cases, and easier, with less effort needed in defining and modifying the schema in any case. Problems with not having a schema are related to error-proneness: everything is left up to the programmer. The database itself has no way of checking whether the data in it is *honest*, i.e. whether all mandatory fields have values, whether the reference type fields refer to existing entities of the right type in general, etc.

The relational databases that are the focus of this section, on the other hand, lean heavily on the existence of a schema, and the advantages and disadvantages of schema databases are almost the opposite compared of the non-schema databases.

The reason why the the previous sections of the course used MongoDB is precisely because of its schema-less nature, which has made it easier to use the database for someone with little knowledge of relational databases. For most of the use cases of this course, I personally would have chosen to use a relational database.

### Application database

For our application we need a relational database. There are many options, but we will be using the currently most popular Open Source solution PostgreSQL. You can install [Postgres](https://www.postgresql.org/) (as the database is often called) on your machine, if you wish to do so.

However, we will be taking advantage of the fact that it is possible to create a Postgres database for the application on the Fly.io and Heroku cloud service platforms, which are familiar from the parts 3 and 4.

In the theory material of this section, we will be building a Postgres-enabled version from the backend of the notes-storage application, which was built in sections 3 and 4.

Since we don't need any database in the cloud in this part (we only use the application locally), there is a possibility to use the lessons of the course [part 12](https://fullstackopen.com/en/part12) and use Postgres locally with Docker. After the Postgres instructions for cloud services, we also give a short instruction on how to easily get Postgres up and running with Docker.

#### Fly.io

Let us create a new Fly.io-app by running the command `fly launch` in a directory where we shall add the code of the app. Let us also create the Postgres database for the app:

