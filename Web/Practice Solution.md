## File Structure
This is the file system in the solution branch.
```
server/
	server.js
src/
	App.css
	App.jsx
	index.css
	main.jsx
.gitignore
index.html
package-lock.json
package.json
README.md
test_image_links.txt
vite.config.ts	
```

## server/server.js
#### Practice
```js
import express from 'express';
import cors from "cors";
import mongoose from 'mongoose';
import path from 'path';
import url from 'url';

const __dirname = path.dirname(url.fileURLToPath(import.meta.url));
const buildFolder = "../dist";

//TODO

//TESTING:
//npm run dev to start React app and Express server
//npm run prod to build React app and start Express server
```

#### Solution
```js
import express from 'express';
import cors from "cors";
import mongoose from 'mongoose';
import path from 'path';
import url from 'url';

const __dirname = path.dirname(url.fileURLToPath(import.meta.url));
const buildFolder = "../dist";

const app = express();

const animalSchema = new mongoose.Schema({
    name: { type: String, required: true, unique: true },
    pictureURL: { type: String, required: true }
});

const Animal = mongoose.model("Animal", animalSchema);

const dbURL = "mongodb+srv://final-exam-practice:final-exam-practice@pfw-cs.ctovaum.mongodb.net/final-exam-practice?retryWrites=true&w=majority";
await mongoose.connect(dbURL);
console.log("Connected to database!");

app.use(cors());
app.use(express.json());

//server static React build files
app.use(express.static(path.join(__dirname, buildFolder)));

app.get("/", (req, res) => {
    res.sendFile(path.join(__dirname, buildFolder, "index.html"));
});

//upload route
app.post("/animals/upload", async (req, res) => {
    try {
        const newAnimal = new Animal({ name: req.body.name, pictureURL: req.body.pictureURL });
        await newAnimal.save();
        const message = `Animal ${req.body.name} uploaded!`
        console.log(message);
        res.send(message);
    }
    catch (err) {
        console.log(err.message);
        res.send(err.message);
    }
})

//search route
app.get("/animals/search", async (req, res) => {
    console.log(req.query.name);
    const animal = await Animal.findOne({ name: req.query.name });
    if (animal) {
        res.send(animal.pictureURL);
    }
    else {
        res.status(404).send("Animal not found.");
    }
})

//clear route
app.delete("/animals/clear", async (req, res) => {
    await Animal.deleteMany({});
    res.send("All animals deleted.")
})

const port = 8080;
app.listen(port, () => {
    console.log(`Server running on port ${port}`);
});

//npm run dev to start React app and Express server
//npm run build and npm start to build React app and start Express server
```

## src/App.jsx
#### Practice
```jsx
import './App.css'

function App() {
    //Any needed hooks
    //TODO

    //Functions for API
    //upload
    async function upload(e) {
        e.preventDefault();  
        //TODO
    }
    
    //search
    async function search(e) {
        e.preventDefault();
        //TODO
    }

    //clear
    async function clear(e) {
        //TODO
    }

    return (
        <div className="App">
            <h1>Animal Collection</h1>
            <h3>Add Animal</h3>
            <form onSubmit={upload}>
                <label htmlFor="name">Animal:</label>
                <input type="text" name="name" id="name" placeholder='Name' />
                <label htmlFor="picture">Picture Address:</label>
                <input type="text" name="picture" id="picture" />
                <button type='submit'>Upload</button>
            </form>

            <h3>Search Animal</h3>
            <form onSubmit={search}>
                <label htmlFor="name">Name:</label>
                <input type="text" name="name" id="name" />
                <button type='submit'>Search</button>
            </form>
            
            <p>
                {/* TODO */}
            </p>
            <section>
                <img src={/* TODO */} height={200} alt="Animal Image" />
            </section>

            <button onClick={clear}>Clear Animal Database</button>
            
        </div>
    )
};

export default App;

//npm run dev to start React app and Express server
```

#### Solution
```jsx
import { useEffect, useState } from 'react'
import './App.css'

function App() {
    //Any needed hooks
    const [result, setResult] = useState("")
    const [image, setImage] = useState(null)

    useEffect(() => {
        setTimeout(() => {
            setResult("");
        }, 3000)
    }, [result]);

    //Functions for API
    //upload
    async function upload(e) {
        e.preventDefault();

        const res = await fetch("/animals/upload", {
            method: "POST",
            headers: {
                "Content-Type": "application/json"
            },
            body: JSON.stringify({
                name: e.target.name.value,
                pictureURL: e.target.picture.value
            })
        });

        if (res.status == 200) {
            const result = await res.text();
            setResult(result);
        }
    }

    //search
    async function search(e) {
        e.preventDefault();

        const res = await fetch(`/animals/search?name=${e.target.name.value}`);

        if (res.status == 200) {
            const URL = await res.text();
            setImage(URL);
        }
        else {
            const result = await res.text();
            setResult(result);
            setImage(null);
        }
    }

    //clear
    async function clear(e) {

        const res = await fetch(`/animals/clear`, {
            method: "DELETE"
        });

        if (res.status == 200) {
            const result = await res.text();
            setResult(result);
        }

        setImage(null);
    }

    return (
        <div className="App">
            <h1>Animal Collection</h1>
            <h3>Add Animal</h3>
            <form onSubmit={upload}>
                <label htmlFor="name">Animal:</label>
                <input type="text" name="name" id="name" placeholder='Name' />
                <label htmlFor="picture">Picture Address:</label>
                <input type="text" name="picture" id="picture" />
                <button type='submit'>Upload</button>
            </form>

            <h3>Search Animal</h3>
            <form onSubmit={search}>
                <label htmlFor="name">Name:</label>
                <input type="text" name="name" id="name" />
                <button type='submit'>Search</button>
            </form>

            <p>
                <b>{result}</b>
            </p>
            <section>
                <img src={image} height={200} alt="Animal Image" />
            </section>

            <button onClick={clear}>Clear Animal Database</button>

        </div>
    )
};

export default App;

//npm run dev to start React app and Express server
//npm run prod to build React app and start Express server
```


