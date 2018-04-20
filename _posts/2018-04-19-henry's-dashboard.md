---
layout: post
key: 20180419
modify_date: 2018-04-19
tags: [Node, chart.js, SCSS, Gulp, English]
title: The birth of Henry's Dashboard
---
![Dashboard](/assets/images/2018-03-09/dashboard.jpg)

And some frontend practices.

<!--more-->
# Why
In light of my ever growing list of self-hosted services, where LDAP isn't applicable to all of them, there is a need for an all-in-one dashboard-like page. This also serves as the homepage of [my API instance]({% post_url 2018-03-09-the-exciting-journey-to-my-node-api-server %})。

To make it a little bit more interesting, I decided it should have [Chart.js](http://www.chartjs.org/){:target="_blank"} to display corresponding statistics of that service. Also instead of traditional html + css, I decided to learn something new.

# What I've Learned

## Sassy CSS

Sassy CSS (SCSS) is a CSS preprocessor designed to improve CSS coding experience. Before then I was mixing SCSS with SASS (**S**yntactically **A**wesome **S**tyle **S**heets), and it turns out they are not identical.

### Nesting and Variable
<div class="evenly-distributed-children-3" markdown="1">
{% highlight css %}
/* CSS */

.logo {
  margin: 0 1em -2em 0;
}

.logo span {
  color: #f2e4d6;
}

.headline {
  margin: 0 1em -2em 0;
}

.headline .content {
  background-color: #f2e4d6;
}

{% endhighlight %}

{% highlight scss %}
// SCSS

$margin-medium: 0 1em -2em 0;
$color1: #f2e4d6;

.logo {
  margin: $margin-medium;
  span {
    color: $color1;
  }
}

.headline {
  margin: $margin-medium;
  .content {
    background-color: $color1;
  }
}
{% endhighlight %}

{% highlight sass %}
// SASS

$margin-medium: 0 1em -2em 0
$color1: #f2e4d6

.logo
  margin: $margin-medium
  span
    color: $color1

.headline
  margin: $margin-medium
  .content
    background-color: $color1
{% endhighlight %}
</div>

SASS is the most concise one, SCSS is SASS with CSS syntax, which is also the reason why I chose it (who doesn't like closing semicolons!).

### Inheritance, Function and Mixin
{% highlight scss %}
// SCSS

@mixin border-radius-mixin($radius) {
  -webkit-border-radius: $radius;
  -moz-border-radius: $radius;
  -ms-border-radius: $radius;
  border-radius: $radius;
}

@function border-radius-func($radiusA, $radiusB) {
    @if ($radiusA > $radiusB){
        @return $radiusA - $radiusB;
    }
    @return $radiusB;
}

.roundBox {
  @include border-radius-mixin( 
    border-radius-func(10px, 8px)
  ); 
}

.bigDiv{
  @extend .roundBox;
  font-size: 3em;
}

{% endhighlight %}

### Import
<div class="evenly-distributed-children-2" markdown="1">
{% highlight css %}
/* CSS */

h2 {
  font-weight: 900;
}

p {
  font-weight: 700;
}

span {
  font-weight: 500;
}
{% endhighlight %}

{% highlight scss %}
// _font-weight.scss

$weight: 900;

$types: h2 p span;

@each $type in $types {
  #{$type} {
    font-weight: $weight;
  }
  $weight: $weight - 200;
}
// --- in_some_other.scss ---

@import "_font-weight";
{% endhighlight %}
</div>

There are also many other features introduced in [the official guide](https://sass-lang.com/guide){:target="_blank"} I don't use currently. In short, SCSS drastically improves the reusability and that's absolutely crucial.

## Jade Templating
[Jade](http://jade-lang.com){:target="_blank"}  is a html template engine, which allows you to produce html without opening/closing all the annoying tags. Indentation is crucial in Jade.

{% highlight jade %}
// layout.jade

body
  .wrapper
    .content
      .content-wrapper
        .logo
          img(src="images/logo.jpg", width="100%")
          h1
            | Henry's Dashboard
        .grid-container
          block content <-- this is allows nesting/layout/partial view, whatever you call it.
    .footer-menu
      .menu.github#github Github
      .menu.email#email Email
      .menu.linkedin#linkedin LinkedIn
{% endhighlight %}

{% highlight jade %}
// index.jade

extends layout

block content <-- this will be included into the content block in layout.jade
 .gc-1-2
    .grid.service#rss
      .headline.content.atd
        | RSS
      .content
        | Daily news driver
      .footline Credit: Tiny Tiny RSS
    .grid
      .upper-headline Feed Stats
      .chart(style="position:relative; height:140px;")
        canvas#rssChart
{% endhighlight %}

After learning Jade, it looks like I could've got used to SASS too.

## Build Automation using Gulp
Another objective of building this page, is to explore more npm packages, in this case, it was the amazing [gulp](https://gulpjs.com){:target="_blank"}.

Using gulp I achieved:
1. SCSS was compiled into browser-readable CSS(gulp-sass).
2. Js files were translated into browser-readable versions(via `gulp-babel`) with all dependencies(via `gulp-browserify`) and combined into a single js file(via gulp-concat).
3. Both CSS and JS are all minified to improve loading speed, but `gulp-sourcemaps` still allows them to be revert back during console debugging.
4. Reduced amount of configurations needed (previously I was using babel, uglify and browserify separately).

{% highlight js%}
// in gulpfile.js

const gulp = require('gulp');
const sass = require('gulp-sass');
const sourcemaps = require('gulp-sourcemaps');
const concat = require('gulp-concat');
const uglify = require('gulp-uglify');
const browserify = require('gulp-browserify');
const babel = require('gulp-babel');
const del = require('del');

const paths = {
  scss: ['./scripts/*.scss'],
  js: ['./scripts/*.js'],
};

gulp.task('clean', () => del(['./public/css'], ['./public/js']));

gulp.task('scss', ['clean'], () =>
  gulp
    .src(paths.scss)
    .pipe(sourcemaps.init())
    .pipe(sass({
        outputStyle: 'compressed',
      }).on('error', sass.logError),)
    .pipe(sourcemaps.write())
    .pipe(gulp.dest('./public/css')),);

gulp.task('js', ['clean'], () =>
  gulp
    .src(paths.js)
    .pipe(sourcemaps.init())
    .pipe(babel({
        presets: ['env'],
      }),)
    .pipe(browserify({
        insertGlobals: true,
      }),)
    .pipe(uglify())
    .pipe(concat('index.js'))
    .pipe(sourcemaps.write())
    .pipe(gulp.dest('./public/js')),);

gulp.task('watch', () => {
  gulp.watch(paths.scss, ['scss']);
  gulp.watch(paths.js, ['js']);
});

gulp.task('default', ['scss', 'js']);
{% endhighlight %}


{% highlight js %}
// in package.json

"scripts": {
  "start": "npm run build && node ./bin/www",
  "build": "./node_modules/.bin/gulp"
}
{% endhighlight %}

# Conclusion
A dashboard was built, with charts to display the number of feeds/events generated in the past 7 days by my TTRSS and huginn services (more services to come).

The end product looks easy to produce but the journey is what makes the destination beautiful.

<style>
.evenly-distributed-children-2 figure, .evenly-distributed-children-3 figure{
      display: inline-block;
      vertical-align: top;
      overflow-x: auto;
      width:100%;
}

@media screen and (min-width: 830px) {
  .evenly-distributed-children-3 figure {
      width: 32%;
  }
}

@media screen and (min-width: 600px) and (max-width: 830px) {
  .evenly-distributed-children-3 figure {
      width: 45%;
  }
}

@media screen and (min-width: 600px) {
  .evenly-distributed-children-2 figure {
      width: 48%;
  }

.evenly-distributed-children-2 figure:last-child{
      margin-left: 10px;
  }
}

</style>
