Heroku buildpack: Static-JS (with Coffee Script and Google Closure Compiler)
=============================================================================

* Note: This buildpack will be merged into [abhishekmunie/heroku-buildpack-static](https://github.com/abhishekmunie/heroku-buildpack-static)

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpack) which compiles coffee, then minifies and serves javascript files using a static.

Usage
-----

Example usage:

    $ ls -R *
    _static_js.cfg              coffeescript.coffee         main.js
    ...

    $ heroku create --stack cedar --buildpack https://github.com/abhishekmunie/heroku-buildpack-static-js.git
    ...

    $ git push heroku master
    ...
    -----> Heroku receiving push
    -----> Fetching custom buildpack... cloning with git...done
    -----> Static-JS app detected
    -----> Resolving engine versions
    ...
    -----> Fetching Node.js binaries
    -----> Vendoring node into slug
    -----> Installing dependencies with npm
           coffee-script@1.3.3 ./node_modules/coffee-script
           coffee-script@1.3.3 /tmp/node.Lsu9Yf/node_modules/coffee-script
           Dependencies installed
    -----> Building runtime environment
    -----> Making Coffee...done
    -----> Fetching Closure Compiler...done
    -----> Compiling JS files...
           -----> compiling coffeescript.js...done
           -----> compiling main.js...done
           ...
           done.
           ...
    ...

The buildpack will detect your app as Static JS if it has the file `_static_js.cfg` in the `root`. At present `_static_js.cfg` doesn't support any configuration.
You can set custom static server config as described for [heroku-buildpack-static](https://github.com/abhishekmunie/heroku-buildpack-static).

If the app has `Cakefile` in root it will be compiled using [Cake](http://coffeescript.org/#cake).

Hacking
-------

To modify this buildpack, fork it on Github. Push up changes to your fork, then
create a test app with `--buildpack <your-github-url>` and push to it.

This buildpack first uses command-line version of [coffee-script Node.js utility](http://coffeescript.org/#usage) to compile all `filename.coffee` to `filename.js`
by running `coffee --compile --output ${BUILD_DIR} ${BUILD_DIR}`.
If repo has `Cakefile` in root it will be used instead to compile CoffeeScripts.
The CoffeeScripts are then removed.
It then uses [Google Closure Compiler](https://developers.google.com/closure/compiler/) to minify `filename.js` and create `filename.min.js`.
It also creates a copy of `filename.min.js` file with first 8 characters of its sha1 (`filename.<sha1:0:8>.js`). Files in 'libs' directories will be ignored.
It simply runs `java -jar compiler.jar --js_output_file "filename.js" --js "filename.min.js"` on all `.js` files except those ending in `.min.js`.
To customize compilation see [Annotating JavaScript for the Closure Compiler](https://developers.google.com/closure/compiler/docs/js-for-compiler)
and [Advanced Compilation and Externs](https://developers.google.com/closure/compiler/docs/api-tutorial3).