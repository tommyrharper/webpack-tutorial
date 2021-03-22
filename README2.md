# The shortcut path

## Start your server

```
npm run develop // to run dev server
npm i webpack-dev-server --save-dev // to build bundle.js
npx webpack --help // view available webpack commands
touch postcss.config.js
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
npm i mini-css-extract-plugin css-loader
touch src/index.css
npm i postcss-loader
npm install autoprefixer
touch postcss.config.js
npm i eslint-loader
npm i --save-dev babel-eslint
npx install-peerdeps --dev eslint-config-airbnb
touch .eslintrc
```

## Webpack.config.js

```JavaScript
const path = require('path')
const webpack = require('webpack')
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = env => {
  const devMode = env.NODE_ENV !== 'production';

  return {
    plugins: [
      new MiniCssExtractPlugin({
        filename: '[name].bundle.css',
        chunkFilename: '[id].css'
      }),
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
      historyApiFallback: true,
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
          }, {
            loader: 'eslint-loader',
            options: {
              fix: true
            }
          }]
        },
        {
          test: /\.css$/i,
          include: path.resolve(__dirname, 'src'),
          exclude: /node_modules/,
          use: [
            {
              loader: MiniCssExtractPlugin.loader,
            },
            {
              loader: 'css-loader',
              options: {
                importLoaders: 1 
              }
            },
            'postcss-loader'
          ]
        }
      ]
    }
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
  <link rel="stylesheet" type="text/css" href="/main.bundle.css">
  <meta name="viewport" content="width=device-width,initial-scale=1">
</head>

<body>
  <div id="app"></div>
  <script src="/bundle.js" async defer></script>
</body>

</html>
```

## Index.js

```JavaScript
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/App';
import './index.css';

// Opt-in to Webpack hot module replacement
if (module.hot) module.hot.accept();

/* eslint-disable no-undef */
ReactDOM.render(
  <App />,
  document.getElementById('app'),
);

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
    "develop": "webpack serve --env NODE_ENV=development --mode development",
    "test": "jest"
  },
  //...
}
```

## App.js

```JavaScript
import React from 'react';

const App = ({ props }) => (
  <div>
    Webpack HMR Example
  </div>
);

export default App;
```

## index.css

```CSS
body {
  background:#eeeeee; 
}

::placeholder {
  color: gray;
}
```

## postcss.config.js

```JavaScript
module.exports = {
  plugins: [
    require('autoprefixer')
  ]
}
```

## ESLint RC

```
{
  "parser": "babel-eslint",
  "extends": ["airbnb"],
  "ignorePatterns": ["webpack.config.js", "postcss.config.js"],
  "rules": {
    "react/jsx-filename-extension": [1, { "extensions": [".js", ".jsx"] }],
    "no-unused-vars": 0,
    "react/prop-types": 0,
    "no-console": 0,
    "quotes": 0,
    "camelcase": 0,
    "comma-dangle": 0,
    "no-plusplus": 0,
    "no-restricted-syntax": 0
  },
  "env": {
    "browser": true,
    "jest": true
  }
}
```

## Further additions

```
npm install --save styled-components
// add jest
mkdir src/__tests__
touch src/__tests__/App.test.js
npm install --save-dev jest
// To test React components
npm install --save-dev babel-jest babel-polyfill
npm install --save-dev jest babel-jest babel-preset-es2015 babel-preset-react react-test-renderer
npm i --save-dev enzyme
touch .babelrc
npm install babel-core@7.0.0-bridge.0 --save-dev
npm install enzyme-adapter-react-16
touch ./src/setupTests.js
// optional
npm install --save @testing-library/react @testing-library/jest-dom
// testing fetch
npm install react-test-renderer fetch-mock @testing-library/react-hooks whatwg-fetch
npm install node-fetch
// allow async hooks -> install this and update babel config plugins
npm install @babel/plugin-transform-runtime
```

- Update `package.json`:
```JSON
{
    ...,
    "jest": {
        "setupFilesAfterEnv": ["./src/setupTests.js"]
    }
}
```

## App.test.js

```JavaScript
import React from 'react';
import ReactDOM from 'react-dom';
import { shallow, configure } from 'enzyme';
import App from '../components/App';

const sum = (a, b) => a + b;

it('sums numbers', () => {
  expect(sum(1, 2)).toEqual(3);
  expect(sum(2, 2)).toEqual(4);
});

it('renders without crashing', () => {
  const div = document.createElement('div');
  ReactDOM.render(<App />, div);
  shallow(<App />);
});
```

## .babelrc

```
{
  "presets": [
    "@babel/preset-env",
    "@babel/preset-react"
  ],
  "plugins": [
    "@babel/plugin-transform-runtime"
  ]
}
```

## setupTests.js

```JavaScript
// Add this line for fetch testing
import 'regenerator-runtime/runtime';
// Standard
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() });
```