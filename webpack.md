# Webpack

To get started with webpack you first need to install the cli. Run the following command:
```sh
$ npm i webpack webpack-cli
```

The above command will add webpack to your package.json file.

Since webpack 4, the package come with a set of default configuration. For simple app, you don't even need to create a configuration file, you can use the default settings.

Webpack, differently from other build tools, creates a dependencies tree. This file basically check all the required files and methods in our application files, and compose them in a tree.

This way webpack has knolwedge of the whole structure of our application.
In order to create the dependencies tree, webpack needs to know where to start from.

The starting point is called `entry` point.

The default entry point that webpack will set goes to `index.js`.

## The config file
To set up any customized configs that are outside of the default Webpack setting created in Webpack4, we can use a file called `webpack.config.js`.

The basic setup of the config file will look similar to the below and it should be placed in the root of the project:

```javascript
const path = require('path')
const webpack = require('webpack')

module.exports = {
    entry: './src/client/index.js'
}
```

After configuring Webpack, you need to add a script in your package.json file in order to create the build.

```json
"build": "webpack"
```

After running `npm run build` webpack will create a `dist` folder and inside the `main.js` file with all the content needed for the application to run.

Hence, the output of webpack is the **dist** folder.

## Babel
When you create your own custom entry point, the output of Webpack will have no reference at all to all your front-end javascript files.
We need then to import those files in the index.js file, in order that the **main.js** file, outputted by webpack, will actually contain all the javascript we have written.

If we were on the backend, in order to import other js files into another file, we would normally use the `require` statment, however, this won't work in the browser.

To address this problem, we need to install another configuration tool, called **Babel**.

Babel allows us to write ES6 syntax in any javascript file (frontend) and then he will take care of converting everything in syntax that is understood by all the browser.

### Install babel
To install babel you need to run the following command:
```sh
$ npm i -D @babel/core @babel/preset-env babel-loader
```
The `-D` is short for development. That means that all the packages installed with this flag are only available during the development process.

### Setup babel
Babel, similar to webpack, needs a configuration file. In the root of your project create a file called `.babelrc`

Inside this file add the following preset:
```json
{'presets': ['@babel/preset-env']}
```

For the list of all the available configurations for babel, please look at the documentation.

After installing and setting up babel, you can make use of the ES6 syntax also in the frontend files. For example, you can import all the javascript files needed for the application, into the index.js webpack entry point with the following syntax:
```javascript
import { handleSubmit } from './js/formHandler'
import { nameChecker } from './js/nameChecker'
```
However, keep in mind, that in order to import a file, you need to export the content of the function of that file.

### Configure webpack in order to use babel
Now that we have babel, we need to tell webpack to use it. Thus, we need to add another piece to our `webpack.config.js` file, we need to add a module key and configure the rules. Our webpack config file will look like this:
```javascript
const path = require('path')
const webpack = require('webpack')

module.exports = {
    entry: './src/client/index.js',
    module: {
        rules: [
            {
                test: '/\.js$/',
                exclude: /node_modules/,
                loader: "babel-loader"
            }
        ]
    }
}
```

The above rule is telling webpack to run every single file that ends with `.js` through the `babel-loader` excluing the files in the `node_modules` directory.
Natively webpack understands only json and js files. Loaders allow webpack to process other types of files.

A loder configuration normally includes a regex matcher, that specifies what types of files it will run on and the optional exclude pair.

After configuring babel, the next run of `npm run build` will include all the js files imported in the index.js file.

## Plugins
While loaders are used to transform certain types of modules, plugins can be leveraged to perform a wider range of tasks like bundle optimization, asset management and injection of environment variables.
Plugins can automatically add references to an html file.

A very common plugin is the html-webpack-plugin, which allows to dynamically create references to external files (js, css) in hour main html file.

```sh
$ npm i -D html-webpack-plugin
```

After adding a configuring the plugin, the webpack configuration file should look simalar to this:

```javascript
const path = require('path')
const webpack = require('webpack')
const HtmlWebPackPlugin = require('html-webpack-plugin')

module.exports = {
    entry: './src/client/index.js',
    module: {
        rules: [
            {
                test: '/\.js$/',
                exclude: /node_modules/,
                use: ["babel-loader", "eslint-loader"]
            }
        ]
    },
    plugins: [
        new HtmlWebPackPlugin({
            template: './src/client/views/index.html',
            filename: "./index.html"
        })
    ]
}
```

The html-webpack-plugin creates a copy of the html file in the dist file (after running npm run build) and it automatically links the outputted main.js file.

**Remember to update the server in order to point to the dist folder when serving the files**

## Mode
Mode tights in with environments. Developers refer to the various "states" of a website as environment.
Most common environments are 
- Development
- Testing
- Staging
- Production

