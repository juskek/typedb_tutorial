# Type DB Tutorial

This repository was created as a learning guide for me to pick up TypeDB as a beginner in databases and graphs. More complex examples can be [here](https://github.com/vaticle/typedb-examples).

- [Type DB Tutorial](#type-db-tutorial)
  - [1.1. Intro](#11-intro)
  - [1.2. TypeDB Building Blocks](#12-typedb-building-blocks)
    - [1.2.1. Schema](#121-schema)
    - [1.2.2. Types](#122-types)
    - [1.2.3. Rules](#123-rules)
  - [1.3. Getting Started](#13-getting-started)
    - [1.3.1. Creating a database and linking it to TypeDB Studio](#131-creating-a-database-and-linking-it-to-typedb-studio)
  - [1.4. Adding Data](#14-adding-data)
    - [1.4.1. Defining a Schema](#141-defining-a-schema)
    - [1.4.2. Inserting Data](#142-inserting-data)
    - [1.4.3. Loading Schema and Data](#143-loading-schema-and-data)
    - [1.4.4. Reading Data](#144-reading-data)


## 1.1. Intro

There are three components in the TypeDB ecosystem: 
* TypeDB: 
  
  The main component of the ecosystem, which is a strongly typed graph database with TypeQL as its querying language.

* TypeDB Cluster 
  
  Allows you to deploy TypeDB as a cluster for high availability and scalability, but operates on the same principle as TypeDB. 

* TypeDB Studio
  
  Provides UI to interact with a TypeDB database.


In this tutorial, we will be focusing on TypeDB and TypeDB Studio, and learning how to build a knowledge graph with them.

## 1.2. TypeDB Building Blocks

### 1.2.1. Schema

The schema is a representation of the domain, i.e., the logical grouping of data and the acceptable values.

The schema is built using **types** and **rules**.

### 1.2.2. Types

Types are a category of things having common characteristics.

There are three main types:

1. `entity`
   
   Represents objects in the domain, e.g. person, company.

   Can be extended, e.g. `person sub entity`.

2. `relation`
   
   Represents connections in the domain, e.g. employment.
   
   Can be extended, e.g. `employment sub relation`.

   Has intrinsic role type to define how types are related, e.g.
   ```
   define
       employment sub relation, relates employee, relates employer; 
       person sub entity, plays employment:employee;
       company sub entity, plays employment:employer;
   ```

   Apart from connecting entities, relations can also connect attributes and other relations.

   Relations can also be nested, e.g. a relation being a role player in another relation.


3. `attribute`
   
   Represents values in the domain, e.g. name.

   Can be extended, e.g. `name sub attribute`.

   Can be owned by entities, relations or other attributes.

   Attribute instances are:
   * Globally unique by type and value
   * Immutable


### 1.2.3. Rules

Rules allow you to define logic statements which can bring more insight. The logic statements are constructed in a when-then manner, e.g. when 2 persons are employed by the same company, then mutual employment.

## 1.3. Getting Started

1. [Install TypeDB](https://docs.vaticle.com/docs/running-typedb/install-and-run#system-requirements)
2. [Install TypeDB Studio](https://github.com/vaticle/typedb-studio/tree/master)
   
   Make sure to check [version compatibility between TypeDB and TypeDB Studio](https://docs.vaticle.com/docs/studio/overview#version-compatibility)
3. Run TypeDB server from terminal: `typedb server`, and take note of the port no the server is running on, e.g. `listening to address: 0.0.0.0:1729`
4. Open TypeDB Studio application
5. Under 'Types', click 'Connect to TypeDB' and enter the address, e.g. `localhost:1729`

### 1.3.1. Creating a database and linking it to TypeDB Studio

1. Click the database icon in the top toolbar, and create a database named `knowledge_graph_tutorial`
2. In the dropdown menu next to the database icon, select the newly created database.
3. In the project sidebar, select this repo as the project root directory.

## 1.4. Adding Data 

There are two main steps needed to add data to the database: defining a schema, and loading the data.

### 1.4.1. Defining a Schema

The first thing to do when defining a schema, is to look at the data we want to store. For example, we can start with some arbitrary facts about people.

```
John is a male.
John likes fried chicken.
John likes Jo.

Jo is a female.
Jo likes fried chicken.
Jo likes John.
```

To define the schema, we need to organise instances of the data into entities, attributes and relationships, e.g.

- Entities:
  - Person, e.g. John, Jo
  - Food, e.g. Fried Chicken
- Attributes:
  - Gender, e.g. male, female
- Relationships
  - liking, e.g. John (liker) likes fried chicken (likee)


The schema can then be defined:

```
define
    person sub entity,
        owns name,
        owns gender;
    
    ...

    name sub attribute,
        value string;
```

You can find the full schema definition queries in `schema.tql`.

### 1.4.2. Inserting Data
With the schema defined, we can start inserting data. We can start by inserting entites, which starts with the `insert` keyword, followed by a variable declaration that is to be inserted, `$foo` and the corresponding attributes for that entity. E.g.,

```
insert $p isa person, has name "Jeremy" , has gender "male";
```

You can find a full list of these queries in `insert_data.tql`

### 1.4.3. Loading Schema and Data
Now that we have the schema definition and insert queries, we can load them into TypeDB.

To load the schema, we need to open a write transaction. This can be done via the terminal, but we will use TypeDB studio for this. 

1. Press 'schema' and 'write' on the top toolbar
2. Open `schema.tql`
3. Press run, then commit.

You should see the schema defined in the Types sidebar on the left. With the schema loaded, we can now load the data.

1. Press 'data' and 'write' on the top toolbar.
2. Open `insert_data.tql`
3. Press run, then commit.

### 1.4.4. Reading Data

Set the transaction mode to 'data' and 'read' on the top toolbar, then open `read_data.tql`

Uncomment the relevant sections and press run to show the following information: 

* Show all entities, attributes and relationships
* Show people who like fried chicken
* Show people who like other people




