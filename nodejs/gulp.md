# A quick tour to `gulp` build tool

## Install and run

* Install `gulp`: `$ npm install -g gulp`
* Run a `gulp` task: `$ gulp <task_name>`

## `gulpfile.js`

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
var browserSync = require('browser-sync')

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

// This task will `watch` and reload after all change
gulp.task('watch', [])
```
