# Component Proponent

We've seen how to create React elements and pass them around as variables. They aren't very useful though, as we can't compose our JSX in the same way we would HTML. There is also no way to update an element—you'd have to create a new version and re-render that to the DOM. Whilst React is smart enough to only update the bits of the DOM that have actually changed ([more on this here](https://reactjs.org/docs/rendering-elements.html)), this isn't ideal for building a UI.

At this point we need to distinguish between _elements_ and _components_. As we've seen an element is an object describing what you want in the DOM. A _component_ is more like a function: they accept inputs (called `props`) and return React elements.

The simplest way to define a component is with a function:

```jsx
function Title(props) {
  return <h1>Hello world!</h1>;
}
```

We could also use arrow functions to simplify this:

```jsx
const Title = props => <h1>Hello world!</h1>;
```

### Rendering components

So how do we render them? Elements can represent either HTML elements or user-defined components. Unlike our naive `createElement` function from part 1, `React.createElement` can take more than an HTML tag name as the first argument. It can also take a React component as a variable. For example, if we have created the `Title` component from above:

```jsx
ReactDOM.render(React.createElement(Title, {}), root);
// renders <h1>Hello world!</h1>
```

We could also use JSX in place of `React.createElement`:

```jsx
ReactDOM.render(<Title />, root);
// renders <h1>Hello world!</h1>
```

#### Note:

- React requires tags with no children to self close (ending with `/>`
- React treats lowercase tag names as HTML and capitalised tags as components, so be sure to always capitalise your component variables.

### Customising components with props

Since `props` is the object passed to `React.createElement` we can make use of this to customise our components. It's the same way we were passing `className` to our HTML element earlier: `React.createElement(Title, { name: 'Mum' })`. The JSX equivalent would be `<Title name="Mum" />`. We then have access to these props in our component as a function parameter. For example:

```jsx
const Title = props => <h1>Hello {props.name}</h1>;
ReactDOM.render(<Title name="Mum" />, root);
// renders <h1>Hello Mum</h1>
```

There's a new bit of JSX syntax here: we can embed [JS expressions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#Expressions) in our JSX using curly braces. As long as it resolves to a value you can put any JS you want in here. For example:

```jsx
const Title = props => <h1>Hello {props.name + '!'}</h1>
ReactDOM.render(<Title name="Mum" />, root);
// renders <h1>Hello Mum!</h1>
```

This means we can do things like ensure something will be rendered even if no prop is passed:

```jsx
const Title = props => <h1>Hello {props.name || 'world'}</h1>
ReactDOM.render(<Title />, root);
// `props.name` is undefined, so renders <h1>Hello world</h1>
```

### Children

Earlier we were passing a third argument to `React.createElement` (`children`). `children` is a prop like any other: `React.createElement('div', {}, 'Hello world!')` is the same as `React.createElement('div', { children: 'Hello world!'})`.

We can pass children to our components just like HTML elements, by nesting the children within the tags. For example:
