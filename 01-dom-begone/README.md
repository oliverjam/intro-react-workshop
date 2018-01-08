# DOM Begone

Interacting with the DOM can be a frustrating experience. It requires lots of awkward imperative code, forces you to write many lines of repetitive code and is by definition impure.

One of React's main selling points is solving this problem.

### Standard DOM manipulation

Here's an example of creating an element with some properties using the standard browser DOM API:

```js
const root = document.getElementById('root');

const title = document.createElement('h1');
title.className = 'main-title';
title.textContent = 'Hello world!';

root.appendChild(title);
```

We have to create a DOM element, change all the properties we need individually and then set the text inside, for every element we want to create.

We can abstract some of this repetitive code away into a helper function:

```js
function createElement(tag, props, children) {
  const element = document.createElement(tag);
  for (prop in props) {
    element[prop] = props[prop];
  }
  element.textContent = children;
  return element;
}
```

Now we can use our `createElement` function to do some of the annoying work for us:

```js
const root = document.getElementById('root');
const title = createElement('h1', { className: 'main-title' }, 'Hello world!');
root.appendChild(title);
```

Of course this function is very rudimentary—it does no checking for valid HTML tags, will break if we pass an invalid prop and can only set strings as children.

### React's time to shine

We could spend some time refining this, or we could take a look at React's `createElement` function:

```js
const title = React.createElement('h1', { className: 'main-title' }, 'Hello world!');
```

The function's arguments are the same as before, but we aren't limited in the same ways. For example, we can pass as many children as we like, and they'll all be rendered as siblings:

```js
const title = React.createElement('h1', { className: 'main-title' }, 'Hello world!', 'This will be another text node');
```

We can also pass other elements as children:

```js
const title = React.createElement('h1', { className: 'main-title' }, React.createElement('span', {}, 'Hello world!'));
```

### Virtual DOM

If we log the `title` variable we just created we'll see that it's not actually a DOM element—it's an object:

```js
{
  "$$typeof": Symbol(react.element)
  _owner: null
  key: null
  props: { className: "main-title", children: "Hello world!" }
  ref: null
  type: "h1"
}
```

Creating, updating and deleting DOM elements is a costly process, so React tries to do it as little as possible. Instead React works with a big tree of objects known as the "virtual DOM".

Since our `title` isn't actually a DOM element we need a special function to render the object onto the page. This comes from the ReactDOM library:

```js
const root = document.getElementById('root');
const title = React.createElement('h1', { className: 'main-title' }, React.createElement('span', {}, 'Hello world!'));
ReactDOM.render(title, root);
```

This will render:

```html
<h1 class="main-title"><span>Hello world!</span></h1>
```

You may be thinking that those `React.createElement` calls will become hellish to manage, especially you once you start nesting elements. This is where another killer React feature helps us out.

### Introducing JSX

React apps usually use a non-JavaScript syntax known as JSX to create elements. It looks a lot like HTML, which allows us to use syntax we already know to keep our element creation and nesting readable.

Our example above becomes:

```jsx
const title = <h1 className="main-title"><span>Hello world!</span></h1>;
```

Isn't that more readable? It's also almost exactly like the eventual HTML rendered on the page.

There are a few caveats:

1) We need to use Babel to turn our non-standard syntax into regular `React.createElement` function calls that the browser understands.
2) Since we're in JS-land we can't use certain reserved words: `class="..."` becomes `className="..."`
3) Similarly, pretty much all DOM attributes become camelCased: `tabindex="..."` becomes `tabIndex="..."`
