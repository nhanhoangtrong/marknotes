# browser-sync reload

![browser-sync logo](https://browsersync.io/img/icons/icons.svg)

>With each web page, device and browser, testing time grows exponentially. From live reloads to URL pushing, form replication to click mirroring, [`browser-sync`] cuts out repetitive manual tasks. It’s like an extra pair of hands. Customizing an array of sync settings from the UI or command line to create a personalized test environment. Need more control? [`browser-sync`] is easily integrated with your web platform, build tools, and other Node.js projects.

## [`browser-sync`] command line

* `browser-sync start [options]`: Start [`browser-sync`]
	* `--server, -s`: Run a Local server (uses your cwd as the web root)
	* `--serveStatic, --ss`: Directories to serve static files from
	* `--port`: Specify a port to use
	* `--proxy, -p`: Proxy to an existing server
	* `--ws`: Proxy mode only - enable websocket proxying
	* `--browser, -b`: Choose which browser should be auto-opened
	* `--files, -f`: File paths to watch
	* `--index`: Specify which file should be used as index page
	* `--plugins`: Load Browsersync plugins
	* `--extensions`: Specify file extension fallbacks
	* `--startPath`: Specify the start path for the opened browser
	* `--https`: Enable SSL for local development
	* `--directory`: Show a directory listing for the server
	* `--xip`: Use xip.io domain routing
	* `--tunnel`: Use a public URL
	* `--open`: Choose which URL is auto-opened (local, external or tunnel), or provide a url
	* `--cors`: Add Access Control headers to every request
	* `--config, -c`: Specify a path to a configuration file
	* `--host`: Specify a hostname to use
	* `--logLevel`: Set the logger output level (`silent`, `info` or `debug`)
	* `--reload-delay`: Time in milliseconds to delay the reload event following file changes
	* `--reload-debounce`: Restrict the frequency in which browser:reload events can be emitted to connected clients
	* `--ui-port`: Specify a port for the UI to use
	* `--no-notify`: Disable the notify element in browsers
	* `--no-open`: Don't open a new browser window
	* `--no-online`: Force offline usage
	* `--no-ui`: Don't start the user interface
	* `--no-ghost-mode`: Disable Ghost Mode
	* `--no-inject-changes`: Reload on every file change
	* `--no-reload-on-restart`: Don't auto-reload all browsers following a restart
* `browser-sync init`: create a configuration file
* `browser-sync reload`: send a reload event over HTTP protocol
	* `--files, -f`: File paths to reload
	* `--port, -p`: Target a running instance by port number
	* `--url, -u`: Provide the full the url to the running Browsersync instance
* `browser-sync recipe [name]`: Generate the files for a recipe
	* `--output, -o`: Specify an output directory

## [`browser-sync`] APIs

### `.create(name: String)`

> Create a Browsersync instance, an identifier that can used for retrieval later

### `.get(name: String)`

> Get a single instance by name

### `.has(name: String)`

> Check if an instance has been created or not

### `.init(options: Object, cb[optional]: Function)`

Start the Browsersync service, which will launch a server, proxy or start the snippet mode depending on options

### `.reload(arg[optional]: String | Array | Object)`

> The `reload` method will inform all browsers about changed files and will either cause the browser to refresh, or inject the files where possible.

* `arg`: The file or files to be reloaded.

### `.stream(opts[optional]: Object)`

> The `stream` method returns a transform stream and can act once or on many files.

```javascript
// Compile SASS & auto-inject into browsers
gulp.task('sass', function () {
    return gulp.src('scss/styles.scss')
        .pipe(sass({includePaths: ['scss']}))
        .pipe(gulp.dest('css'))
        .pipe(bs.stream());
});

// Provide `once: true` to restrict reloading to once per stream
gulp.task('templates', function () {
    return gulp.src('*.jade')
        .pipe(jade())
        .pipe(gulp.dest('app'))
        .pipe(bs.stream({once: true}));
});

// Provide a filter to stop unwanted files from being reloaded
gulp.task('less', function () {
    return gulp.src('*.less')
        .pipe(less())
        .pipe(gulp.dest('css'))
        .pipe(bs.stream({match: "**/*.css"}));
});
```

### `.notify(msg: String | HTML, timeout[optional]: Number)`

> Helper method for browser notifications

```javascript
var bs = require("browser-sync").create();

// Text message
bs.notify("Compiling, please wait!");

// HTML message
bs.notify("HTML <span color='green'>is supported</span> too!");

// Since 1.3.0, specify a timeout
bs.notify("This message will only last a second", 1000);
```

### `.exit()`

> This method will close any running server, stop file watching & exit the current process.

### `.watch(patterns: String, opts[optional]: Object, fn[optional]: Function)`

> Stand alone file-watcher. Use this along with Browsersync to create your own, minimal build system

```javascript
// Create a Browsersync instance
var bs = require("browser-sync").create();

// Listen to change events on HTML and reload
bs.watch("*.html").on("change", bs.reload);

// Provide a callback to capture ALL events to CSS
// files - then filter for 'change' and reload all
// css files on the page.
bs.watch("css/*.css", function (event, file) {
    if (event === "change") {
        bs.reload("*.css");
    }
});

// Now init the Browsersync server
bs.init({
    server: "./app"
});
```

### `.pause()`

> Method to pause file change events

### `.resume()`

> Method to resume paused watchers

### `.emitter`

> The internal Event Emitter used by the running Browsersync instance (if there is one). You can use this to emit your own events, such as changed files, logging etc.

```javascript
var bs = require("browser-sync").create();

// Listen for the `init` event
bs.emitter.on("init", function () {
    console.log("Browsersync is running!");
});

bs.init(config);
```

### `.active`

A simple true/false flag that you can use to determine if there's a currently-running Browsersync instance.

### `.paused`

> A simple true/false flag to determine if the current instance is paused

### `.sockets`

> Access to client-side socket for emitting events

## [`browser-sync`] Options

### `ui`: Object

> Browsersync includes a user-interface that is accessed via a separate port. The UI allows to controls all devices, push sync updates and much more.

* `port` - Default: 3001
* `weinre.port` - Default: 8080

### `files`: Array | String, default: `false`

> Browsersync can watch your files as you work. Changes you make will either be injected into the page (CSS & images) or will cause all browsers to do a full-page refresh.

```javascript
// single file
browserSync({
    files: "app/css/style.css"
});

// multiple files
browserSync({
    files: ["app/css/style.css", "app/js/*.js"]
});

// patterns + 1 with custom callback
// since 2.6.0
browserSync({
    files: [
        "wp-content/themes/**/*.css",
        {
            match: ["wp-content/themes/**/*.php"],
            fn:    function (event, file) {
                /** Custom event handler **/
            }
        }
    ]
});
```

### `watchOptions`: Object

> File watching options that get passed along to [Chokidar](https://github.com/paulmillr/chokidar). Check their docs for available options

```javascript
const bs = require('browser-sync').create();

// Options passed to Chokidar
bs.init({
    watchOptions: {
        ignoreInitial: true,
        ignored: '*.txt'
    },
    files: ['./app']
});

// options for chokidar with custom callback
// since 2.6.0
bs.init({
    files: [
        {
            match: ["wp-content/themes/**/*.php"],
            fn: function (event, file) {
                /** Custom event handler **/
            },
            options: {
                ignored: '*.txt'
            }
        }
    ]
});

// NOTE: the .watch() method will not receive
// these options automatically, so you must provide
// them manually in the following way
bs.watch(['app/*.css'], {ignored: '*.map.css'});
```

### `server`: Object | Boolean, default: `false`

> Use the built-in static server for basic HTML/JS/CSS websites.

```javascript
// Serve files from the app directory
server: "app"

// Serve files from the current directory
server: true

// Serve files from the app directory with directory listing
server: {
    baseDir: "app",
    directory: true
}

// Multiple base directories
server: ["app", "dist"]

// Serve files from the app directory, with a specific index filename
server: {
    baseDir: "app",
    index: "index.htm"
}

// Since version 1.2.1
// The key is the url to match
// The value is which folder to serve (relative to your current working directory)
server: {
    baseDir: "app",
    routes: {
        "/bower_components": "bower_components"
    }
}
```

### `proxy`: String | Object | Boolean

> Proxy an EXISTING vhost. Browsersync will wrap your vhost with a proxy URL to view your site.

* `target` - Default: undefined
* `ws` - Default: undefined
* `middleware` - Default: undefined
* `reqHeaders` - Default: undefined
* `proxyReq` - Default: undefined
* `proxyRes` - Default: undefined

```javascript
// Using a vhost-based url
proxy: "local.dev"

// Using a localhost address with a port
proxy: "localhost:8888"

// Using localhost sub directories
proxy: "localhost/site1"

// When your app also uses web sockets
// NOTE: requires 2.8.1 or above
proxy: {
    target: "http://yourlocal.dev",
    ws: true
}

proxy: {
    target: "http://yourlocal.dev",
}

// Modify the server request before it hits your application
// NOTE: requires v2.12.1
proxy: {
    target: "http://yourlocal.dev",
    proxyReq: [
        function(proxyReq) {
            proxyReq.setHeader('X-Special-Proxy-Header', 'foobar');
        }
    ]
}

// Modify the server response after it's returned from the proxy
proxy: {
    target: "http://yourlocal.dev",
    proxyRes: [
        function(proxyRes, req, res) {
            console.log(proxyRes.headers);
        }
    ]
}
```

### `port`: Number, default: 3000

### `middleware`: Function | Array, default: `false`

```javascript
// Multiple Global Middlewares
middleware: [
    function (req, res, next) {
        /** First middleware handler **/
    },
    function (req, res, next) {
        /** Second middleware handler **/
    }
]

// Per-route middleware
// NOTE: requires v2.12.1
middleware: [
    {
        route: "/api",
        handle: function (req, res, next) {
            // handle any requests at /api
        }
    }
]

// Per-route + global middleware
// NOTE: requires v2.12.1
middleware: [
    require("compression")(), // global
    {
        route: "/api", // per-route
        handle: function (req, res, next) {
            // handle any requests at /api
        }
    }
]
```

### `serveStatic`: Array, default: `[]`

**Note:** requires at least version 2.8.0

> Add additional directories from which static files should be served. Should only be used in proxy or snippet mode.

```javascript
var bs = require('browser-sync').create();

// Run in proxy mode with static files also served
// from current directory + ./app/css
bs.init({
    proxy: "http://www.bbc.co.uk",
    serveStatic: ['.', './app/css']
});

// Run in proxy mode where files under /assets will be served
// from a local ./tmp directory
// NOTE: requires 2.17.0
bs.init({
    proxy: "http://www.bbc.co.uk",
    serveStatic: [{
        route: '/assets',
        dir: 'tmp'
    }]
});

// Run in proxy mode where files under /assets + /content will be served
// from a local ./tmp directory
// NOTE: requires 2.17.0
bs.init({
    proxy: "http://www.bbc.co.uk",
    serveStatic: [{
        route: ['/assets', '/content'],
        dir: 'tmp'
    }]
});

// Run in proxy mode where files under /assets will be served
// from either the ./tmp or ./app directory
// NOTE: requires 2.17.0
bs.init({
    proxy: "http://www.bbc.co.uk",
    serveStatic: [{
        route: '/assets',
        dir: ['./tmp', './app']
    }]
});
```

### `serveStaticOptions`: Object

**Note:** requires at least version 2.17.0

> Options that are passed to the serve-static middleware when you use the `string[]` syntax: eg: `serveStatic: ['./app']`. Please see [`serve-static`] for details

```javascript
var bs = require('browser-sync').create();

// Serve files from 3 directories with serve-static options
bs.init({
    serveStatic: ['.', './app', './temp'],
    serveStaticOptions: {
        extensions: ['html'] // pretty urls
    }
});
```

### `https`: Boolean, default: `undefined`

> Enable https for localhost development. **Note** - this is not needed for proxy option as it will be inferred from your target url.

### `httpModule`: String

**Note:** requires at least version 2.17.6

> Override http module to allow using 3rd party server modules (such as http2)

```javascript
// First run: npm install browser-sync http2
const bs = require('browser-sync').create();

bs.init({
    server: './app',
    httpModule: 'http2',
    https: true
});
```

### `ghostMode`: Object

* `clicks` - Default: true
* `scroll` - Default: true
* `forms` - Default: true

> Clicks, Scrolls & Form inputs on any device will be mirrored to all others.

```javascript
// Here you can disable/enable each feature individually
ghostMode: {
    clicks: true,
    forms: true,
    scroll: false
}

// Or switch them all off in one go
ghostMode: false
```

### `logLevel`: String, default: `"info"`

> Can be either "info", "debug", "warn", or "silent"

### `logPrefix`: String, default: `"BS"`

> Change the console logging prefix. Useful if you're creating your own project based on Browsersync

### `logConnections`: Boolean, default: `false`

### `logFileChanges`: Boolean, default: `true`

### `logSnippet`: Boolean, default: `true`

> Log the snippet to the console when you're in snippet mode (no proxy/server)

### `snippetOptions`: Object

* `async` - Default: undefined
* `blacklist` - Default: undefined
* `whitelist` - Default: undefined
* `rule.match` - Default: /$/
* `rule.fn` - Default: Function

**Note:** requires at least version 2.0.0

> You can control how the snippet is injected onto each page via a custom regex + function. You can also provide patterns for certain urls that should be ignored from the snippet injection.

### `rewriteRules`: Array, default: `false`

**Note:** requires at least version 2.4.0

> Add additional HTML rewriting rules.

```javascript
// Replace every occurrence of the word Browsersync with 'kittenz'
rewriteRules: [
    {
        match: /Browsersync/g,
        fn: function (req, res, match) {
            return 'kittenz';
        }
    }
]

// Also accepts a string as a replacement
rewriteRules: [
    {
        match: /(cats|kitten[sz]) are mediocre/g,
        replace: "$1 are excellent"
    }
]
```

### `tunnel`: String | Boolean

### `online`: Boolean

### `open`: Boolean | String, default: `true`

> Decide which URL to open automatically when Browsersync starts. Defaults to "local" if none set. Can be `true`, `local`, `external`, `ui`, `ui-external`, `tunnel` or `false`

### `browser`: String | Array

```javascript
// Open the site in Chrome
browser: "google chrome"

// Open the site in Chrome & Firefox
browser: ["google chrome", "firefox"]
```

### `cors`: Boolean, default: `false`

**Note:** requires at least version 2.16.0

> Add HTTP access control (CORS) headers to assets served by Browsersync.

### `plugins`: Array

### `startPath`: String | Null

```javascript
// Open the first browser window at URL + "/info.php"
startPath: "/info.php"
```

### `host`: string

### `localOnly`: Boolean, default: `false`

> Support environments where dynamic hostnames are not required (ie: electron)

```javascript
// For use in electron development
const bs = require('browser-sync');
bs.init({
    localOnly: true
});
```

### `codeSync`: Boolean, default: `true`

> Don't send any file-change events to browsers, set to `false`

### `socket`: Object

* `path` - Default: "/browser-sync/socket.io"
* `clientPath` - Default: "/browser-sync"
* `namespace` - Default: "/browser-sync"
* `domain` - Default: undefined
* `port` - Default: undefined
* `clients.heartbeatTimeout` - Default: 5000

> Configure the Socket.IO path and namespace & domain to avoid collisions.

```javascript
// To change the namespace used by Browsersync
socket: {
    namespace: '/someothername'
}

// By default, Browsersync is configured to use the domain
// of your page in order to connect to socket.io
// eg: location.host + '/browser-sync
// but, should you need to change that, provide the `domain` property
socket: {
    domain: 'localhost:3000'
}
```

## [`browser-sync`] Recipes

> [Recipes](https://github.com/BrowserSync/recipes) are stand-alone examples of how to use [`browser-sync`] alongside many other popular tools. Each example can be run separately and is a great way for newcomers and pros alike to get their heads around the different use-cases that are possible.

[`browser-sync`]: https://browsersync.io/
[`serve-static`]: https://github.com/expressjs/serve-static
