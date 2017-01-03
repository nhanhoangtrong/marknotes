# A quick tour to `gulp` build tool

## Install and run

* Install `gulp`: `$ npm install -g gulp`
* Run a `gulp` task: `$ gulp <task_name>`

## `gulpfile.js`

This where we register all tasks could be executed and call `gulp <task_name>` to run a task. The `default` task could be run with `gulp` command.

### `gulp` tasks

```javascript
var gulp = require('gulp'),
    stylus = require('gulp-stylus')

gulp.task('stylus', function() {
    return gulp.src('src/stylus/**/*.styl')
        .pipe(stylus())
        .pipe(gulp.dest('src/css'))
})
```

### File match

* `*.<ext>`: Match any file name with <ext> in a folder
* `**/*.<ext>`: Match any file name with <ext> in this folder and all subfolders
* `*`: Match every file

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
