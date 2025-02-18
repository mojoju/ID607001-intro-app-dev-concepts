# 03: Node.js REST API 1

## Introduction

You consume **APIs** daily. They enable applications to communicate with each other, internally or externally.

The type of **API** you will develop and eventually consume is **REST** or **RESTful**. It is a set of architectural constraints. What it is not is a protocol or a standard. When a request is sent, it transfers a representation of the resource's state to the endpoint. This representation is delivered in one of many formats via **HTTP** such as **JSON**, **HTML**, etc.

### REST vs. RESTful

**REST** stands for **Representational State Transfer** and is a set of constraints. These constraints include client-server, stateless, uniform interface and cacheable. In essence, if an **API** adheres to these constraints, then the **API** is **RESTful**.

**Resource:** <https://blog.devmountain.com/what-is-the-difference-between-rest-and-restful-apis>

### Anatomy of a REST API

The following table describes the different **HTTP methods**:

| Operation | HTTP Method | Description                              |
| --------- | ----------- | ---------------------------------------- |
| Create    | POST        | Creates a new resource.                  |
| Read      | GET         | Provides read-only access to a resource. |
| Update    | PUT         | Updates an existing resource.            |
| Delete    | DELETE      | Removes a resource.                      |

There are a few others, but you will only be concerned with the four above.

## Express

As described, **Express** is a lightweight **Node.js** web application framework that provides a set of robust features for creating applications.

Assume you have **Node.js** installed. Create a new directory called `03-node-js-rest-api-1`. Change your current directory to `03-node-js-rest-api-1`. Now, it should be your working directory.

```bash
mkdir 03-node-js-rest-api-1
cd 03-node-js-rest-api-1
```

Open this directory in **Visual Studio Code**.

```bash
code .
```

Create a `package.json` file for your **REST API**.

```bash
npm init
```

It will prompt you to enter your **REST API's** name, version, etc. For now, you can press <kbd>Return</kbd> to accept the default values except for the following:

```bash
entry point: (index.js)
```

Enter `app.js` or whatever you want the name of the entry point file to be.

Install **Express** as a dependency. You can check whether it has been installed in `package.json`.

```bash
npm install express
```

Majority of the online **Node.js** examples use **CommonJS**. You are going to use **Modules** instead. In `package.json`, you need to add `"type": "module",` under `"main": "app.js",`.

**Resources:**

- <https://expressjs.com>
- <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules>

## In-memory storage

You will explore **database storage** next week. For now, you will use **in-memory storage** via a local file.

Create a new file called `data.js`. This file will contain an **array** of **objects**.

```javascript
const institutions = [
  { id: 1, name: "Otago Polytechnic" },
  { id: 2, name: "Southern Institute of Technology" },
];

export { institutions }; 
```

You need to export it to use `institutions` outside of `data.js`. `export { institutions };` is an example of a named export. It means that when you import `institutions` in `controllers/institutions.js`, you **must** use `institutions` as the import name. However, if you want to change `institutions`, you can use an alias. **Note:** Named exports are enclosed in curly braces.

**Resource:** JavaScript ES6 Modules - <https://www.youtube.com/watch?v=RMl-ystfzoY&t=246s>

Feel free to add more **objects** to the **array** or even more properties.

## Controllers

In the root directory, create a new directory called `controllers`. In this directory, create a new file called `institutions.js`. In `institutions.js`, you will write functions associated with the **HTTP methods** mentioned above.

To access `institutions` from `data.js`, you need to add the following:

```javascript
// Importing institutions. Note institutions is enclosed in curly braces
import { institutions } from "../data.js";

// Importing institutions with an alias
// import { institutions as randomName } from "../data.js";
```

### Get function

All functions have at least two parameters - `req` and `res`. If you go to the route that is mapped to this function, you will be presented with a **JSON** response that contains `success` and `data`.

```javascript
const getInstitutions = (req, res) => {
  res.status(200).json({ success: true, data: institutions });
};
```

### Create function

There is a little more going on here. When you make a **POST** request, you will send data with it. Also, it is important to validate the data before it is sent.

```javascript
const createInstitution = (req, res) => {
  const { name } = req.body;

  if (!name) {
    return res
      .status(400)
      .json({ success: false, msg: "Please provide a name" });
  }

  const id = institutions[institutions.length - 1].id + 1;
  // Key names are identical to assignment variables, so use property value shorthand
  institutions.push({
    id,
    name,
  });
  res.status(201).json({ success: true, data: institutions });
};
```

### Update function

