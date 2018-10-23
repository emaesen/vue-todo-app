# Vue.js Todo App

This is a basic one-page ToDo demo app, written with [vue.js v2](https://vuejs.org/) (for app logic), [semantic-ui](https://semantic-ui.com) (for styling) and [sweetAlert](https://sweetalert.js.org/) (for modal windows).

This is a demo: it allows ToDo cards to be created, edited, moved from one status to another, and deleted. It doesn't store any entered data. Everything you enter will be lost upon reload...

v0.1: The page contains two columns: one for Open Tasks and one for Completed Tasks. ToDo items have a title and project description.

V0.2: Refactored and added new status and column for In Progress Tasks

v0.3: Added note and due date to ToDo item, with visual clues when items are almost due or past due.


## Build Setup

The development build uses [WebPack](http://vuejs-templates.github.io/webpack/) (with hot reload) and [vue-loader](https://vue-loader.vuejs.org/).

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev
# or simply
npm start

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```


## Tests
Test are not (yet) implemented, only some scaffolding is present.

``` bash
# run unit tests
npm run unit

# run e2e tests
npm run e2e

# run all tests
npm test
```
