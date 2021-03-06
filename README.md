[![Build Status](https://travis-ci.org/istarkov/babel-plugin-webpack-loaders.svg?branch=master)](https://travis-ci.org/istarkov/babel-plugin-webpack-loaders)

# babel-plugin-webpack-loaders

This babel 6 plugin allows you to use webpack loaders in babel.
It's now easy to run universal apps on the server without additional build steps and to create libraries as usual with `babel src --out-dir lib` command.
It just replaces `require - import` statements with `webpack loaders` results. Look at babel build output  [diff](https://github.com/istarkov/babel-plugin-webpack-loaders/commit/2a7a6d1e61ea3d052b34afd5c3abc46f075d277c#diff-4) to get the idea.

For now this plugin is at alpha quality and tested on webpack loaders I use in my projects.
These loaders are `file-loader`, `url-loader`, `css-loader`, `style-loader`, `sass-loader`, `postcss-loader`.
Plugin supports all webpack features like loaders chaining, webpack plugins, and all loaders params. It's easy because this plugin just uses webpack.

There are three examples here:

- [runtime css-modules example](https://github.com/istarkov/babel-plugin-webpack-loaders/blob/master/examples/runExample/run.js) with simple [webpack config](https://github.com/istarkov/babel-plugin-webpack-loaders/blob/master/examples_webpack_configs/run.webpack.config.js),
run it with `npm run example-run`

- [library example](https://github.com/istarkov/babel-plugin-webpack-loaders/blob/master/examples/myCoolLibrary/myCoolLibrary.js) with [multi loaders-plugins webpack config](https://github.com/istarkov/babel-plugin-webpack-loaders/blob/master/examples_webpack_configs/lib.webpack.config.js),
build it with `npm run example-build` and execute with `node build/myCoolLibrary/myCoolLibrary.js`, assets and code will be placed at `./build/myCoolLibrary` folder.

  Here is [output diff](https://github.com/istarkov/babel-plugin-webpack-loaders/commit/2a7a6d1e61ea3d052b34afd5c3abc46f075d277c#diff-4) of this [library example](https://github.com/istarkov/babel-plugin-webpack-loaders/blob/master/examples/myCoolLibrary/myCoolLibrary.js) build without and with current plugin.

- [minimal-example-standalone-repo](https://github.com/istarkov/minimal-example-for-babel-plugin-webpack-loaders)

# How it works

Look at [minimal-example](https://github.com/istarkov/minimal-example-for-babel-plugin-webpack-loaders)

- You need to create [webpack config](https://github.com/istarkov/minimal-example-for-babel-plugin-webpack-loaders/blob/master/webpack.config.js) file.

- You need add to `.babelrc` [next lines](https://github.com/istarkov/minimal-example-for-babel-plugin-webpack-loaders/blob/master/.babelrc#L1-L16)

- Now you can run [next javascript file](https://github.com/istarkov/minimal-example-for-babel-plugin-webpack-loaders/blob/master/example.js)

  ```javascript
  // example.js
  import css from './example.css';
  console.log('css-modules result:', css);
  ```

  with command `NODE_ENV=EXAMPLE ./node_modules/.bin/babel-node ./example.js` and you get the console output

  ```javascript
  css-modules result: { main: 'example__main--zYOjd', item: 'example__item--W9XoN' }
  ```

- Here I placed [output diff](https://github.com/istarkov/babel-plugin-webpack-loaders/commit/2a7a6d1e61ea3d052b34afd5c3abc46f075d277c#diff-4)
of this [babel library](https://github.com/istarkov/babel-plugin-webpack-loaders/blob/master/examples/myCoolLibrary/myCoolLibrary.js) build without and with plugin.
As you can see plugin just replaces require with loaders results. [All loaders](https://github.com/istarkov/babel-plugin-webpack-loaders/blob/example-output/build/myCoolLibrary/assets/myCoolStyle.css#L12) and [plugins](https://github.com/istarkov/babel-plugin-webpack-loaders/blob/example-output/build/myCoolLibrary/assets/myCoolStyle.css#L4) applied to generated assets


# Install

```shell
npm install --save-dev babel-cli babel-plugin-webpack-loaders
```

# Examples

[webpack configs](https://github.com/istarkov/babel-plugin-webpack-loaders/tree/master/examples_webpack_configs),
[examples](https://github.com/istarkov/babel-plugin-webpack-loaders/tree/master/examples),
[.babelrc example](https://github.com/istarkov/babel-plugin-webpack-loaders/blob/master/.babelrc),
[tests](https://github.com/istarkov/babel-plugin-webpack-loaders/tree/master/test),
[minimal-example-repo](https://github.com/istarkov/minimal-example-for-babel-plugin-webpack-loaders)

you can test local examples just cloning this repo and running next commands

```shell
npm install
# example above
npm run example-run
# library example - build library with a lot of modules
npm run example-build
# and now you can use your library using just node
node build/myCoolLibrary/myCoolLibrary.js
# test sources are also good examples
npm run test
```

# Why

The source of inspiration of this plugin is [babel-plugin-css-modules-transform](https://github.com/michalkvasnicak/babel-plugin-css-modules-transform)

- But I love to write css with sass
- I just like webpack and its loaders (chaining, plugins, settings).
- I want to opensource an UI library which heavily uses css-modules + sass and other webpack loaders.
  Library contains of many small modules and every module must be available to users independently like `lodash/blabla/blublu`.
  Now I can replace heavy build file with this plugin and just one command `babel src --out-dir lib`

# How plugin works internally

Plugin tests all `require` pathes with [test regexps](https://github.com/istarkov/babel-plugin-webpack-loaders/blob/master/src/plugin.js#L91) from webpack config loaders,

- for each successful test plugin [synchronously executes webpack](https://github.com/istarkov/babel-plugin-webpack-loaders/blob/master/src/runWebPackSync.js#L15-L16),

- using babel-parse plugin [parses webpack output](https://github.com/istarkov/babel-plugin-webpack-loaders/blob/master/src/plugin.js#L7),

- plugin [replaces](https://github.com/istarkov/babel-plugin-webpack-loaders/blob/master/src/plugin.js#L104) require ast with parse ast output.

# Verbose mode in config

By default babel caches compiled files, if you need to view webpack stdout output, run commands with
`BABEL_DISABLE_CACHE=1` prefix

# Thanks to

[Felix Kling](https://github.com/fkling) and his [astexplorer](https://github.com/fkling/astexplorer)

[James Kyle](https://github.com/thejameskyle) and his [babel-plugin-handbook](https://github.com/thejameskyle/babel-plugin-handbook)

[Michal Kvasničák](https://github.com/michalkvasnicak) and his [babel-plugin-css-modules-transform](https://github.com/michalkvasnicak/babel-plugin-css-modules-transform)