You need to find the `institution` you want to update.

```javascript
const updateInstitution = (req, res) => {
  const { id } = req.params;
  const institution = institutions.find(
    (institution) => institution.id === Number(id)
  );

  // Check if institution does exist
  if (!institution) {
    return res
      .status(404)
      .json({ success: false, msg: `No institution with the id ${id}` });
  }

  const newInstitutions = institutions.map((institution) => {
    if (institution.id === Number(id)) {
      // If institution does exist, update its name
      institution.name = req.body.name;
    }
    return institution;
  });

  res.status(200).json({ success: true, data: newInstitutions });
};
```

### Delete function

Similar to the update function.

```javascript
const deleteInstitution = (req, res) => {
  const { id } = req.params;
  const institution = institutions.find(
    (institution) => institution.id === Number(id)
  );

  // Check if institution does exist
  if (!institution) {
    return res
      .status(404)
      .json({ success: false, msg: `No institution with the id ${id}` });
  }

  const newInstitutions = institutions.filter(
    (institution) => institution.id !== Number(id) // If institution does exist, delete it
  );
  res.status(200).json({ success: true, data: newInstitutions });
};
```

You need to export these functions to use them outside of `institutions.js`.

```javascript
export {
  getInstitutions,
  createInstitution,
  updateInstitution,
  deleteInstitution,
};
```

It may be hard to visualise at the moment, but it will become clearer soon.

**Resource:** <https://developer.mozilla.org/en-US/docs/Web/HTTP/Status>

## Routes

In the root directory, create a new directory called `routes`. In this directory, create a new file called `institutions.js`. In `institutions.js`, you will create four routes and map them to the functions imported from `controllers/institutions.js`.

```javascript
import { Router } from "express";
const router = Router(); // Accessing the Router() object from express. This allows to handle various requests

// Importing the four functions
import {
  getInstitutions,
  createInstitution,
  updateInstitution,
  deleteInstitution,
} from "../controllers/institutions.js";

// Four routes that are mapped to the functions above
router.route("/").get(getInstitutions);
router.route("/").post(createInstitution);

router.route("/:id").put(updateInstitution);
router.route("/:id").delete(deleteInstitution);

// You can chain these if you wish. For example:
// router.route("/").get(getInstitution).post(createInstitution)
// router.route("/:id").put(updateInstitution).delete(deleteInstitution)

export default router; // You do not need to enclose router in curly braces
```

`export default router;` is an example of a default export. It means that when you import `routes/institutions.js` in `app.js`, you can use whatever name you want as the import name. Also, much like the name export, you can change the name using an alias. **Note:** Default exports are not enclosed in curly braces.

## Entry point

In the root directory, create a new file called `app.js`. It is your **REST API's** entry point. Open a terminal and run the following command:

```bash
node app.js
```

Nothing happened. Why? It is because `app.js` is empty.

You need to add the following:

```javascript
import express from "express";

// Access all the routes exported from routes/institutions.js
import institutions from "./routes/institutions.js";

// Create an Express application
const app = express();

const PORT = process.env.PORT || 3000;

// Express middleware
app.use(express.urlencoded({ extended: false })); // Parses incoming requests with urlencoded payloads
app.use(express.json()); // Parses incoming requests with JSON payloads

// To make it clear to the consumer that the application is an API, prefix the endpoint with /api
app.use("/api/institutions", institutions);

// Listening on port 3000
app.listen(PORT, () => {
  console.log(`Server is listening on port ${PORT}`);
});
```

Rerun the following command:

```bash
node app.js
```

Navigate to <http://localhost:3000/api/institutions>

## Postman

**Postman** is an **API** development environment that allows you to design, mock and test your **APIs**. The examples below are using the **desktop client**. Alternatively, you can use the **web client**. The interface is much the same on both **clients**.

To use **Postman**, you need to create an account or sign in. There are two sign-in options - username/password and **Google**.

<img src="https://github.com/otago-polytechnic-bit-courses/ID607001-intro-app-dev-concepts/blob/master/resources/img/03-node-js-rest-api-1/03-node-js-rest-api-1.JPG" />

Once you sign in, it will navigate you to **Home**.

<img src="https://github.com/otago-polytechnic-bit-courses/ID607001-intro-app-dev-concepts/blob/master/resources/img/03-node-js-rest-api-1/03-node-js-rest-api-2.JPG" />

Next to the **Home** tab is the **Workspaces** dropdown. Click this and create a **New Workspace**.

