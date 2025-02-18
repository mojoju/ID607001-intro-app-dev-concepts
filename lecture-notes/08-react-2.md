# 08: React 2

## Components

**Components** allow you to split your **UI** into independent, reusable chunks of **JSX**.

### Class vs. function components

In **React**, there are two ways to declare a **component**.

**Class component** example:

```js
class App extends React.Component {
  render() {
    return <h1>Hello, World!</h1>;
  }
}

export default App;
```

**Function component** example:

```js
const App = () => {
  return <h1>Hello, World!</h1>;
};

export default App;
```

From **React's** point of view, these two ways are equivalent. **Note:** Please use **function components** as it is the modern way of creating **components**.

### Create a new component

In `src`, create a new directory called `components`. In `components`, create a new file called `Welcome.js`. In this file, add the following:

```js
const Welcome = () => {
  return <h1>Welcome</h1>;
};

export default Welcome;
```

Your file structure should look something like this:

```bash
<Your OP username>-react
├── README.md
├── node_modules
├── package.json
├── .gitignore
├── public
│ ├── favicon.ico
│ ├── index.html
│ ├── logo192.png
│ ├── logo512.png
│ ├── manifest.json
│ └── robots.txt
└── src
│ ├── components
│ │  ├── Welcome.js
│ ├── App.css
│ ├── App.js
│ ├── App.test.js
│ ├── index.css
│ ├── index.js
│ ├── logo.svg
│ ├── reportWebVitals.js
│ ├── setupTests.js
```

Here is an example of how to import and use a **component**:

```js
import Welcome from "./components/Welcome"; // Importing the Welcome function component

const App = () => {
  return (
    <>
      <h1>Hello, World!</h1>
      <Welcome /> {/* Self-closing tag. <Welcome></Welcome> are equivalent */}
    </>
  );
};

export default App;
```

When you nest **components** within **components**, you create a **component tree**. Currently, the **component tree** looks like this:

```md
App (parent) <- Welcome (child)
```

**Resource:** <https://reactjs.org/docs/react-component.html>

### Props

When you declare a **class** or **function** component, you must never modify its **props (properties)**. There is one strict rule that all **components** must adhere to:

_All React components must act like pure functions with respect to their props._

If you are unsure of what a **pure function** is, please carefully read this resource - <https://www.freecodecamp.org/news/what-is-a-pure-function-in-javascript-acb887375dfe>

```js
const Welcome = (props) => {
  return (
    <h1>Welcome {props.firstName}</h1> // Pass a prop called firstName
  )
}

...
```

Here is an example of how you can reuse a **component**:

```js
...

const App = () => {
  return (
    <>
      <h1>Hello, World!</h1>
      <Welcome /> {/* It will render - Welcome an <h1> element. Note: a prop attribute is not given */}
      <Welcome firstName="John" /> {/*
                                     It will render - Welcome John in an <h1> element. Note: a prop
                                     attribute is given. Make sure the prop attribute's name is the
                                     same as what is specified in the child component, i.e., Welcome.js.
                                   */}
    </>
  )
}

...
```

Here is an example output without and with `props`:

<img src="https://github.com/otago-polytechnic-bit-courses/ID607001-intro-app-dev-concepts/blob/master/resources/img/08-react-2/08-react-1.png" width="950" height="537">

**Resource:** <https://reactjs.org/docs/components-and-props.html>

### Component lifecycle methods

Each **function component** has several **lifecycle methods** that are run at particular times during the rendering and commit process.

If you are unsure what the **component lifecycle methods** are, please carefully read this resource - <https://datacadamia.com/web/javascript/react/function/lifecycle>.

## Axios

The following few examples will be using **Axios**. Many of you have probably used a **promise** based HTTP client before, i.e., **Fetch**. You can think of **Axios** as a better alternative to **Fetch**. For more information, please carefully read this resource - <https://axios-http.com/docs/intro>.

To install **Axios**, open the terminal in **Visual Studio Code**, and run the following command:

```bash
npm i axios
```

You can check the `dependencies` block in `package.json` to make sure you have installed **Axios**.

## Hooks

**Hooks** allow you to use **state** and other features without having to use **class components**. **Hooks** "hook into" **state** and the **component lifecycle** using **function components**. **Note:** you can not use **Hooks** with **class components**.

### State Hook

Here is an example of how you can use the `useState` hook:

