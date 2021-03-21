# The shortcut path

## Start your server

```
npm run develop // to run dev server
npm i webpack-dev-server --save-dev // to build bundle.js
```

## Setup

```
git init
echo 'node_modules\ndist/bundle.js*' >> .gitignore
npm init -y
npm i react react-dom
mkdir dist
touch dist/index.html
touch dist/bundle.js
mkdir src
touch src/index.js
mkdir src/components
npm i webpack webpack-cli
touch webpack.config.js
npm i @babel/core @babel/preset-env @babel/preset-react babel-loader
npm i webpack-dev-server --save-dev
touch src/components/App.js
```


## Webpack.config.js

```JavaScript
const path = require('path')
const webpack = require('webpack')

module.exports = {
  plugins: [
    new webpack.HotModuleReplacementPlugin()
  ],
  entry: path.resolve(__dirname, 'src', 'index.js'),
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  },
  devServer: {
    contentBase: path.resolve(__dirname, 'dist'),
    open: true,
    clientLogLevel: 'silent',
    port: 9000,
    hot: true
  },
  module: {
    rules: [
      {
        test: /\.(jsx|js)$/,
        include: path.resolve(__dirname, 'src'),
        exclude: /node_modules/,
        use: [{
          loader: 'babel-loader',
          options: {
            presets: [
              ['@babel/preset-env', {
                "targets": "defaults" 
              }],
              '@babel/preset-react'
            ]
          }
        }]
      }
    ]
  }
}
```

## Index.html

```HTML
<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <title>Complete React Webpack Configuration</title>
  <meta name="viewport" content="width=device-width,initial-scale=1">
</head>

<body>
  <div id="app"></div>
  <script src="./bundle.js" async defer></script>
</body>

</html>
```

## Index.js

```JavaScript
import React from 'react'
import ReactDOM from 'react-dom'
import App from './components/App'

// Opt-in to Webpack hot module replacement
if (module.hot) module.hot.accept()

ReactDOM.render(
  <App />,
  document.getElementById('app')
)
```

## package.json

- Add the following rules:
  - The develop script
```JSON
{
  "name": "react-webpack-config",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "develop": "webpack serve",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  //...
}
```

## App.js

```JavaScript
import React from 'react'

const App = ({ props }) => (
  <div>
    Webpack HMR Example
  </div>
)

export default App
```