# Webpack Tutorial

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
```

## Inside webpack.config.js

```JavaScript
const path = require('path')

module.exports = {
  entry: path.resolve(__dirname, 'src', 'index.js'),
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  }
}
```

## Inside index.js

```JavaScript
(() => {
  console.log('webpack worked')
})()
```

- Check webpack is working:
```
npx webpack --config webpack.config.js
```
- You should see in `bundle.js` => `console.log('webpack worked')`.

## Update index.html

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

- Open in web browser -> you should see it working.

## Now install babel

```
npm i @babel/core @babel/preset-env @babel/preset-react babel-loader
```

## Update webpack.config.js

```JavaScript
const path = require('path')

module.exports = {
  entry: path.resolve(__dirname, 'src', 'index.js'),
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
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

- See which browsers you can run on:
```
npx browserslist 'defaults'
```

### Now we can import react into src/index.js

```JavaScript
import React from 'react'
import ReactDOM from 'react-dom'

ReactDOM.render(
  React.createElement('div', null, `Hello React`),
  document.getElementById('app')
)
```

- Check react has compiled correctly
```
npx webpack --config webpack.config.js
```
- Should see 'Hello React' in index.html

## Configure webpack dev server

```
npm i webpack-dev-server --save-dev
```

- webpack.config.js:

```JavaScript
const path = require('path')

module.exports = {
  entry: path.resolve(__dirname, 'src', 'index.js'),
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  },
  devServer: {
    contentBase: path.resolve(__dirname, 'dist'),
    open: true,
    clientLogLevel: 'silent',
    port: 9000
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

- Add to `package.json`:

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

### Start the server

```
npm run develop
```

## Webpack Dev Server Hot Module Replacement (HMR)

```
touch src/components/App.js
```

- Add to `App.js`

```JavaScript
import React from 'react'

const App = ({ props }) => (
  <div>
    Webpack HMR Example
  </div>
)

export default App
```


- Add to `index.js`
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

- Now update `webpack.config.js`

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

- Restart your webpack server:
```
npm run develop
```

- You should now be able to modify CSS in the browser, then edit App.js, causing a hot re-render without losing the CSS changes in the browser.

## Configuring MiniCssExtractPlugin to write CSS Files

```
npm i mini-css-extract-plugin css-loader
```

- Update webpack config:
```JavaScript
const path = require('path')
const webpack = require('webpack')
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = env => {
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
                importLoaders: 0 
              }
            }
          ]
        }
      ]
    }
  }
}
```

- Update `package.json`:
```JSON
//...
"scripts": {
  "develop": "webpack serve --env NODE_ENV=development --mode development",
},
//...
```

- Create a CSS file:

```
touch src/index.css
```
- Import `index.css` into `index.js`
```JavaScript
import './index.css'
```

- Update `index.html`

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

## Adding PostCSS and PostCSS plugins to your Webpack Config

```
npm i postcss-loader
```

- Update webpack config:

```JavaScript
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
```

```
npm install autoprefixer
touch postcss.config.js
```

- Add to `postcss.config.js`
```JavaScript
module.exports = {
  plugins: [
    require('autoprefixer')
  ]
}
```

- Update `index.css`:
```CSS
body {
  background:#eeeeee; 
}

::placeholder {
  color: gray;
}
```

## Adding ESLint to your JavaScript and JSX files

```
npm i eslint-loader
npm i --save-dev babel-eslint
npx install-peerdeps --dev eslint-config-airbnb
touch .eslintrc
```

- In `.eslintrc`
```
{
  "parser": "babel-eslint",
  "extends": ["airbnb"],
  "ignorePatterns": ["webpack.config.js", "postcss.config.js"],
  "rules": {
    "react/jsx-filename-extension": [1, { "extensions": [".js", ".jsx"] }],
    "no-unused-vars": 0,
    "react/prop-types": 0
  }
}
```

- Then update webpack config:

```JavaScript
//...
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
  }
  //...
```

- Add the following rule to `index.js`:
```JavaScript
/* eslint-disable no-undef */
```

- Now we are done!