```js
import { useState } from "react"; // Import the useState hook from the react dependency

const Counter = () => {
  const [count, setCount] = useState(0); // Local state variable called count.

  // const [count, ...] - first argument allows you to get the state variable's value
  // const [..., setCount] - second argument allows you to set the state variables's value
  // useState(0) - using the useState hook which sets an initial state value, i.e., 0

  return (
    <div>
      <p>You clicked {count} times</p>{" "}
      {/* Rendering the count state variable in a <p> element. Note: we do not re-render any of the other elements */}
      <button onClick={() => setCount(count + 1)}>
        {" "}
        {/* Increment the count state variable by one */}
        Click me
      </button>
    </div>
  );
};

export default Counter;
```

You can declare as many state variables as you wish:

```js
const ExampleWithManyStates = () => {
  const [age, setAge] = useState(25);
  const [fruit, setFruit] = useState("apple");
  const [todos, setTodos] = useState([{ text: "Get some petrol" }]);
};
```

### Effect Hook

Here is an example of how you can use the `useEffect` hook:

```js
import axios from "axios";
import { useEffect, useState } from "react"; // Import the useEffect hook from the react dependency

const Post = () => {
  const [post, setPost] = useState([]); // State variables

  const getPosts = async () => {
    try {
      const response = await axios.get(
        "https://jsonplaceholder.typicode.com/posts/1"
      );
      setPost(response.data);
    } catch (error) {
      console.log(error);
    }
  };

  useEffect(() => {
    getPosts();
  }, []); /*
           The empty array means render once. If we pass in post, i.e., [post],
           it will re-render the component if post's data changes
         */

  return (
    <div>
      {/* Rendering the post's title and body */}
      <h1>{post.title}</h1>
      <p>{post.body}</p>
    </div>
  );
};

export default Post;
```

Here is an example output of `useState`, `useEffect` and **Axios**:

<img src="https://github.com/otago-polytechnic-bit-courses/ID607001-intro-app-dev-concepts/blob/master/resources/img/08-react-2/08-react-2.png" width="950" height="537">

**Resources:**

- <https://reactjs.org/docs/hooks-overview.html>
- <https://reactjs.org/docs/hooks-effect.html>

## Formative assessment

In this **in-class activity**, you will continue to learn the basics of **React**.

### Submission

You must submit all program files via **GitHub Classroom**. Here is the URL to the repository you will use for this **in-class activity** – <https://classroom.github.com/a/6KSahyX>. If you wish to have your code reviewed, message the course lecturer on **Microsoft Teams**. 

### Getting started

Open your repository in **Visual Studio Code** and solve the following four problems.

### Problem 1

At the top of `App.js` and in a comment, answer the following:

1. What is happening when you call `useEffect`?
2. Write a code example of how you can destructure `props` in a function component.

### Problem 2

Create a new **function component** (name it whatever you like) that has three `props` - `firstName`, `lastName` and `bandName`. Render `firstName` in an `<h1>` element, `lastName` in an `<h2>` element and `bandName` in an `<h3>` element. Import and use the **function component** in `App.js`.

**Expected output:**

<img src="https://github.com/otago-polytechnic-bit-courses/ID607001-intro-app-dev-concepts/blob/master/resources/img/08-react-2/08-react-3.png" width="450" height="350" />

### Problem 3

Create a new **function component** (name it whatever you like) that performs a **GET** request using **Axios** to the following URL - <https://api.chucknorris.io/jokes/random>.

Using the response, render an **image** in an `<img>` element and **text** in a `<p>` element. Import and use the **function component** in `App.js`.

**Expected output:**

<img src="https://github.com/otago-polytechnic-bit-courses/ID607001-intro-app-dev-concepts/blob/master/resources/img/08-react-2/08-react-4.png" width="450" height="300" />

### Problem 4

Create a new **function component** (name it whatever you like) that performs **GET** requests using **Axios** in **parallel** to the following URLs:

- <https://random-word-form.herokuapp.com/random/noun>
- <https://random-word-form.herokuapp.com/random/adjective>

Using the response, render the random noun and adjective in a `<p>` element. Import and use the **function component** in `App.js`.

**Expected output:**

<img src="https://github.com/otago-polytechnic-bit-courses/ID607001-intro-app-dev-concepts/blob/master/resources/img/08-react-2/08-react-5.png" width="450" height="300" />