## Extras

### src/main.jsx
```jsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App'
import './index.css'

ReactDOM.createRoot(document.getElementById('root')).render(
    <React.StrictMode>
        <App />
    </React.StrictMode>,
)
```

### package.json
```json
{
    "name": "animalcollection",
    "private": true,
    "version": "0.0.0",
    "type": "module",

    "scripts": {
        "dev": "concurrently \"npm run client\" \"npm run server\"",
        "prod": "npm run build && npm run start",
        "client": "vite",
        "server": "nodemon --inspect server/server.js",
        "build": "vite build",
        "start": "node server/server.js"
    },

    "dependencies": {
        "concurrently": "^8.0.1",
        "cors": "^2.8.5",
        "express": "^4.18.2",
        "http-proxy": "^1.18.1",
        "mongodb": "^5.2.0",
        "mongoose": "^7.0.4",
        "react": "^18.2.0",
        "react-dom": "^18.2.0"
    },

    "devDependencies": {
        "@types/react": "^18.0.28",
        "@types/react-dom": "^18.0.11",
        "@vitejs/plugin-react": "^3.1.0",
        "nodemon": "^3.1.0",
        "vite": "^4.2.0"
    }
}
```

### vite.config.ts
```ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// https://vitejs.dev/config/
export default defineConfig({
    plugins: [react()],
    server: {
        proxy: {
            '/animals': {
                target: "http://localhost:8080",
                changeOrigin: true,
                secure: false,
                // rewrite: path => path.replace('/animals', ''),
                // If you want to remove the /api prefix
                configure: (proxy, _options) => {
                    proxy.on('error', (err, _req, _res) => {
                        console.log(
	                        'proxy error', 
	                        err
		                );
                    });
                    proxy.on('proxyReq', (proxyReq, req, _res) => {
                        console.log(
	                        'Sending Request to the Target:',
	                        req.method,
	                        req.url
	                    );
                    });

                    proxy.on('proxyRes', (proxyRes, req, _res) => {
                        console.log(
	                        'Received Response from the Target:',
	                        proxyRes.statusCode,
	                        req.url
                        );
                    });
                },
            }
        }
    },
})
```

### src/App.css
```css
#root {
    max-width: 1280px;
    margin: 0 auto;
    padding: 2rem;
    text-align: center;
}

.logo {
    height: 6em;
    padding: 1.5em;
    will-change: filter;
    transition: filter 300ms;
}

.logo:hover {
    filter: drop-shadow(0 0 2em #646cffaa);
}

.logo.react:hover {
    filter: drop-shadow(0 0 2em #61dafbaa);
}

@keyframes logo-spin {
    from {
        transform: rotate(0deg);
    }

    to {
        transform: rotate(360deg);
    }
}

@media (prefers-reduced-motion: no-preference) {
    a:nth-of-type(2) .logo {
        animation: logo-spin infinite 20s linear;
    }
}

.card {
    padding: 2em;
}

.read-the-docs {
    color: #888;
}
```

### index.css
```css
:root {
    font-family: Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
    line-height: 1.5;
    font-weight: 400;

    color-scheme: light dark;
    color: rgba(255, 255, 255, 0.87);
    background-color: #242424;

    font-synthesis: none;
    text-rendering: optimizeLegibility;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    -webkit-text-size-adjust: 100%;
}

a {
    font-weight: 500;
    color: #646cff;
    text-decoration: inherit;
}

a:hover {
    color: #535bf2;
}

body {
    margin: 0;
    display: flex;
    place-items: center;
    min-width: 320px;
    min-height: 100vh;
}

h1 {
    font-size: 3.2em;
    line-height: 1.1;
}

button {
    border-radius: 8px;
    border: 1px solid transparent;
    padding: 0.6em 1.2em;
    font-size: 1em;
    font-weight: 500;
    font-family: inherit;
    background-color: #1a1a1a;
    cursor: pointer;
    transition: border-color 0.25s;
}

button:hover {
    border-color: #646cff;
}

button:focus,
button:focus-visible {
    outline: 4px auto -webkit-focus-ring-color;
}

@media (prefers-color-scheme: light) {
    :root {
        color: #213547;
        background-color: #ffffff;
    }

    a:hover {
        color: #747bff;
    }

    button {
        background-color: #f9f9f9;
    }
}
```

### index.html
```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Animal Collection</title>
</head>

<body>
    <div id="root"></div>
    <script type="module" src="/src/main.jsx"></script>
</body>

</html>
```

### test_image_links.txt
```txt
Lion
https://i1.sndcdn.com/avatars-zlqzZpaIOogVFZ5a-jjNVHw-t500x500.jpg

Lamb
https://a-z-animals.com/media/2022/01/lamb-grazing-on-green-grass-meadow-picture-id665494268-1024x535.jpg

Snake
https://t4.ftcdn.net/jpg/03/40/75/71/360_F_340757151_smR8wXJf6QqmvCsHmQyNpGYq6c6YyFIh.jpg
```
