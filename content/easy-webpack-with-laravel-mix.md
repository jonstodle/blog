+++
date = "2017-11-25T11:00:00.0000000+00:00"
tags = ["JavaScript","Webpack"]
title = "Easy Webpack with Laravel Mix"
+++
I've started to look into Webpack about 3 times, but I always get overwhelmed and discouraged at an early point and just give it up as a bad job. (If you've got a good getting started tutorial I'm all ears).

However, there's an easier way to work with Webpack that suits my needs perfectly. If you've developed with Laravel (the hugely popular PHP MVC framework) the last year or so, you've probably come across Laravel Mix - a tool to simplify working with Webpack.

While the tool is called Laravel Mix and is built for use in Laravel, it isn't actually part of Laravel. Mix can be used in other projects just fine; it doesn't have any dependencies on Laravel.

Laravel Mix is an abstraction on top of Webpack and aims to make Webpack super simple in 80% of use cases. It uses a simple syntax to compile SASS to CSS, minify JavaScript, compile from ES2017 to older JavaScript syntax, compile TypeScript and bundle files.

To get Laravel Mix you install it through NPM:

    npm install laravel-mix --save-dev
    
Create a file named `webpack.mix.js` in your root folder and you're ready to use Webpack through Mix.

Open `webpack.mix.js` and add the following:

    let mix = require('laravel-mix'); // 1

    mix
        .sass('src/app.sass', 'dist') // 2
        .js('src/app.js', 'dist'); // 3
       
1. First we import Laravel Mix with a `require`.
2. We call `sass` on `mix` to compile SASS to CSS. The first parameter is the path to the `.sass` file, and the second parameter is the folder to output the `.css` file.
3. We call `js` on `mix` to minify and compile the JavaScript. Again the first parameter is the input and the second paramater where to put the output.

Next we copy the following script templates from [the docs](https://github.com/JeffreyWay/laravel-mix/blob/master/docs/installation.md#npm-scripts) to make it easy to run the Mix setup. Add the following scripts ot the `scripts` property in your `package.json`:

    "scripts": {
        "dev": "NODE_ENV=development node_modules/webpack/bin/webpack.js --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
        "watch": "NODE_ENV=development node_modules/webpack/bin/webpack.js --watch --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
        "hot": "NODE_ENV=development webpack-dev-server --inline --hot --config=node_modules/laravel-mix/setup/webpack.config.js",
        "production": "NODE_ENV=production node_modules/webpack/bin/webpack.js --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js"
      }
      
`dev` will compile everything for you, while `production` will also do all the heavy work like minification and compiling ES2017. `watch` will watch the source files for changes and automatically run `dev` every time you save a change. `hot` will run "hot reloading".
 
I usually run `watch` when I start developing and let it run. That way I don't have to worry about any files not being up to date. By default Mix will send a notification when a compile is done, if your OS supports it. If you find this annoying it is possible to turn it off by adding `.disableNotifications()` to your chain in `webconfig.mix.js`.
 
---

If you've been struggling to get started with Webpack, I strongly suggest you give Laravel Mix a try. It gets you started and should you want to customize how it handles different files, the [documentation](https://github.com/JeffreyWay/laravel-mix/tree/master/docs#readme) has a nice list of all of Mix's features.

Should you want to go even deeper, you can add `.webpackConfig(...)` to your chain in `webpack.mix.js` and pass in any custom configuration. Mix will do a deep merge of the original and the custom config. Or you could, of course, edit the webpack.config.js file directly.

Happy coding!