Each environment has its own peculiarities. For instance there is a tool called `sass` that developers use to write css with some friendly sytax and features. However, saas can't run on a server, hence all the sass files need to be converted (through a transpiler) in css. By utilizing buil toos, it is possible to write code that is convenient for development team without sacrificing speed on the server.

Webpack allows you to create different environments such as dev environment (MODE in webpack) that run totally different loaders and plugins than the production mode.

You can set the MODE in two different ways:
- Through the cli
- Inside the webpack.config.js file

A common strategy is to create different webpack config files for each environment.

For instance in the root of your project you could have:
```sh
webpack.dev.js
webpack.prod.js
```
Now that we have different configuration file, we need to change the build command in order to determine which configuration file we want based on the MODE.

In our example, we'll add two build commands, one for dev and one for production. The scripts section in package.json file will have:
```json
"build-dev": "webpack --config webpack.dev.js",
"build-prod": "webpack --config webpack.prod.js"
```

## Webpack dev-server
webpack-dev-server allows to create a server for the development mode that run files and updates immediately.
It is what allows Create React App to have the hot reloading feature.

Out of the box weback provides the ability to watch certain files.
You can run this directly by adding a `watch` flag into the build-dev command.
```json
"build-dev": "webpack --config webpack.dev.js --watch --info-verbosity verbose",
```

With the watch flag, webpack will act as nodemon and recompile the dist everytime there is a change to a file.
However, this won't refresh the browser to reflect the updates.

In order to automatically reflect the changes in the browser, webpack has the webpack-dev-server which allows the hot reloading feature.

```sh
$ npm i -D webpack-dev-server
```

After installing the webpack-dev-server we need to change the build-dev command in order to use the new module.

```json
"build-dev": "webpack-dev-server --config webpack.dev.js --open",
"build-prod": "webpack --config webpack.prod.js"
```

The `--open` flag will open a new window browser when we start the dev-server.

## Clean the dist folder
Before running a build command, we need to manually delete the dist folder. We could edit the script in order to automatically do this step before the building.

The script would look something like the below:
```json
"build-dev": "rm -rf dist && webpack-dev-server --config webpack.dev.js --open",
```

However, this is not very efficient, because it removes everything even if the code is 99% the same as the new build.

To improve the efficiency there is a plugin called **Clean**.

```sh
$ npm i -D clean-webpack-plugin
```

After installing we need to require and add it to the plugins array configuration.

After adding the clean plugin, the dev configuration file should look similar to the below:
```javascript
const path = require('path')
const webpack = require('webpack') // to access built-in plugins
const HtmlWebpackPlugin = require('html-webpack-plugin')
const { CleanWebpackPlugin } = require('clean-webpack-plugin')

module.exports = {
    mode: 'production',
    entry: './src/client/index.js',
    module: {
        rules: [
            {
                test: '/\.js$/',
                exclude: /node_modules/,
                use: ["babel-loader", "eslint-loader"]
            }
        ]
    },
    plugins: [
        new CleanWebpackPlugin(),
        new HtmlWebpackPlugin({
            template: './src/client/views/index.html',
            filename: "./index.html"
        }),
    ],
    output: {
        filename: '[name].[contenthash].js',
        path: path.resolve(__dirname, 'dist')
    }
}
```

## Commongs Chunk plugin
To improve performance by keeping the modularity in the project there is a very cool plugin called CommonsChunk.
This plugin checks how many times the same file is requested in different other files and put it in a "common pool" so the file is loaded only once.