<img src="https://github.com/otago-polytechnic-bit-courses/ID607001-intro-app-dev-concepts/blob/master/resources/img/03-node-js-rest-api-1/03-node-js-rest-api-3.JPG" />

To keep things consistent with the **MongoDB Atlas** database, you will create next week, name your new workspace `id607001-<Your OP username>`. Once you have done this, click the **Create workspace and team** button in the bottom right-hand corner.

<img src="https://github.com/otago-polytechnic-bit-courses/ID607001-intro-app-dev-concepts/blob/master/resources/img/03-node-js-rest-api-1/03-node-js-rest-api-4.JPG" />

Click the **Go to Workspace** button.

<img src="https://github.com/otago-polytechnic-bit-courses/ID607001-intro-app-dev-concepts/blob/master/resources/img/03-node-js-rest-api-1/03-node-js-rest-api-5.JPG" />

You should see something like this:

<img src="https://github.com/otago-polytechnic-bit-courses/ID607001-intro-app-dev-concepts/blob/master/resources/img/03-node-js-rest-api-1/03-node-js-rest-api-6.JPG" />

Click the **+** button next to the **Overview** tab.

Here you will make your first request. Enter a request URL, i.e., <http://localhost:3000/api/institutions>, then click the **Send** button.

<img src="https://github.com/otago-polytechnic-bit-courses/ID607001-intro-app-dev-concepts/blob/master/resources/img/03-node-js-rest-api-1/03-node-js-rest-api-7.JPG" />

**POST** request example:

<img src="https://github.com/otago-polytechnic-bit-courses/ID607001-intro-app-dev-concepts/blob/master/resources/img/03-node-js-rest-api-1/03-node-js-rest-api-8.JPG" />

**Note**: Creating a new institution will not mutate the `institutions` **array** of **objects** in `data.js`. Though, it will mutate the copy imported.

**PUT** request example:

<img src="https://github.com/otago-polytechnic-bit-courses/ID607001-intro-app-dev-concepts/blob/master/resources/img/03-node-js-rest-api-1/03-node-js-rest-api-9.JPG" />

**Note:** This will change the institution's name.

**DELETE** request example:

<img src="https://github.com/otago-polytechnic-bit-courses/ID607001-intro-app-dev-concepts/blob/master/resources/img/03-node-js-rest-api-1/03-node-js-rest-api-10.JPG" />

**Note:** Much like creating a new institution, it will not mutate the `institutions` **array** of **objects** in `data.js`.

## Final thoughts

You need to understand that there are many approaches to this. I encourage you to explore the internet for others.

## Formative assessment

In this **in-class activity**, you will plan your **REST API** for the **Project 1: Node.js REST API** assessment. In addition, you will explore how to automatically restart your **REST API's** server using **Nodemon** and get an institution by its id.

### Submission

You must submit all program files via **GitHub Classroom**. Here is the URL to the repository you will use for this **in-class activity** – <https://classroom.github.com/a/_6KSahyX>. If you wish to have your code reviewed, message the course lecturer on **Microsoft Teams**.

### Getting started

Open your repository in **Visual Studio Code**. Create a **REST API** using **Express** and **in-memory storage** as described in the lecture notes above.

### Nodemon

**Nodemon** is a tool that helps you develop **Node.js** applications by automatically restarting the application when a file change is detected. It does not require additional changes to the application's code to get started. To use **Nodemon**, install it as a development dependency. In `package.json`, replace the `start` script value `node app.js` with `nodemon app.js`.

**Resource:** <https://www.npmjs.com/package/nodemon>

### Get an institution by its id

In the [lecture notes](https://github.com/otago-polytechnic-bit-courses/ID607001-intro-app-dev-concepts/blob/master/lecture-notes/03-node-js-rest-api-1.md), you looked at how to create, read, update and delete an institution. Extend your **REST API's** functionality by creating a **route** and **controller** function that enables a user to get an institution by its id. **Hint:** The approach is similar to updating and deleting an institution.

### Project 1: Node.js REST API planning

You will be starting your **Project 1: Node.js REST API** assessment next week. Before you start, you need to decide your **REST API's** theme and the data you are going to display to the user. You need at least **five collections** (**user collection** is included) with at least **three fields** of data. **Note:** The **user collection** will contain at least a name, email and password. However, you can additional **fields**. Also, show the relationships between **collections**. **Note:** You need at least **two relationships** between **collections**. You can display this anyway you wish, i.e., UML, text, etc, as long as it is clear to the **course lecturer** when reviewing. Please send your plan via **Microsoft Teams** or **Outlook**.
