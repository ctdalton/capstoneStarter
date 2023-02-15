[React/Node setup guide](https://www.freecodecamp.org/news/how-to-create-a-react-app-with-a-node-backend-the-complete-guide/)

# Create parent npm project

This project will hold the scripts needed for Render to build and run both the server and client apps

`npm init -y`

# Create server npm project and node skeleton code.

`mkdir server && cd server && npm init -y`

create basic express server:

`touch server.js`

install express:

`npm i express`

```
// server/server.js

const express = require("express");

const PORT = process.env.PORT || 3001;

const app = express();

app.listen(PORT, () => {
  console.log(`Server listening on ${PORT}`);
});
```

create npm start script: `"start": "node server.js",`

# Create react skeleton code

run from parent directory: `npx create-react-app client`

add proxy to client/package.json: `"proxy": "http://localhost:3001",`

# Start both apps in different terminals

```
cd server && npm start
cd client && npm start
```

# Use fetch API in App.js to make a request to server

```
// client/src/App.js

import React from "react";
import logo from "./logo.svg";
import "./App.css";

function App() {
  const [data, setData] = React.useState(null);

  React.useEffect(() => {
    fetch("/api")
      .then((res) => res.json())
      .then((data) => setData(data.message));
  }, []);

  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>{!data ? "Loading..." : data}</p>
      </header>
    </div>
  );
}

export default App;
```

update server/server.js to serve React files

```
const path = require("path");
```

```
// Have Node serve the files for our built React app
app.use(express.static(path.resolve(__dirname, '../client/build')));

// Handle GET requests to /api route
app.get("/api", (req, res) => {
  res.json({ message: "Hello from server!" });
});
```

in server project:

install path

```
npm i path
```

create npm start-dev script:

```
"start-dev": "nodemon server.js",
```

run the start-dev script. You should now see the React logo along with your the 'Hello from server!' message sent from the sever.

# Setup Git

remove Git repo that is automatically created by create-react-app

```
rm -rf client/.git
```

intialize git:

```
git init
```

create .gitignore and add node_modules to it:

```
touch .gitignore && echo node_modules > .gitignore
```

add and commit all:

```
git add -A && git commit -m 'initial commit
```

create a new repo in github
add the newly create repo as a remote

```
git remote add origin https://github.com/your-github-username/your-repo.git
```

push your application: `git push -u origin master`

# Deploy to Render

[Deploy to Render guide](https://github.com/MediaComem/comem-archioweb/blob/main/guides/deploy-in-the-cloud.md)

[Create a Render account.](https://dashboard.render.com/register?next=/) If you register through GitHub, you will not have to link the two accounts together later.

Go to your Render dashboard and create a new Web Service
![render dashboard](https://github.com/MediaComem/comem-archioweb/raw/main/guides/images/render-02-create.png)

Connect your GitHub repository to Render by selecting the one the contains your app from the list.
![render connect](https://github.com/MediaComem/comem-archioweb/raw/main/guides/images/render-03-connect.png)

add build script in main package.json:

```
"build": "cd server && npm i && cd ../client && npm i && npm run build",
```

add start script in main package.json:

```
"start": "node server/server.js",
```

add engine in main package.json:

```
"engines": {
  "node": "your-node-version"
}
```

set Render build command to: `npm run build`
set Render start command to: `npm run start`

![alt text](https://github.com/ctdalton/student-registration/blob/master/renderInfo.png?raw=true)

click Create Web Service

Create a MongoDB cluster on MongoDB Atlas
Allow access from anywhere in atlas network access tab
Create database user in Database Access tab
Get connection url in deployment > database > connect
Provide your database URL to your Render application env variables

# Set up eslint

in server project:

```
npm i eslint -D
npm init @eslint/config
```

may need to add node to env property of .eslintrc

```
 "env": {
    "browser": true,
    "commonjs": true,
    "es2021": true,
    "node": true
  },
```

add script: `"lint": "eslint **/*.js --fix"`
