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

- React requires tags with no children to self close (ending with `/>`)
- React treats lowercase tag names as HTML and capitalised tags as components, so be sure to always capitalise your component variables.

### Customising components with props

The props parameter our component function receives is the same object we've been passing to `React.createElement`. We can make use of this to customise our components (`React.createElement(Title, { name: 'Mum' })`). The JSX equivalent is `<Title name="Mum" />`. We can use anything passed this way in our component:

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

Since `children` is a normal prop we use it in the same way:

```jsx
const Title = props => <h1>{props.children}</h1>;
ReactDOM.render(<Title>Hello world</Title>)
```

It's worth noting that this syntax would render the same result:

```jsx
ReactDOM.render(<Title children="Hello world" />, root);
```

It's generally easier (and closer to HTML) to pass children between the tags.

### Composition

We can pass React components to each other as children. This unlocks one of React's most powerful ideas: composition. It means we can encapsulate functionality in a component and then wrap that component around whatever we like.

Imagine we wanted a component that rendered some "card" features (border, box-shadow etc):

```jsx
const Card = props => <div className="card">{props.children}</div>;
```

We can now construct very readable interfaces by using our custom component:

```jsx
const ProductCard = props => (
  <Card>
    <h2>{props.name}</h2>
    <p>{props.description}</p>
  </Card>
);

ReactDOM.render(<ProductCard name="hummus" description="Hummus is real tasty", root);
```

The `<ProductCard>` component shouldn't have to care about the implementation details of a `<Card>`. This is a contrived example but you can have as much complexity as you like concealed behind a simple JSX tag, which gets very powerful.

We don't have to use `children` for composition. Sometimes you need to ensure a child component will be rendered in the right place. You can use props for this:

```jsx
const SplitCard = props => (
  <div className="card">
    <div className="card__left">{props.left}</div>
    <div className="card__right">{props.right}</div>
  </div>
);

const Product = props => (
  <div>
    <h2>{props.name}</h2>
    <p>{props.description}</p>
  </div>
);

const Image = props => <img src={props.imgSrc} />;

const ProductCard = props => (
  <SplitCard
    left={<Product name="hummus" description="I love hummus" />}
    right={<Image src="/hummus-rules.gif" />}
  />
);
```