You can read more about this plugin [here](https://www.jonathancreamer.com/advanced-webpack-part-1-the-commonschunk-plugin/).

## Webpack output
Leaving all the default settings, when you have an application with moderate/complex structure, will create some problems. For instance the javascript won't work as expected. This is because webpack tries to keep the scope very very defined, therefore, things such as event listener, won't have access to the global scope.
To make sure that each containerized function talk to each other we need to configure the output in the configuration file.

An example of output configuration looks like the below;
```javascript
modeule.export = {
    mode: 'production',
    devtool: 'source-map',
    entry: './src/client/index.js',
    output: {
        libraryTarget: 'var',
        library: 'Client'
    },
    module {

    },
    ...
}
```

The important thing to notice in the above code is the output section.
This basically creates our own library, called in this case `Client` (you can name it as you want), which contains all our javascript.

Now, for your javascript to endup into the Client library, you need to export it first. Hence, in your index.js file you need to export all the imported javascript files.

```javascript
import { handleSubmit } from './js/formHandler'
import { nameChecker } from './js/nameChecker'
import './styles/resets.scss'
import './styles/base.scss'
import './styles/form.scss'
import './styles/footer.scss'
import './styles/header.scss'

export {
    nameChecker,
    handleSubmit
}
```

To make use of the functions included into the `Client` library, you need to target the Client object first.
See below example.

```html
<!-- Without client library -->
<form class="" onsubmit="return handleSubmit(event)">
    <input id="name" type="text" name="input" value="name" placeholder="Name">
    <input type="submit" name="" value="submit" onsubmit="return handleSubmit(event)">
</form>

<!-- With client library -->
<form class="" onsubmit="return Client.handleSubmit(event)">
    <input id="name" type="text" name="input" value="name" placeholder="Name">
    <input type="submit" name="" value="submit" onsubmit="return Client.handleSubmit(event)">
</form>
```

Same applies for any custom function referenced in javascript files. In the below example we're using the `checkForName()` function coming from an external file. In order to access that function, we need to use the `Client` library which has a global scope.

```javascript
function handleSubmit(event) {
    event.preventDefault()

    // check what text was put into the form field
    let formText = document.getElementById('name').value
    Client.checkForName(formText)

    console.log("::: Form Submitted :::")
}

export { handleSubmit }
```

## Webpack and Express
One thing to keep in mind is that if webpack is running on port 8080, the express server cannot run on the same port.
A common strategy is to set the express server port to be 8081.

Also, another important thing to keep in mind is that webpack is great for single page application, because if you need to reference different root URI, it starts to get tricky.

## Build for production
When selecting `production` mode in the webpack config file, webpack will minfy all our javascript by default, but it won't touch the stylesheets, since they're inserted in all the javascript file and then applied inline.

Inline styles are slow and to be most efficient, we need to move all the styles in a dedicated file and minified it.
To achieve that we can use the `mini-css-extract-plugin`.

```sh
$ npm install -D mini-css-extract-plugin --save
```

The mini css plugin provides a loader that we can use in the roles section in our config file. This plugin will insert all the css in a `main.css` file.
However, the output is not yet optimized (minified).

In order to do the optimization, we need other two plugins:
- Terser Plugin
- Optimize CSS Assets Plugins.

```sh
$ npm i -D optimize-css-assets-webpack-plugin terser-webpack-plugin
```

Your final production webpack configuration file should look similar to this:

```javascript
const path = require('path')
const webpack = require('webpack')
const HtmlWebPackPlugin = require('html-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin')
const TerserPlugin = require('terser-webpack-plugin')

module.exports = {
    mode: 'production',
    devtool: 'source-map',
    entry: './src/client/index.js',
    output: {
        libraryTarget: 'var',
        library: 'Client'
    },
    optimization: {
        minimizer: [new TerserPlugin({}), new OptimizeCSSAssetsPlugin({})]
    },
    module: {
        rules: [
            {
                test: '/\.js$/',
                exclude: /node_modules/,
                use: ["babel-loader", "eslint-loader"]
            },
            {
                test: /\.scss$/,
                use: [ MiniCssExtractPlugin.loader, 'css-loader',  'sass-loader']
            }
        ]
    },
    plugins: [
        new HtmlWebPackPlugin({
            template: './src/client/views/index.html',
            filename: './index.html'
        }),
        new MiniCssExtractPlugin({filename: '[name].css'})
    ]
}
```

## The .map file

Map files keep track of which source files the code in your bundled file comes from. This is incredibbly handy when debugging. Without a map file, you would get an error that says it is coming from like 1783 of bundle.js - which isn’t very helpful, but with a source map turned on it would tell you the file name and line where the error is occuring.


## Webpack-merge
Even if we have two separate configuration files `development` and `production` there are still few thing in common on the two.
In order to keep our code DRY, we can use an useful plugin called `webpack-merge`.

```sh
$ npm install --save-dev webpack-merge
```

With webpack-merge, we can now create a new configuration file called `webpack.common.js`. In this file we'll include all the setup common to dev and prod.

In order to use the `common` file in the development and production configuration files, we need to require it first and use the `merge()` function.

Example of common file:
```js
const path = require('path');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
const HtmlWebpackPlugin = require('html-webpack-plugin');
module.exports = {
  entry: {
    app: './src/index.js',
    devtool: 'source-map',
  },
  plugins: [
    // new CleanWebpackPlugin(['dist/*']) for < v2 versions of CleanWebpackPlugin
    new CleanWebpackPlugin(),
    new HtmlWebpackPlugin({
      title: 'Production',
     }),
   ],
   output: {
     filename: '[name].bundle.js',
     path: path.resolve(__dirname, 'dist'),
   },
};
```
<br>

`webpack.dev.js`
```js
const merge = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'development',
  devtool: 'inline-source-map',
  devServer: {
    contentBase: './dist',
  },
});
```

<br>

`webpack.prod.js`

```js
const merge = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'production',
});
```

## Advanced reading
- [Code splitting/lazy loading](https://webpack.js.org/guides/code-splitting/)
- [Tree shaking or dead code elimination](https://webpack.js.org/guides/tree-shaking/)
- [List of plugins and tutorials](https://github.com/markerikson/react-redux-links/blob/master/webpack-advanced-techniques.md)