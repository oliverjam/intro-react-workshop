# FAC React Workshop

An introductory workshop for FAC students to learn about React.

### Note

We'll be writing our code in a single `index.html` file for now. There are three scripts included on the page: React, ReactDOM and Babel. The first two expose `React` and `ReactDOM` as globals. The Babel script allows us to use a special `<script type="text/babel">` to have our JS 'transpiled' into standard ES5. This will let us write ES6 and other React specific syntax.

This set-up simpler to jump into without having to learn about complex build-systems when you are really trying to learn React. It also demonstrates that React isn't something magical that requires modern JS and WebPack etc. It's just a JavaScript library that you can include in an HTML page.

This is in no way a best practice, so please don't build a real app this way :)

React apps would usually be split up into separate files using [ES Modules](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import), transpiled to ES5 syntax with Babel and then bundled together into a single JS file with something like WebPack. We'll look at using [Create React App](https://github.com/facebookincubator/create-react-app) eventually to help with some of this complexity.

[Part 1: React syntax](/01-dom-begone)
