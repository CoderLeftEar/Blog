---
title: "Gulpfile"
date: 2020-06-10T11:12:41+08:00
author: CoderLeftEar
categories: ["Gulp"]
tags: ["Gulp"]
draft: true
---

# Gulp

## gulpfile.js

```js
const gulp = require('gulp');
const $ = require('gulp-load-plugins')();
const htmlmin = require('gulp-htmlmin');
const cleanCss = require('gulp-clean-css');
const uglify = require('gulp-uglify');
const babel = require('gulp-babel');
const imagemin = require('gulp-imagemin');
const clean = require('gulp-clean');

function htmlBuild () {
  return gulp.src('./src/*.html')
  .pipe(htmlmin({
    collapseWhitespace: true, 
    removeComments: true,
    minifyJS: true,
    minifyCSS: true,
  }))
  .pipe(gulp.dest('./dist/'))
}

function cssBuild () {
  return gulp.src('./src/css/*.css')
  .pipe(gulp.dest('./dist/css/'))
  .pipe(gulp.src(['./src/css/style.css', './src/css/bootstrap-theme.css', './src/css/bootstrap.css',]))
  .pipe(cleanCss({compatibility: 'ie7'}))
  .pipe(gulp.dest('./dist/css/'))
}

function jsBuild () {
  return gulp.src('./src/js/*.js')
  .pipe(gulp.dest('./dist/js/'))
  .pipe(gulp.src(['./src/js/login.js', './src/js/gloab.js', './src/js/bootstrap.js', './src/js/comPort.js', './src/js/dist-vue.js']))
  .pipe(babel({presets: ['@babel/preset-env']}))
  .pipe(uglify())
  .pipe(gulp.dest('./dist/js/'))
}

function imgBuild () {
  return gulp.src('./src/img/*')
  .pipe(imagemin())
  .pipe(gulp.dest('./dist/img/'))
}

function fontBuild () {
  return gulp.src('./src/css/fonts/*')
  .pipe(gulp.dest('./dist/css/fonts/'))
}

function icoBuild () {
  return gulp.src('./src/*.ico')
  .pipe(gulp.dest('./dist/'))
}

function cleanDir () {
  return gulp.src('./dist', {read: false})
  .pipe(clean())
}

exports.default = gulp.series(cleanDir, gulp.parallel(htmlBuild, cssBuild, jsBuild, imgBuild, fontBuild, icoBuild));
```

