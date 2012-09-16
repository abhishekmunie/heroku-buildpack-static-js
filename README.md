Heroku buildpack: Static-JS
============================

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpack) which minifies and serves javascript files using static nginx.

Usage
-----

Example usage:

    $ ls -R *
    _staticjs.yml    	        main.js
    ...

    $ heroku create --stack cedar --buildpack https://github.com/abhishekmunie/heroku-buildpack-static-js.git
    ...

    $ git push heroku master
    ...
    -----> Heroku receiving push
    -----> Fetching custom buildpack... cloning with git...done
    -----> JavaScript CDN app detected
    -----> Fetching nginx binaries
    -----> Vendoring nginx 1.0.14
    -----> Installing OpenJDK 1.6...done
    -----> Fetching Closure Compiler...done
    -----> Compiling JS files...
           -----> compiling main.js...done
           ...
           done.
    -----> Discovering process types
           Procfile declares types          -> (none)
           Default types for JavaScript CDN -> web
    ...

The buildpack will detect your app as Static JS if it has the file `_staticjs.yml` in the `root`. At present `_staticjs.yml` doesn't support any configuration.
You can set custom nginx config as described for [heroku-buildpack-nginx](https://github.com/abhishekmunie/heroku-buildpack-nginx).

Hacking
-------

To modify this buildpack, fork it on Github. Push up changes to your fork, then
create a test app with `--buildpack <your-github-url>` and push to it.

This buildpack uses [Google Closure Compiler](https://developers.google.com/closure/compiler/) to minify `filename.js` and create `filename.min.js`.
It also creates a copy of `filename.min.js` file with first 8 characters of its sha1 (`filename.<sha1:0:8>.js`).
It simply runs `java -jar compiler.jar --js_output_file "filename.js" --js "filename.min.js"` on all `.js` files except those ending in `.min.js`.
To customize compilation see [Annotating JavaScript for the Closure Compiler](https://developers.google.com/closure/compiler/docs/js-for-compiler) 
and [Advanced Compilation and Externs](https://developers.google.com/closure/compiler/docs/api-tutorial3).