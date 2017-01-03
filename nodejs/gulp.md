# A quick tour to `gulp` build tool

## Install and run

* Install `gulp`: `$ npm install -g gulp`
* Run a `gulp` task: `$ gulp <task_name>`

## `gulpfile.js`

This where we register all tasks could be executed and call `gulp <task_name>` to run a task. The `default` task could be run with `gulp` command.

### Create a `gulp` tasks

```javascript
var gulp = require('gulp'),
    stylus = require('gulp-stylus')

gulp.task('stylus', function() {
    return gulp.src('src/stylus/**/*.styl')
        .pipe(stylus())
        .pipe(gulp.dest('src/css'))
})
```

### File matching in `gulp.src` function

* `*.<ext>`: Match any file name with <ext> in a folder
* `**/*.<ext>`: Match any file name with <ext> in this folder and all subfolders
* `**/*.+(<ext1>|<ext2>)`: Match any file name which has extensions <ext1> or <ext2>
* `*`: Match every file in folder
* `**/*`: Match every file in folder and subfolder

### Integrate `browser-sync` with `gulp`

```javascript
var browserSync = require('browser-sync').create('name')

// This task will notify browserSync to reload every time it run
gulp.task('stylus', function() {
    return gulp.src('src/stylus/**/*.styl')
        .pipe(stylus())
        .pipe(gulp.dest('src/css'))
        // Notify browserSync to update
        .pipe(browserSync.reload({
            stream: true
        }))
})

// Create a `browserSync` task that initialize the browserSync
gulp.task('browserSync', function() {
    browserSync.init({
        server: {
            baseDir: 'dist'
        }
    })
})

// This task will `watch` and reload after any file is changed
gulp.task('watch', ['tasks', 'to', 'run', 'parallel', 'before', 'watching'], function(done) {
    gulp.watch('files/to/watch', ['tasks', 'when', 'files', 'change'])
    gulp.watch('files/to/watch', browserSync.reload)
})
```

### Run `Gulp` tasks asynchronously/synchronously

For running taks asynchronously/synchronously, we could use two ways:

#### Using `gulp.series` and `gulp.parallel` with `gulp@^4.0.0`

```javascript
let gulp = require('gulp')

gulp.task('default', gulp.series('task1', 'task2', gulp.parallel('task3-1', 'task3-2'), function(done) {
    done()
}))
```

### Using `run-sequence` module

First we have to install this module:

`npm install --save-dev run-sequence`

and then we can use this module in `gulpfile.js`

```javascript
let runSequence = require('run-sequence')
gulp.task('default', function() {
    runSequence('task1', 'task2', ['task3-1', 'task3-2'])
})
```

## Some useful `gulp` modules

For more modules, find at [essential-gulp-plugins](https://github.com/Pestov/essential-gulp-plugins)

### Utilities

#### `gulp-rename` for renaming output files

#### `gulp-useref` for merging multiple files at ouput stage

#### `gulp-watch` is a file watcher that uses super-fast [`chokidar`] and emits vinyl objects.

Example:
```javascript
var gulp = require('gulp'),
    watch = require('gulp-watch');

gulp.task('stream', function () {
    // Endless stream mode
    return watch('css/**/*.css', { ignoreInitial: false })
        .pipe(gulp.dest('build'));
});

gulp.task('callback', function () {
    // Callback mode, useful if any plugin in the pipeline depends on the `end`/`flush` event
    return watch('css/**/*.css', function () {
        gulp.src('css/**/*.css')
            .pipe(gulp.dest('build'));
    });
});
```

#### `gulp-concat` for concatenating files

### CSS files

#### `gulp-nanocss` for nanofying CSS files

#### `gulp-uncss` for removing unused CSS

#### `gulp-csso` is a very cool CSS minificator

### HTML files

### `gulp-htmlmin` is a neat HTML minificator

### Javascript

#### `gulp-babel` for transpiling from next generation JS

#### `gulp-uglify` for minifying JS files

### Testing

#### `gulp-mocha` is a thin wrapper around [`mocha`]

[`gulp`]: http://gulpjs.com/
[`mocha`]: https://mochajs.org/
[`chokidar`]: https://github.com/paulmillr/chokidar
