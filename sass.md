# SASS

SAAS is a preprocessor that lets you use features that don't exists in CSS:
- variables
- nesting
- mixins
- inheritance
- etc

In Node.js you can install Sass using npm:
```sh
$ npm install -g sass
```

A .scss file cannot be read from the browser, therefore a Saas file needs to be converted into a css file that the browser can understand.

You can save a Saas as a normal CSS directly from the terminal running the following command:
```sh
$ sass input.scss output.css
```

However, if you're using a build tool, such as webpack, you can use a builder that converts scss into css.

```sh
$ npm i -D style-loader node-sass css-loader sass-loader
```

Then you need to add the rule for sass in the webpack configuration file.

```json
{
    test: /\.scss$/,
    use: [ 'style-loader', 'css-loader', 'sass-loader' ]
}
```

## Variables
Variables in Saas act as any other variable in other programming languages. They store information that can be reused.

SCSS INPUT
```scss
$font-stack:    Helvetica, sans-serif;
$primary-color: #333;

body {
  font: 100% $font-stack;
  color: $primary-color;
}
```

CSS output
```css
body {
  font: 100% Helvetica, sans-serif;
  color: #333;
}
```
___

## Nesting
scss
```scss
nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  li { display: inline-block; }

  a {
    display: block;
    padding: 6px 12px;
    text-decoration: none;
  }
}
```
CSS output
```css
nav ul {
  margin: 0;
  padding: 0;
  list-style: none;
}
nav li {
  display: inline-block;
}
nav a {
  display: block;
  padding: 6px 12px;
  text-decoration: none;
}
```

___
## Partials
You can create partial Sass files that can be imported into other files. Partial files always begin with underscore, `_partial.scss`.
You can include it in another scss file using the `@use` rule.

SCSS partial
```scss
// _base.scss
$font-stack:    Helvetica, sans-serif;
$primary-color: #333;

body {
  font: 100% $font-stack;
  color: $primary-color;
}
```
SCSS file that uses _base.scss
```scss
// styles.scss
@import 'base';

.inverse {
  background-color: $primary-color;
  color: white;
}
```

CSS Output
```css
body {
  font: 100% Helvetica, sans-serif;
  color: #333;
}

.inverse {
  background-color: #333;
  color: white;
}

```

___
## Mixins
Mixins are used to target all the specific vendor prefixes (webkit, ms, mozilla, etc).

SCSS input
```scss
@mixin transform($property) {
  -webkit-transform: $property;
  -ms-transform: $property;
  transform: $property;
}
.box { @include transform(rotate(30deg)); }
```

CSS Output
```css
.box {
  -webkit-transform: rotate(30deg);
  -ms-transform: rotate(30deg);
  transform: rotate(30deg);
}

```

___
## Extend/Inheritance
This features let you share a set of CSS properties from one selector to another.

SCSS Input
```scss
/* This CSS will print because %message-shared is extended. */
%message-shared {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}

.message {
  @extend %message-shared;
}

.success {
  @extend %message-shared;
  border-color: green;
}

.error {
  @extend %message-shared;
  border-color: red;
}
```

CSS Output
```css
/* This CSS will print because %message-shared is extended. */
.message, .success, .error, .warning {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}

.success {
  border-color: green;
}

.error {
  border-color: red;
}
```

___
## Operators
Sass lets you use math operators in your file that will be translated into % in the css output.

SCSS Input
```scss
.container {
  width: 100%;
}

article[role="main"] {
  float: left;
  width: 600px / 960px * 100%;
}
```

CSS Output
```css
.container {
  width: 100%;
}

article[role="main"] {
  float: left;
  width: 62.5%;
}
```

___
## Ampersand (&)
The Ampersand is used to target the parent selector in nested sass properties. It is commonly used to add properties to the same selector. For instance you can nest the hover, focus, visited, etc. properties of a button in a button itself. To do so, you need to add the ampersand, otherwise we'll not achieve what we are looking for.

```scss
button.awesome {
    border-radius: 3px;
    background: blue;
    color: white;

    &:hover {
        background: aqua;
    }

    &:visited {
        background: yellow;
    }
}
```