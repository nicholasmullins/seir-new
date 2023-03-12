<img src="https://i.imgur.com/cD5R8OG.png" width="600px;display:inline-block;margin:auto">

# Mongoose - Referencing Related Data

## Learning Objectives

| Students Will Be Able To: |
|---|
| CRUD Data External to the Application |
| Explain the Difference Between 1:M & M:M Relationships |
| Use Referencing to Implement 1:M & M:M Data Relationships |
| "Populate" Referenced Documents |

## Road Map

1. Setup
2. Review the Starter Code
3. Create a CRUD Helper Module
4. Compute the _Average Rating_ of the Reviews
5. The `mongoose-movies` Data Model
6. Referencing _Performers_ in the _Movie_ Model
7. Associating Movies and Performers
8. Essential Questions

## 1. Setup

1. Move into our existing `mongoose-movies` project:
    ```
    cd ~/code/mongoose-movies
    ```
2. Sync your code with the starter code for this lesson (mandatory):

    ```
    git reset --hard origin/sync-11-referencing-starter
    ```
3. Open the project's folder in VS Code:
    ```
    code .
    ```
4. Open an integrated terminal session:
    ```
    control + backtick
    ```
5. Start the Express server:
    ```
    nodemon
    ```
6. Browse to `localhost:3000`

## 2. Review the Starter Code

The starter code has a few updates from the _Mongoose - Embedding Related Data_ lesson's final code:
	
- As you will learn in this lesson, a many-to-many relationship between two data resources such as _movies_ and _performers_, requires that those resources already exist.  Therefore, the functionality to create _performers_ has been implemented to save time. However, rest assured that there is nothing in this code that has not been previously taught - let's checkout the model, router, controller & view.

  > 👀 Practice implementing the code for Performers was an optional exercise previously assigned.

- Be sure to check out the date "fix" required in the `create` action.

- Also, check out how the performers `newPerformer` controller function to see how we can sort documents.

- The cast-related code that treated `cast` as an array of strings has been removed from templates.

### 👉 You Do - Create a Few Performers <small>(1 min)</small>

Feel free to use these from Star Wars and Caddyshack:

```
Mark Hamill  9/25/1951
Carrie Fisher  10/21/1956
Harrison Ford  7/13/1942
Chevy Chase  10/8/1943
Bill Murray  9/21/1950
```

## 3. Create a CRUD Helper Module

Currently, the `cast` property on the `Movie` model holds an array of strings representing the names of the performers.

During this lesson, we will be refactoring the array to contain/reference the performer documents' `ObjectId` instead.

An error will occur if the schema specifies a type of `ObjectId` and Mongoose receives a string instead, so let's clear out any strings that might be in the movie documents.

### CRUD Data Externally to the Application

At times you might need to CRUD data "outside" of the application.

Well, that time is now because we need to "reset" all of the movie documents' `cast` property to an empty array.

To do this, we're going to create a **crud-helper.js** module and load it within a Node REPL...

### Create **crud-helper.js**

Although **crud-helper.js** will not run as part of the app, it needs to be able to connect to the database and access the models.

Creating it in the project's root folder works well:

```
touch crud-helper.js
```

Copy/paste the following code with comments:

```js
// crud-helper.js

// Used to perform CRUD external to the application

// To use (don't type the $'s):
//   1. Open a Node REPL in Terminal:
//         $ node

//   2. Load this crud-helper.js module
//         $ .load crud-helper.js

//   3. When done CRUDing, exit the REPL with:
//         $ .exit (or ctrl-D, or ctrl-C twice)

// Note that if any changes are made to the
// models or this module, exit 


// If the db connection string is in a .env file,
// we need to process it just like in server.js
require('dotenv').config();
// Connect to the database
require('./config/database');

// Require the app's Mongoose models
const Movie = require('./models/movie');
const Performer = require('./models/performer');

// Define Variables to Hold Documents
let movie, movies;
let performer, performers;


// Example CRUD

// console.log all movie documents
// Preview of promise syntax - coming SOON!
Movie.find({}).then(console.log);

console.log('Time to CRUD!');

```

Cool, let's follow the instructions included in **crud-helper.js** to load it.

Now we can run the Mongoose code that will update the `cast` property of all movie documents to an empty array:

```js
Movie.updateMany(
  {},  // Query object determines which docs to update
  {cast: []},  // Update object has properties to update
  function(err, result) {console.log(result)}
);
```

Nice, now we will be able to refactor the movieSchema later without causing any errors.

## 4. Compute the _Average Rating_ of the Reviews

The demo of the completed mongoose-movies app computed and displayed an _average rating_ for reviews in the movie's detail page:

<img src="https://i.imgur.com/CoFuOOW.png">
	
All it takes is adding some EJS to **views/movies/show.ejs** :

```html
<tbody>
  <!-- Yep, we can define variables! -->
  <% let total = 0 %>
  <% movie.reviews.forEach(function(r) { %>
    <!-- Accumulate the total rating -->
    <% total += r.rating %>
    <tr>
      <td><%= r.createdAt.toLocaleDateString() %></td>
      <td><%= r.content %></td>
      <td><%= r.rating %></td>
    </tr>
  <% }); %>
  <!-- Add a row to display the avg AFTER the forEach iteration  -->
  <tr>
    <td colspan="2"></td>
    <td><strong><%= (total / movie.reviews.length).toFixed(1) %></strong></td>
  </tr>
</tbody>
```

Although we just used the amazing power of EJS, typically it's the controller's responsibility to gather/compute data and pass it to views to be rendered.

<details>
<summary>
👀 Do you need to sync your code?
</summary>
<hr>

<code>git reset --hard origin/sync-12-avg-rating</code>

<hr>
</details>
