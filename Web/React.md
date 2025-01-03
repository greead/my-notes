[<< Essentials](Essentials.md) ‧ [Express >>](Express.md)
### JS Essentials
[[Essentials#JavaScript]]

## Setup
`npm create vite@latest .`
`npm install`

## React Components
Each React component is a function that may contain some markup to be rendered in the browser. Often this is done with JSX.
## JSX
Templating (HTML) + Scripting (JS) that compiles to React elements.

**Embedded HTML in JSX**
Write JavaScript modules, include HTML anywhere within the JS
```jsx
export default function fn() {
	do_stuff();
	return <h1>Some HTML</h1>;
}
```

**Special Rules**
- Must return a single root element from the component, for example:
```jsx
// Invalid
export default function ReactComponent() {
	return (
		<.../>
		<.../>
		<.../>
	)
}

// Encapsulate in a single element
export default function ReactComponent() {
	return (
		<div>
			<.../>
			<.../>
			<.../>
		</div>
	)
}

// ...or encapsulate in a Fragment
export default function ReactComponent() {
	return (
		<>
			<.../>
			<.../>
			<.../>
		</>
	)
}
```
- Close all tags, for example:
	- Must use `<img />` instead of `<img>`
	- Must use `<li> ... </li>` instead of `<li> ...`
- HTML attributes are all camelCase , for example:
	- HTML `<... onclick=...>` must be replaced with `<... onClick=...>`
- Some HTML keywords are JS reserved keywords and so have replacements:
	- HTML `<... class=...>` must be replaced with `<... className=...>`

**Embedded JS Strings in HTML**
Include strings within embedded HTML using `' '` or `" "`
```jsx
export default function fn() {
	do_stuff();
	return <h1 id="my-id">Some HTML</h1>;
}
```

**Embedded JS in HTML**
Include JS within embedded HTML (within tags or as attributes) using `{ }`
```jsx
let x = 10;
export default function fn() {
	do_stuff();
	return <h1>Some {x}</h1>;
}
```

HTML embedded JS Code (`{ }` code) must be value-returning, for example:
- Must use `Array.map` instead of `for`
- Must use ternaries `exp ? true : false` instead of `if`

**Embedded JS objects in HTML**
- Objects in JSX with "double curlies" `{{ }}`
	- Useful for inline CSS, each object property is a CSS property
	- Replace kebab-case with camelCase: `background-color` to `backgroundColor`
- Objects in JSX can also be referenced

## Props
Parent components can pass information to child components by passing *props* to them. Predefined tags have predefined props corresponding to HTML standard.

**Pass props to a child**
```jsx
export default function Profile() {
	return (
		<Avatar
			person={{ name: 'Lin Lanying', imageId: '1bX5QH6' }}
			size={100}
	    />
	);
}
```

**Receive props from a parent**
```jsx
function Avatar({ person, size }) {  
	// person and size are available here  
}
```
Props are always received as a single object argument.

**Default props**
```jsx
function Avatar({ person, size = 100 }) {  
	// person and size are available here  
}
```

**Forwarding props by spreading**
```jsx
export default function Profile(props) {
	return (
		<Avatar {...props} />
	);
}
```

**Passing Children**
If we pass a child component into our component tag as follows...
```jsx
<Card>
	<Avatar />
<Card>
```

We can access it with the `children` prop as follows...
```jsx
function Card({ children }) {
	return (
		<div className="card">
			{children}
		</div>
	);
}
```

This kind of structure is often used for wrappers.

## Conditional Rendering
```jsx
if (...) {
	return ...	
} else {
	return ...
}
```

[JS && expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND)

## List Rendering
**Render a list with map**
Must include a key for JSX elements in a map.
```jsx
function comp() {
	let count = 0
	const people = [
		'Creola Katherine Johnson: mathematician',
		'Mario José Molina-Pasquel Henríquez: chemist',
		'Mohammad Abdus Salam: physicist',
		'Percy Lavon Julian: chemist',
		'Subrahmanyan Chandrasekhar: astrophysicist'
	];
	const listItems = people.map(person => <li key={count++}>{person}</li>);
	return <ul>{listItems}</ul>;
}
```

**Filter and render with filter**
```jsx
function comp() {
	count = 0
	const people = [{
		id: 0,
		name: 'Creola Katherine Johnson',
		profession: 'mathematician',
	}, {
		id: 1,
		name: 'Mario José Molina-Pasquel Henríquez',
		profession: 'chemist',
	}, {
		id: 2,
		name: 'Mohammad Abdus Salam',
		profession: 'physicist',
	}, {
		id: 3,
		name: 'Percy Lavon Julian',
		profession: 'chemist',  
	}, {
		id: 4,
		name: 'Subrahmanyan Chandrasekhar',
		profession: 'astrophysicist',
	}];
	const chemists = people.filter(person =>
		person.profession === 'chemist'
	);
	const listItems = chemists.map(person => 
		<li key={count++}>{person.name}</li>
	);
	return <ul>{listItems}</ul>;
```

- Arrow function implicit returns if no curly braces `.. => ..`
- Must include `return` if using curly braces `.. => { return .. }`

## React Purity
No side effects.

## Events
**Pass Events**
Passing events through event props.
```jsx
export default function Button() {
	function handleClick() {
	    alert('You clicked me!');
	}

	return (
		<button onClick={handleClick}>
			Click me
		</button>
	);
}
```

Inline handler.
```jsx
export default function Button() {
	return (
		<button onClick={function handleClick() {
			alert('You clicked me!');
		}}>
			Click me
		</button>
	);
}
```

Arrow handler.
```jsx
export default function Button() {
	return (
		<button onClick={() => {alert('You clicked me!');}}>
			Click me
		</button>
	);
}
```

Remember to pass handler functions, don't call them!

Consider that event props are just props. Your components can have any name for a prop.

**Event Propagation**
When activating an event on a child, the events of all parents capture that event too. So if a parent captures onClick events, and an onClick event occurs in one of its child components, then the child event handler will occur first followed by the parent event handler.

All events propagate except onScroll.

**Event objects**
Event handlers receive event objects as their only parameter, usually given the name `e`.
```jsx
function handler(e) {
	...
}
```

```jsx
...
	<button onClick={e => {...}}> ... </button>
...
```

**Stopping Propagation**
```jsx
...
	<button onClick={e => {
		e.stopPropagation(); 
		...
	}}> 
		... 
	</button>
...
```

Can alternatively do handler passing instead of propagation.
```jsx
function Button({ onClick, children }) {
	<button onClick={e => {
		e.stopPropagation(); // Stop propagation and
		onClick(); // Take care of the method here
	}}> 
		{ children }
	</button>
}
```

Event handlers can have side effects.

## Hooks
Hooks can only be called at the top level of a component or within custom hooks (cannot call them inside conditions, loops, or other nested functions).

- [[#useState]]
- [[#useReducer]]
- [[#useRef]]
- [[#createContext / useContext]]
- [[#useEffect]]

## useState
Local variables don't persist between renders and changing local variables does not trigger renders. Instead we can use hooks which persist between renders and work with React better, such as being able to trigger renders.

**Importing useState**
```jsx
import { useState } from 'react';
```

**Implementing useState**
```jsx
let index = 0;

// Replace with

const [index, setIndex] = useState(0);
```

This provides the `index` state variable, `setIndex` setter function, and provides a default value of `0` to the state variable.

**State snapshot**
State doesn't change within a render, only on the next render.

**State queue**
Queue state updates by passing in a function. The function will receive a single argument for the previous state `setNumber(n => n + 1)`. This is called an updater function.

**Treat state objects and arrays as read-only**
Treat state objects and arrays as immutable, only change them with the setter function.
```jsx
onPointerMove={e => {
	setPosition({
	    x: e.clientX,
	    y: e.clientY
	});
}}
```

Use spread syntax if necessary
```jsx
setPerson({
	...person, // Copy the old fields
	firstName: e.target.value // But override this one
});
```

Spread is shallow.
```jsx
setPerson({
	...person, // Copy other fields
	artwork: { // but replace the artwork
	    ...person.artwork, // with the same one
		city: 'New Delhi' // but in New Delhi!
	}
});
```

**State Arrays**
```jsx
// Adding
array.concat(), [...arr]

// Removing
array.filter(), array.slice()

// Replacing
array.map()

// Sorting
// Copy, then reverse/sort
[...arr].reverse(), [..arr].sort()

```

## useReducer
Handle state in one place rather than multiple places.

- `import { useReducer } from 'react'`
- Create a reducer function that accepts two args: state and action object
- The reducer function should affect state based on the given action object
	- Usually provide a "type" to an action object
- `const [state, dispatch] useReducer(reducerFunc, initialState)`


## createContext / useContext
Share state among many components.
- Create the context first (usually in another file)
	- `import { createContext } from 'react'`
	- `export const ContextVar = createContext(defaultVal)`
- Use the context where needed with the useContext hook
	- `import { useContext } from 'react`
	- `import { ContextVar } from './ContextVar.js'`
	- `const context = useContext(ContextVar)`
- Provide the context by wrapping any components that need the context in a provider wrapper
	- `import { ContextVar } from './ContextVar.js'`
	- `<ContextVar.Provider value={val}> {children} </ContextVar.Provider>`
- Can auto pass down
	- `import { ContextVar } from './ContextVar.js'`
	- `const ctx = useContext(ContextVar)`
	- `<ContextVar.Provider value={ctx}> {children} </ContextVar.Provider>`

## useRef
Remember info without rerenders.
- `import { useRef } from 'react'`
- `const ref = useRef(init)`
- Access and update value with `ref.current`

## useEffect
side effect activity (networks, data updates, dom manip, logging)
- `import { useEffect } from 'react'`
- `useEffect(() => {})` Every render.
- `useEffect(() => {}, [depend,])` run on depend update
- `useEffect(() => {}, [])` run on mount
- `useEffect(() => {return () => {cleanup}}, [])` on mount w/ cleanup


## Custom Hook
```jsx
function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function handleOnline() {
      setIsOnline(true);
    }
    function handleOffline() {
      setIsOnline(false);
    }
    window.addEventListener('online', handleOnline);
    window.addEventListener('offline', handleOffline);
    return () => {
      window.removeEventListener('online', handleOnline);
      window.removeEventListener('offline', handleOffline);
    };
  }, []);
  return isOnline;
}
```
## Forms
**Preventing default behavior**
Form forces a page reload by default. We can prevent this behavior using `e.preventDefault()`.
```jsx
export default function Signup() {
	return (
		<form onSubmit={e => {
			e.preventDefault();
			alert('Submitting!');
		}}>
			<input />
		    <button>Send</button>
	    </form>
	);
}
```

[YUP](https://github.com/bolitj01/ReactLibraryExamples/blob/main/react_hook_form/user_form/src/App.jsx)

## Subcomponents from Arrays



## API Calls

- Axios/Fetch

**useEffect Fetching**
```jsx
useEffect(() => {
  let ignore = false;

  async function startFetching() {
    const json = await fetchTodos(userId);
    if (!ignore) {
      setTodos(json);
    }
  }

  startFetching();

  return () => {
    ignore = true;
  };
}, [userId]);
```

```jsx
import { useState, useEffect } from 'react';
const Fetch = () => {
  const [photos, setPhotos] = useState([]);
  useEffect(() => {
    fetch('https://jsonplaceholder.typicode.com/photos')
      .then((res) => {
        return res.json();
      })
      .then((data) => {
        console.log(data);
        setPhotos(data);
      });
  }, []);
  return (
    <div>

      {photos.map((photo) => (
        <img key={photo.id} src={photo.url} alt={photo.title} width={100} />
      ))}
    </div>
  );
};
export default Fetch;
```

## Debugging

[REACT DEV TOOLS](https://chromewebstore.google.com/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi?hl=en)

## React Router

```jsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import { createBrowserRouter, RouterProvider } from "react-router-dom";
import ExampleNavigator from "./ExampleNavigator";
import Basic from "./basic/Basic";
import Nested from "./nested_routes/Nested";
import Topics from "./nested_routes/Topics";
import Topic from "./nested_routes/Topic";
import Search from "./nested_routes/Search";
import Protected from "./protected_routes_navigation/Protected";

const router = createBrowserRouter([
  {
    path: "/",
    element: <ExampleNavigator />,
  },
  {
    path: "/basic/*",
    element: <Basic />,
  },
  {
    path: "/nested/*",
    element: <Nested />,
  },
  // Alternative way to nest routes
  // {
  //   path: "/nested",
  //   element: <Nested />,
  //   children: [
  //     {
  //       path: "topics",
  //       element: <Topics />,
  //       children: [
  //         {
  //           path: ":topicName",
  //           element: <Topic />,
  //         },
  //         {
  //           path: "search/:query",
  //           element: <Search />,
  //         },
  //       ]
  //     },
  //   ]
  // },
  {
    path: "/protected/*",
    element: <Protected />,
  },
]);

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <RouterProvider router={router}>{router.element}</RouterProvider>
  </StrictMode>
);
```

```jsx
import { Link } from "react-router-dom";
import styles from "./menu.module.css";

function ExampleNavigator() {
  return (
    <>
      <h1>Examples</h1>
      <div className={styles.hide}>
        <h3 className={styles.speed1}>React Router Examples</h3>
        <ul>
          <li className={styles.speed2}>
            <Link to="/basic">Basic</Link>
          </li>
          <li className={styles.speed3}>
            <Link to="/nested">Nested Routes</Link>
          </li>
          <li className={styles.speed3}>
            <Link to="/protected">Protected Routes</Link>
          </li>
        </ul>
      </div>
    </>
  );
}

export default ExampleNavigator;
```

```jsx
import { Outlet, Routes, Route, Link } from 'react-router-dom';
import Home from './Home';
import About from './About';
import Profile from './Profile';

const Basic = () => (
    <div>
      <nav>
        <Link to="home">Home</Link>
        <br />
        <Link to="about">About</Link>
        <br />
        <Link to="profile">Profile</Link>
      </nav>
      <Routes>
        <Route path="home" element={<Home />} />
        <Route path="about" element={<About />} />
        <Route path="profile" element={<Profile />} />
      </Routes>
    </div>
);

export default Basic;
```

```jsx
import { Routes, Route } from "react-router-dom";
import Topics from "./Topics";
import Topic from "./Topic";
import Search from "./Search";

const Nested = () => {
  return (
    <Routes>
      <Route path="" element={<Topics />}>
        {/* Nested routes will begin with /topics/ */}
        <Route path=":topicName" element={<Topic />} />
        <Route path="search/:query" element={<Search />} />
      </Route>
    </Routes>
  );
};

export default Nested;
```

