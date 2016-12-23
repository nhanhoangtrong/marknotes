# Webpack module bundler

![webpack logo](https://webpack.js.org/cd0bb358c45b584743d8ce4991777c42.svg)

>[`webpack`] is a module bundler that can packing all your submodule into one or many bundled files. webpack is very powerful and configurable.

## `webpack` command line

* `--config <config_file_path>`: set the config file manually. If not set, the default file is `./webpack.config.js`
* `--env`: enviroment passed to the config, when the config was exported as a function

## `webpack` function

We can run `webpack` by passing to it an config object.

Example:

```javascript
let webpack = require('webpack')
let webpackConfig = require('webpack.config.js')

let myWebpackConfig = Object.create(webpackConfig)
webpack(myWebpackObject, function(err, stats) {
	if (err) {
    	return console.error(err)
    }
    return console.log(stats.toString({
    	color: true
    }))
})
```

## `webpack` configuration object

### `entry` property

This property can be a `String` or a `List` tells extractly where to find all the input files. [`webpack`] will try to bundle all the dependencies of each input file.

Example:
```javascript
entry: [
    // Tell webpack to reload React components with hot replacement
    'react-hot-loader/patch',

    // Tell webpack to bundle the client for webpack-dev-server
    // and where to reload the modules
    'webpack-dev-server/client?http://localhost:8080',

    // Tell webpack to bundle for hot reloading
    // only- means to only hot reload for successful updates
    'webpack/hot/only-dev-server',

    // The main entry point
    './index.js'
]

// Multiple entry points
entry: {
    home: './home.js',
    about: './about.js',
    contact: './contact.js'
}
```

### `output` property

This property specifies where [`webpack`] will put all the bundled file.

Example:
```javascript
output: {
    // Tell webpack where to put the output file
    path: path.resolve(__dirname, "dist"),
    // and the file name of output file
    filename: "bundle.js",

    // Tell the webpack where bundled file is served in server,
    // very important in webpack-dev-server
    publicPath: "/"
}
```

### `context` property

Tell webpack where is the source folder to find input entries

```javascript
context: path.resolve(__dirname, "src")
```

### 'devtool' property

|devtool|build|rebuild|production|quality|
| --- | --- | --- | --- | --- |
|eval|+++|+++|no|generated code|
|cheap-eval-source-map|+|++|no|transformed code (lines only)|
|cheap-source-map|+|o|yes|transformed code (lines only)|
|cheap-module-eval-source-map|o|++|no|original source (lines only)|
|cheap-module-source-map|o|-|yes|original source (lines only)|
|eval-source-map|--|+|no|original source|
|source-map|--|--|yes|original source|
|nosources-source-map|--|--|yes|without source content|

### 'devServer' property

This property is for configurating [`webpack-dev-server`] module

Example:
```javascript
devServer: {
    // Tell dev server where to serve file
    contentBase: path.resolve(__dirname, "dist"),

    // Path to server bundled files
    publicPath: '/',

    // Tell webpack bundle not to show information when starting up and after each save
    // Errors and warning will still be shown
    noInfo: true,

    // set the custom port
    port: 8080,
    // and host
    host: '0.0.0.0',
    // proxy is useful when we have a separate API backend development server
    // that we want to send request via current domain
    proxy: {
        '/api': 'http://localhost:3000'
    },

    // enable hot module replacement
    hot: true,
    // toggle dev-server mode between inline and iframe mode
    // if true there is a script inserted to bundle file to take care the live reloading
    // if false <iframe> will be used
    inline: true,

    // if enabled, dev-server will only compile the bundle when it gets requested
    // webpack will not watch any file changes
    lazy: false,

    // add headers to all requests
    headers: {
        'X-Custom-Foo': 'bar'
    },

    // Serve index.html in 404 error
    historyApiFallback: true,

    // serve over HTTP/2 with HTTPS
    https: {
        key: fs.readFileSync('/path/to/server.key'),
        cert: fs.readFileSync('path/to/certification.crt'),
        ca: fs.readFileSync('path/to/ca.pem')
    }
}
```

### `module` property

>These options determine how the different types of modules within a project will be treated.

Example:
```javascript
module: {
    // Tell webpack not parsing any files match the given regular expression
    noParse: /jquery|backbone/,

    // List of rules, each rule tell webpack how to treat a specific modules are loaded by loaders
    rules: [
        {
            // Condition
            test: /.styl$/,
            exclude: /node_modules/,
            include: [
                path.resolve(__dirname, 'path1'),
                path.resolve(__dirname, 'path2')
            ],

            // Results, use when condition matched
            use: [
                {
                    loader: 'css-loader',
                    options: {
                        modules: true
                    }
                },
                {
                    loader: 'stylus-loader'
                }
            ]
        }
    ]

}
```

### `plugins` property

Tell [`webpack`] to use an array of plugins specified in this property

```javascript
plugins: [
    new webpack.HotModuleReplacementPlugin(),
    new CopyWebpackPlugin({
        from: path.resolve(__dirname, 'src/img'),
        to: path.resolve(__dirname, 'dist/img')
    }),
    new ExtractTextPlugin("css/main.css")
]
```

## [`webpack-dev-server`] and `webpack-dev-middlewere`

### Run [`webpack-dev-server`] asynchronously

```javascript
let WebpackDevServer = require('webpack-dev-sevrer')
let webpackConfig = require('webpack.config.js')

let myConfig = Object.create(webpackConfig)
myConfig.plugins = (myConfig.plugins || []).concat(
	new webpack.DefinePlugin({
    	"process.env": {
        	// This has effect on the React lib size
            "NODE_ENV": JSON.stringify("development")
        }
    }))
myConfig.devtool = "eval"
myConfig.debug = true

new WebpackDevServer(webpack(myConfig), {
	publicPath: "/" + myConfig.output.publicPath,
    stats: {
    	colors: true
    }
}).listen(8080, "localhost", function(err) {
	if (err) {
		return console.error(err)
    }
    console.log("Dev-Server is running in http://localhost:8080")
})
```

## Some useful [`webpack`] plugins

### `webpack.HotModuleReplacementPlugin`

For enable Hot Module Replacement

### `extract-text-webpack-plugin`

For extract text to a file without bundle to output

Example:
```javascript
let stylusCss = new ExtractTextPlugin("css/stylus.css")
let lessCss = new ExtractTextPlugin("css/less.css")

// ...
module: {
	rules: [
    	{
        	test: /.less$/, loader: lessCss.extract({
            	loader: 'css-loader!less-loader'
            })
        },
        {
        	test: /.styl$/, loader: stylusCss.extract({
            	loader: 'css-loader!stylus-loader'
            })
        }
    ]
}
```

### `copy-webpack-plugin`

For copy files or folder to output directory




[`webpack`]: https://webpack.js.org/
[`webpack-dev-server`]: https://github.com/webpack/webpack-dev-server
