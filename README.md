# node-sass

<table>
  <tr>
    <td>
      <img width="100%" alt="Sass logo" src="https://rawgit.com/sass/node-sass/master/media/logo.svg" />
    </td>
    <td valign="bottom" align="right">
      <a href="https://nodei.co/npm/node-sass/">
        <img width="100%" src="https://nodei.co/npm/node-sass.png?downloads=true&downloadRank=true&stars=true">
      </a>
    </td>
  </tr>
</table>

[![Build Status](https://travis-ci.org/sass/node-sass.svg?branch=master&style=flat)](https://travis-ci.org/sass/node-sass)
[![Build status](https://ci.appveyor.com/api/projects/status/22mjbk59kvd55m9y/branch/master)](https://ci.appveyor.com/project/sass/node-sass/branch/master)
[![npm version](https://badge.fury.io/js/node-sass.svg)](http://badge.fury.io/js/node-sass)
[![Dependency Status](https://david-dm.org/sass/node-sass.svg?theme=shields.io)](https://david-dm.org/sass/node-sass)
[![devDependency Status](https://david-dm.org/sass/node-sass/dev-status.svg?theme=shields.io)](https://david-dm.org/sass/node-sass#info=devDependencies)
[![Coverage Status](https://coveralls.io/repos/sass/node-sass/badge.svg?branch=master)](https://coveralls.io/r/sass/node-sass?branch=master)
[![Inline docs](http://inch-ci.org/github/sass/node-sass.svg?branch=master)](http://inch-ci.org/github/sass/node-sass)
[![Gitter chat](http://img.shields.io/badge/gitter-sass/node--sass-brightgreen.svg)](https://gitter.im/sass/node-sass)

Node-sass is a library that provides binding for Node.js to [libsass], the C version of the popular stylesheet preprocessor, Sass.

It allows you to natively compile .scss files to css at incredible speed and automatically via a connect middleware.

Find it on npm: <https://npmjs.org/package/node-sass>

Follow @nodesass on twitter for release updates: https://twitter.com/nodesass

## Install

```
npm install node-sass
```

Some users have reported issues installing on Ubuntu due to `node` being registered to another package. [Follow the official NodeJS docs](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager) to install NodeJS so that `#!/usr/bin/env node` correctly resolved.

Compiling versions 0.9.4 and above on Windows machines requires [Visual Studio 2013 WD](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-desktop). If you have multiple VS versions, use ```npm install``` with the ```--msvs_version=2013``` flag also use this flag when rebuilding the module with node-gyp or nw-gyp.

## Usage

```javascript
var sass = require('node-sass');
sass.render({
  file: scss_filename,
  [, options..]
}, function(err, result) { /*...*/ });
// OR
var result = sass.renderSync({
  data: scss_content
  [, options..]
});
```

## Options
### file
Type: `String | null`
Default: `null`
**Special**: `file` or `data` must be specified

Path to a file for [libsass] to render.

### data
Type: `String | null`
Default: `null`
**Special**: `file` or `data` must be specified

A string to pass to [libsass] to render. It is recommended that you use `includePaths` in conjunction with this so that [libsass] can find files when using the `@import` directive.

### importer (>= v2.0.0)
Type: `Function` signature `function(url, prev, done)`
Default: `undefined`

Function Parameters and Information:
* `url (String)` - the path in import **as-is**, which [libsass] encountered
* `prev (String)` - the previously resolved path
* `done (Function)` - a callback function to invoke on async completion, takes an object literal containing
  * `file (String)` - an alternate path for [libsass] to use **OR**
  * `contents (String)` - the imported contents (for example, read from memory or the file system)

Handles when [libsass] encounters the `@import` directive. A custom importer allows extension of the [libsass] engine in both a synchronous and asynchronous manner. In both cases, the goal is to either `return` or call `done()` with an object literal. Depending on the value of the object literal, one of two things will happen.

When returning or calling `done()` with `{ file: "String" }`, the new file path will be assumed for the `@import`. It's recommended to be mindful of the value of `prev` in instances where relative path resolution may be required.

When returning or calling `done()` with `{ contents: "String" }`, the string value will be used as if the file was read in through an external source.

`this` refers to a contextual scope for the immediate run of `sass.render` or `sass.renderSync`

### includePaths
Type: `Array<String>`
Default: `[]`

An array of paths that [libsass] can look in to attempt to resolve your `@import` declarations. When using `data`, it is recommended that you use this.

### indentedSyntax
Type: `Boolean`
Default: `false`

`true` values enable [Sass Indented Syntax](http://sass-lang.com/documentation/file.INDENTED_SYNTAX.html) for parsing the data string or file.

### omitSourceMapUrl
Type: `Boolean`
Default: `false`
**Special:** When using this, you should also specify `outFile` to avoid unexpected behavior.

`true` values disable the inclusion of source map information in the output file.

### outFile
Type: `String | null`
Default: `null`
**Special:** Required when `sourceMap` is a truthy value

Specify the intended location of the output file. Strongly recommended when outputting source maps so that they can properly refer back to their intended files.

### outputStyle
Type: `String`
Default: `nested`
Values: `nested`, `compressed`

Determines the output format of the final CSS style. (`'expanded'` and `'compact'` are not currently supported by [libsass], but are planned in a future version.)

### precision
Type: `Integer`
Default: `5`

Used to determine how many digits after the decimal will be allowed. For instance, if you had a decimal number of `1.23456789` and a precision of `5`, the result will be `1.23457` in the final CSS.

### sourceComments
Type: `Boolean`
Default: `false`

`true` enables additional debugging information in the output file as CSS comments

### sourceMap
Type: `Boolean | String | undefined`
Default: `undefined`
**Special:** Setting the `sourceMap` option requires also setting the `outFile` option

Enables the outputting of a source map during `render` and `renderSync`. When `sourceMap === true`, the value of `outFile` is used as the target output location for the source map. When `typeof sourceMap === "String"`, the value of `sourceMap` will be used as the writing location for the file.

### sourceMapEmbed
Type: `Boolean`
Default: `false`

`true` embeds the source map as a data URI

### sourceMapContents
Type: `Boolean`
Default: `false`

`true` includes the `contents` in the source map information

## `render` Callback (>= v3.0.0)
node-sass supports standard node style asynchronous callbacks with the signature of `function(err, result)`. In error conditions, the `error` argument is populated with the error object. In success conditions, the `result` object is populated with an object describing the result of the render call.

### Error Object
* `message` (String) - The error message.
* `line` (Number) - The line number of error.
* `column` (Number) - The column number of error.
* `status` (Number) - The status code.
* `file` (String) - The filename of error. In case `file` option was not set (in favour of `data`), this will reflect the value `stdin`.

### Result Object
* `css` (Buffer) - The compiled CSS. Write this to a file, or serve it out as needed.
* `map` (Buffer) - The source map
* `stats` (Object) - An object containing information about the compile. It contains the following keys:
  * `entry` (String) - The path to the scss file, or `data` if the source was not a file
  * `start` (Number) - Date.now() before the compilation
  * `end` (Number) - Date.now() after the compilation
  * `duration` (Number) - *end* - *start*
  * `includedFiles` (Array) - Absolute paths to all related scss files in no particular order.

### Examples

```javascript
var sass = require('node-sass');
sass.render({
  file: '/path/to/myFile.scss',
  data: 'body{background:blue; a{color:black;}}',
  importer: function(url, prev, done) {
    // url is the path in import as is, which libsass encountered.
    // prev is the previously resolved path.
    // done is an optional callback, either consume it or return value synchronously.
    // this.options contains this options hash, this.callback contains the node-style callback
    someAsyncFunction(url, prev, function(result){
      done({
        file: result.path, // only one of them is required, see section Sepcial Behaviours.
        contents: result.data
      });
    });
    // OR
    var result = someSyncFunction(url, prev);
    return {file: result.path, contents: result.data};
  },
  includePaths: [ 'lib/', 'mod/' ],
  outputStyle: 'compressed'
}, function(error, result) { // >= v3.0.0
  if (error) {
    console.log(error.status); // use "code" <= v3.0.0
    console.log(error.column);
    console.log(error.message);
    console.log(error.line);
  }
  else {
    console.log(result.css.toString());
    console.log(result.stats);
    console.log(result.map.toString()); // or console.log(JSON.stringify(result.map));
  }
});
// OR
var result = sass.renderSync({
  file: '/path/to/file.scss',
  data: 'body{background:blue; a{color:black;}}',
  outputStyle: 'compressed',
  outFile: '/to/my/output.css',
  sourceMap: true, // or an absolute or relative (to outFile) path
  importer: function(url, prev, done) {
    // url is the path in import as is, which libsass encountered.
    // prev is the previously resolved path.
    // done is an optional callback, either consume it or return value synchronously.
    // this.options contains this options hash
    someAsyncFunction(url, prev, function(result){
      done({
        file: result.path, // only one of them is required, see section Sepcial Behaviours.
        contents: result.data
      });
    });
    // OR
    var result = someSyncFunction(url, prev);
    return {file: result.path, contents: result.data};
  },
}));

console.log(result.css);
console.log(result.map);
console.log(result.stats);
```

### Special behaviours

* In the case that both `file` and `data` options are set, node-sass will give precedence to `data` and use `file` to calculate paths in sourcemaps.

### Version information (>= v2.0.0)

Both `node-sass` and `libsass` version info is now present in `package.json` and is exposed via `info` method:

```javascript
var sass = require('node-sass');

console.log(sass.info);

/*
  it will output something like:

  node-sass       2.0.1   (Wrapper)       [JavaScript]
  libsass         3.1.0   (Sass Compiler) [C/C++]
*/
```

## Integrations

Listing of community uses of node-sass in build tools and frameworks.

### Brackets extension

[@jasonsanjose](https://github.com/jasonsanjose) has created a [Brackets](http://brackets.io) extension based on node-sass: <https://github.com/jasonsanjose/brackets-sass>. When editing Sass files, the extension compiles changes on save. The extension also integrates with Live Preview to show Sass changes in the browser without saving or compiling.

### Brunch plugin

[Brunch](http://brunch.io)'s official sass plugin uses node-sass by default, and automatically falls back to ruby if use of Compass is detected: <https://github.com/brunch/sass-brunch>

### Connect/Express middleware

Recompile `.scss` files automatically for connect and express based http servers.

This functionality has been moved to [`node-sass-middleware`](https://github.com/sass/node-sass-middleware) in node-sass v1.0.0

### DocPad Plugin

[@jking90](https://github.com/jking90) wrote a [DocPad](http://docpad.org/) plugin that compiles `.scss` files using node-sass: <https://github.com/jking90/docpad-plugin-nodesass>

### Duo.js extension

[@stephenway](https://github.com/stephenway) has created an extension that transpiles Sass to CSS using node-sass with [duo.js](http://duojs.org/)
<https://github.com/duojs/sass>

### Grunt extension

[@sindresorhus](https://github.com/sindresorhus/) has created a set of grunt tasks based on node-sass: <https://github.com/sindresorhus/grunt-sass>

### Gulp extension

[@dlmanning](https://github.com/dlmanning/) has created a gulp sass plugin based on node-sass: <https://github.com/dlmanning/gulp-sass>

### Harp

[@sintaxi](https://github.com/sintaxi)’s Harp web server implicitly compiles `.scss` files using node-sass: <https://github.com/sintaxi/harp>

### Metalsmith plugin

[@stevenschobert](https://github.com/stevenschobert/) has created a metalsmith plugin based on node-sass: <https://github.com/stevenschobert/metalsmith-sass>

### Meteor plugin

[@fourseven](https://github.com/fourseven) has created a meteor plugin based on node-sass: <https://github.com/fourseven/meteor-scss>

### Mimosa module

[@dbashford](https://github.com/dbashford) has created a Mimosa module for sass which includes node-sass: <https://github.com/dbashford/mimosa-sass>

## Example App

There is also an example connect app here: <https://github.com/andrew/node-sass-example>

## Rebuilding binaries

Node-sass includes pre-compiled binaries for popular platforms, to add a binary for your platform follow these steps:

Check out the project:

```bash
git clone --recursive https://github.com/sass/node-sass.git
cd node-sass
git submodule update --init --recursive
npm install
node scripts/build -f  # use -d switch for debug release
# if succeeded, it will generate and move
# the binary in vendor directory.
```

## Command Line Interface

The interface for command-line usage is fairly simplistic at this stage, as seen in the following usage section.

Output will be saved with the same name as input SASS file into the current working directory if it's omitted.

### Usage
 `node-sass [options] <input.scss> [<output.css>]`

 **Options:**

```bash
    -w, --watch                Watch a directory or file
    -r, --recursive            Recursively watch directories or files
    -o, --output               Output directory
    -x, --omit-source-map-url  Omit source map URL comment from output
    -i, --indented-syntax      Treat data from stdin as sass code (versus scss)
    -v, --version              Prints version info
    --output-style             CSS output style (nested|expanded|compact|compressed)
    --source-comments          Include debug info in output
    --source-map               Emit source map
    --source-map-embed         Embed sourceMappingUrl as data URI
    --source-map-contents      Embed include contents in map
    --include-path             Path to look for imported files
    --precision                The amount of precision allowed in decimal numbers
    --importer                 Path to custom importer
    --help                     Print usage info
```

Note `--importer` takes the (absolute or relative to pwd) path to a js file, which needs to have a default `module.exports` set to the importer function. See our test [fixtures](https://github.com/sass/node-sass/tree/974f93e76ddd08ea850e3e663cfe64bb6a059dd3/test/fixtures/extras) for example.

## Post-install Build

Install runs only two Mocha tests to see if your machine can use the pre-built [libsass] which will save some time during install. If any tests fail it will build from source.

## Maintainers

This module is brought to you and maintained by the following people:

* Adeel Mujahid - Project Lead ([Github](https://github.com/am11) / [Twitter](https://twitter.com/adeelbm))
* Andrew Nesbitt ([Github](https://github.com/andrew) / [Twitter](https://twitter.com/teabass))
* Dean Mao ([Github](https://github.com/deanmao) / [Twitter](https://twitter.com/deanmao))
* Brett Wilkins ([Github](https://github.com/bwilkins) / [Twitter](https://twitter.com/bjmaz))
* Keith Cirkel ([Github](https://github.com/keithamus) / [Twitter](https://twitter.com/Keithamus))
* Laurent Goderre ([Github](https://github.com/laurentgoderre) / [Twitter](https://twitter.com/laurentgoderre))
* Nick Schonning ([Github](https://github.com/nschonni) / [Twitter](https://twitter.com/nschonni))
* Adam Yeats ([Github](https://github.com/adamyeats) / [Twitter](https://twitter.com/adamyeats))

## Contributors

We <3 our contributors! A special thanks to all those who have clocked in some dev time on this project, we really appreciate your hard work. You can find [a full list of those people here.](https://github.com/sass/node-sass/graphs/contributors)

### Note on Patches/Pull Requests

 * Fork the project.
 * Make your feature addition or bug fix.
 * Add documentation if necessary.
 * Add tests for it. This is important so I don't break it in a future version unintentionally.
 * Send a pull request. Bonus points for topic branches.

## Copyright

Copyright (c) 2015 Andrew Nesbitt. See [LICENSE](https://github.com/sass/node-sass/blob/master/LICENSE) for details.

[libsass]: https://github.com/hcatlin/libsass
