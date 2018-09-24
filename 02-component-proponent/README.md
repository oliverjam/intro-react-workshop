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

#### Note:

React components should act like pure functions with respect to their props. Don't try to change the props from within the component. Props should only get updated by a component higher up the tree passing new props in (which is equivalent to calling the component function again with new arguments).

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

Children is a normal prop with a couple of extra privileges. It can be passed either like any other prop:

```jsx
const Title = props => <h1>{props.children}</h1>;
ReactDOM.render(<Title children="Hello world" />, root);
```

or between two tags just like in HTML:

```jsx
ReactDOM.render(<Title>Hello world</Title>)
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

ReactDOM.render(<ProductCard name="hummus" description="Hummus is real tasty" />, root);
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
    right={<Image imgSrc="/hummus-rules.gif" />}
  />
);
```

Remember `<Image />` is turned into `React.createElement(Image, {})` by Babel, and `createElement` returns React elements, which are just JavaScript objects. It might look slightly weird but `<SplitCard right={<Image />} />` is fine because we're effectively passing an normal object as the `right` prop.

### Splitting up components

Imagine we had a chunk of HTML like this representing a blog post excerpt:

```html
<article class="post">
  <heading class="post__heading">
    <h2 class="post__title">Everyone should eat hummus</h2>
    <span class="post__subtitle">Hummus is the best</span>
  </heading>
  <div class="post__body">
    <p class="post__summary">This easy dip recipe is great to make sandwiches for your lunchbox, or simply to serve with breadsticks or pitta</p>
  </div>
  <footer class="post__footer">
    <span>Posted at <time datetime="2018-01-18">18th January, 2018</time></span>
  </footer>
</article>
```

We might have lots of these on a single page, which would get pretty unwieldy to read through and work with. We could insert this HTML straight into a React component, but it would be better if we could break this large component down into smaller parts:

```jsx
const Heading = props => (
  <heading className="post__heading">
    <h2 className="post__title">{props.title}</h2>
    <span className="post__subtitle">{props.subtitle}</span>
  </heading>
);

const Body = props => (
  <div className="post__body">
    <p className="post__summary">{props.children}</p>
  </div>
);

const Footer = props => (
  <footer className="post__footer">
    <span>Posted at <time datetime={props.time}>{props.time.toLocaleDateString()}</time></span>
  </footer>
);
```

We can them compose our Post component together from these parts:

```jsx
const Post = props => (
  <article className="post">
    <Heading title={props.title} subtitle={props.subtitle} />
    <Body>{props.children}</Body>
    <Footer time={props.time} />
  </article>
);

const HummusPost = <Post
                    title="Everyone should eat hummus"
                    subtitle="Hummus is the best"
                    time="2018-01-18"
                    >
                    This easy dip recipe is great to make sandwiches for your lunchbox, or simply to serve with breadsticks or pitta
                    </Post>
```

Our Post component is easier to understand at a glance, with more meaningful tag names than the standard HTML. We've also encapsulated some of the implementation noise (like the ugly BEM classes) and made it easier to feed data into the component.

## Exercise

1. Open index.html in your editor and browser. You should see the `<h1>` rendered to the page.
2. Replace the `Title` component with your own that takes props for your name and Github profile URL, then renders your name as a link to your Github
3. Render your new component to the page with the correct props

Next: [Class-based components](/03-surpass-with-class)

## Further Reading

- [Components and Props | React Docs](https://reactjs.org/docs/components-and-props.html)
- [Composition vs Inheritance | React Docs](https://reactjs.org/docs/composition-vs-inheritance.html)
