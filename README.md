Heroku buildpack: Static-JS (with Coffee Script and Google Closure Compiler)
=============================================================================

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpack) which compiles coffee, then minifies and serves javascript files using static nginx.

Usage
-----

Example usage:

    $ ls -R *
    _staticjs.yml    	        coffeescript.coffee         main.js
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
    -----> Creating default nginx configuration.done
    -----> Fetching nginx binaries
    -----> Vendoring nginx 1.0.14
    -----> Discovering process types
           Procfile declares types          -> (none)
           Default types for Static-JS -> web
    ...

The buildpack will detect your app as Static JS if it has the file `_staticjs.yml` in the `root`. At present `_staticjs.yml` doesn't support any configuration.
You can set custom nginx config as described for [heroku-buildpack-nginx](https://github.com/abhishekmunie/heroku-buildpack-nginx).

Hacking
-------

To modify this buildpack, fork it on Github. Push up changes to your fork, then
create a test app with `--buildpack <your-github-url>` and push to it.

This buildpack first uses command-line version of [coffee-script Node.js utility](http://coffeescript.org/#usage) to compile all `filename.coffee` to `filename.js`
by running `coffee --compile --output ${BUILD_DIR} ${BUILD_DIR}`.
It then uses [Google Closure Compiler](https://developers.google.com/closure/compiler/) to minify `filename.js` and create `filename.min.js`.
It also creates a copy of `filename.min.js` file with first 8 characters of its sha1 (`filename.<sha1:0:8>.js`).
It simply runs `java -jar compiler.jar --js_output_file "filename.js" --js "filename.min.js"` on all `.js` files except those ending in `.min.js`.
To customize compilation see [Annotating JavaScript for the Closure Compiler](https://developers.google.com/closure/compiler/docs/js-for-compiler) 
and [Advanced Compilation and Externs](https://developers.google.com/closure/compiler/docs/api-tutorial3).