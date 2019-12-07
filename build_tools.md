# Build Tools

Build tools will manage all our assets so that we don’t have to by combining them all into a single file (or sometimes a few files). 
We create a set of rules for the build tool to follow, telling it specifically how we want each type of asset handled, and then it follows our rules, takes all the assets and bundles them into a single large file, which has everything loading in the correct order and is much easier for us to deal with. 

Typically, files with names like bundle or main are the result of a build tool combining many assets into one.

There are many build tool options, the most common are:
- Grunt
- Gulp
- Webpack (more difficult but more professional)

Rules for a build tool are written into config files. See below 
Webpack example of config file.

```javascript
module.exports = {
  entry: './src/index.js',
  output: {
    path: path.join(__dirname, 'dist'),
    filename: 'main.js',
  },
  module: {
       rules: [
          {
                test: /\.js$/,
                exclude: /node_modules/,
                loader: "babel-loader",
          },
          {
                test: /\.html$/,
                use: [{ loader: "html-loader"}],
                },
                {
                    test: /\.scss$/,
                    use: [ 'style-loader', 'css-loader', 'sass-loader' ]
                }
       ]
 },
  plugins: [
    new HtmlWebPackPlugin({
           template: "./src/html/index.html",
           filename: "./index.html",
    })
  ]
}
```

One thing to notice is that Webpack is entirely built with js.

The output of webpack is normally a bundle.js file. Basically takes all the files and libraries that make up your website and combine into one giant file. This file is organized in a way that all the dependencies are loaded in a specific order.

## What webpack can do
Among others webpack can:
- Run Typescript files 
- Configure a development server
- Encode in Base64 image files
- Add dynamic css references to html
- Run a linter on your code
