# Surpass With Class

We now know how to create React components that can dynamically render elements to the DOM based on props. But how do we add actual interactivity? For that we need to use classes to create our components.

### Class syntax

Our simple `Title` component looks like this as a class:

```jsx
class Title extends React.Component {
  render() {
    return <h1>{this.props.children}</h1>;
  }
}
```

We _extend_ the React base component (which provides us with the methods we'll be using). The only mandatory part of a class-based component is the render method. This is effectively the same as the functional components we've been using: you need to return a React element.

The other difference you may have spotted is the reference to `this`. Classes receive props like all components, but they have to access them on `this`. You can only access arguments passed to an ES6 class in the constructor, so React binds them to the instance for you, which is why you have to use `this.props` to reference them.

#### Note:

We can take a look under the hood to see what we're actually dealing with here:

```jsx
class Title extends React.Component {
  render() {
    return <h1>{this.props.children}</h1;
  }
}

const myTitle = <Title>Hello world</Title>

console.log(myTitle);
{
  "$$typeof": Symbol(react.element),
  _owner: null,
  _self: null,
  _source: null,
  _store: {},
  key: null,
  props: { children: "Hello world" },
  ref: null,
  type: Title()
}

console.log(new Title({ children: 'hummus'}));
{
  context: undefined,
  props: { children: 'hummus' },
  refs: {},
  state: { toggled: false },
  updater: {}
}
```

So if we use JSX to create a React Element it results in the element object we saw in part 1. But if we initialise the class by calling it as a constructor we can see how React represents it internally. We get an object with the relevant properties we added to the class (and some that we aren't using yet, like refs).

### State

To add interactivity we need to learn a new concept: state. React allows components to keep track of their state as an object, which you can update as the result of user interaction. By rendering our UI based on this state we can tell React how the page should look and let it worry about actually updating the DOM. This is the radical difference between React and traditional JQuery-style JavaScript: here we can _describe_ how our UI should look, without having to specify every step along the way to updating it.

#### Creating state

We'll worry about the render method in a moment. First lets look at how to give a component state:

```jsx
class Toggle extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      toggled: false
    };
  }
  render() {...}
}
```

We have a constructor (which takes `props` as an argument if you need to use them in initialising your component). Inside the constructor we create a property called `state`, which is an object with whatever key/value pairs we need.

There is a simpler way to do this using the ES7 class properties proposal (which Babel will transpile for us):

```jsx
class Toggle extends React.Component {
  state = {
    toggled: false
  }
  render() {...}
}
```

That's it!

#### Updating state

React provides all classes with a `setState` method. We can use this two ways:

1. If we're simply updating the state we can pass a new state object as the argument.
2. If we're basing the new state on the old state we can pass a function that receives the old state and props and returns the new state.

##### Example 1:

```jsx
class Toggle extends React.Component {
  state = {
    toggled: false
  }
  toggleOn() {
    this.setState({ toggled: true });
  }
}
```

##### Example 2:

```jsx
class Toggle extends React.Component {
  state = {
    toggled: false
  }
  toggle() {
    this.setState((prevState, props) => {
      return { toggled: !prevState.toggled};
    });
  }
  render() {...}
}
```

##### After state updates

It's worth highlighting that `setState` is asynchronous. React will batch these calls up, so you can't rely on state being updated immediately. If you need something to only happen after a state update you can pass a callback as a second argument to `setState`:

```jsx
class Toggle extends React.Component {
  state = {
    toggled: false
  }
  toggle() {
    this.setState({ toggled: true }, () => {
      // do something relying on the state update in here
    });
  }
  render() {...}
}
```

### Rendering based on state

Now that we have our state updating we can use it to describe our UI:

```jsx
class Toggle extends React.Component {
  state = {
    toggled: false
  }
  toggle() {
    this.setState((prevState, props) => {
      return { toggled: !prevState.toggled };
    })
  }
  render() {
    return (
      <div>
        {this.state.toggled && <p>Hidden toggle-able text</p>}
      </div>
    )
  }
}
```

We are using `&&` to ensure that we only render the second statement if the first is true. Another common pattern is to use a ternary operator to either render one thing or another based on the state.

#### Event handlers

You may notice we have no way to actually change the state. We need to create a button with a click-handler that calls our `toggle` method. You can pass events to React elements as props: they start with 'on' and end with the event name (e.g. `onClick`, `onChange`, `onMouseEnter` etc).

```jsx
class Toggle extends React.Component {
  state = {
    toggled: false
  }
  toggle() {
    this.setState((prevState, props) => {
      return { toggled: !prevState.toggled };
    })
  }
  render() {
    return (
      <div>
        <button onClick={this.toggle}>Toggle</button>
        {this.state.toggled && <p>Hidden toggle-able text</p>}
      </div>
    )
  }
}
```

Unfortunately this will currently throw an error: `this is undefined`.

#### We need to talk about `this`

This is a common problem with class-based components. React does not automatically bind your event handlers (like our `toggle` method) to your component instance, because they don't always come from that component. It's quite common to pass these as props to allow children to update their parent's state, in which case you wouldn't want `this` to point at the child.

Practically what this means is you need to make sure `this` is bound correctly. There are lots of ways of doing this:

##### Using inline arrow functions

An arrow function defined inline will pass its classes context to your method.

```jsx
class Toggle extends React.Component {
  state = {
    toggled: false
  }
  toggle() {...}
  render() {
    return (
      <button onClick={() => this.toggle()}>Toggle}</button>
    )
  }
}
```

##### Binding in the constructor

You'll see this a lot: we create a new function from the old method, but _bound_ to the class instance.

```jsx
class Toggle extends React.Component {
  constructor {
    this.state = { toggled: false };
    this.toggle = this.toggle.bind(this);
  }
  toggle() {...}
  render() {...}
}
```

##### Using ES7 class properties

This requires the least extra code, using a new class feature from ES7. We can create an arrow function as a class property in the same way we were adding our `state` before:

```jsx
class Toggle extends React.Component {
  state = { toggled: false };
  toggle = () => {...};
  render() {...}
}
```

Because arrow functions always get their context from where they were defined this `toggle` will have the correct `this`.

### Finally...

So here's our final fully functional toggle component:

```jsx
class Toggle extends React.Component {
  state = {
    toggled: false
  }
  toggle = () => {
    this.setState((prevState, props) => {
      return { toggled: !prevState.toggled };
    })
  }
  render() {
    return (
      <div>
        <button onClick={this.toggle}>Toggle</button>
        {this.state.toggled && <p>Hidden toggle-able text</p>}
      </div>
    )
  }
}
```

Can you see how the JSX in our render method _describes_ how we want the UI to look? We don't have to write code telling the browser _how_ to update anything, we just show how it should look for each state and provide a way for state to update.

## Exercise

1. Open `index.html` in your editor and browser. You should see the toggle component.
2. Try to make the button display what it's about to do (e.g. "Toggle on" or "Toggle off").
3. Try making your own class-based component with state (copy/paste is cheating).
