

**TL;DR**
`npm create vite@latest`
`npm i react-router express, cors, mongoose, mongodb`
`npm i --save-dev nodemon chai mocha cypress`
`npm run dev`
`npx cypress open`

Create `/server`, `/test`, `/src/components`
Create `/server/server.js`, `/test/*.test.mjs`


**React, React DOM, React Router**
`npm create vite@latest`

![[Pasted image 20241216113218.png]]

- `main.jsx`
```jsx
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import './index.css'
import App from './App.jsx'
createRoot(document.getElementById('root')).render(
    <StrictMode>
        <App />
    </StrictMode>,
)
```
- `App.jsx`
```jsx
// IMPORT
import './App.css'

function App() {
    // HOOKS

    // HTML
    return (
        <>

        </>
    )
}

// EXPORT
export default App
```

Go into the newly created folder and run:
`npm install`
`npm run dev`

Install the following if not already existing:
`npm install react-router`

**Essentials**
`npm install --save-dev nodemon`

**Express, CORs, Mongoose**
`npm install express, cors, mongoose, mongodb`
Create a folder named `/server` with a file named `server.js`.

**Mocha, Chai**
`npm install --save-dev mocha chai`
Create a folder named `/test` with multiple test files ending in `*.test.mjs`.

**Cypress**
`npm install --save-dev cypress`
`npx cypress open`

**package.json**
```json
    "scripts": {
        "dev": "concurrently \"npm run client\" \"npm run server\"",
        "prod": "npm run build && npm run start",
        "client": "vite",
        "server": "nodemon --inspect server/server.js",
        "build": "vite build",
        "start": "node server/server.js",
        "test": "mocha && cypress"
    },
```

**Vite Config**
```js
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
                // rewrite: path => path.replace('/animals', ''), // If you want to remove the /api prefix
                configure: (proxy, _options) => {
                    proxy.on('error', (err, _req, _res) => {
                        console.log('proxy error', err);
                    });
                    proxy.on('proxyReq', (proxyReq, req, _res) => {
                        console.log('Sending Request to the Target:', req.method, req.url);
                    });
                    proxy.on('proxyRes', (proxyRes, req, _res) => {
                        console.log('Received Response from the Target:', proxyRes.statusCode, req.url);
                    });
                },
            }
        }
    },
})

```

