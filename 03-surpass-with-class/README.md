# Surpass With Class

We now know how to create React components that can dynamically render elements to the DOM based on props. But how do we add actual interactivity? For that we need to use classes to create our components.

### Class syntax

Our simple `Title` component looks like this as a class:

```jsx
class Title extends React.component {
  render() {
    return <h1>{this.props.children}</h1>;
  }
}
```

We _extend_ the React base component (which provides us with the methods we'll be using). The only mandatory part of a class-based component is the render method. This is effectively the same as the functional components we've been using: you just need to return a React element.

The other difference you may have spotted is the reference to `this`. Classes receive props like all components, but they have to access them on `this`. [TODO: because...?]

### State

To add interactivity we need to learn a new concept: state. React allows components to keep track of their state as an object, which you can update as the result of user interaction. By rendering our UI based on this state we can tell React how the page should look and let it worry about actually updating the DOM. This is the radical difference between React and traditional JQuery-style JavaScript: here we can declaratively _describe_ our UI, without having to specify every step along the way to updating it.

#### Creating state

We'll worry about the render method in a moment. First lets look at how to give a component state:

```jsx
class Toggle extends React.component {
  constructor(props) {
    super(props)
    this.state = {
      toggled: false
    };
  }
  render() {
    ...
  }
  render() {
    return (
      <div>
        <button>Toggle
        {this.state.toggled && <p>Hidden toggle-able text</p>}
      </div>
    )
  }
}
```

We have a constructor (which takes `props` as an argument if you need to use them in initialising your component). Inside the constructor we create a property called `state`, which is an object with whatever key/value pairs we need.

There is a simpler way to do this using the instance properties proposal (which Babel will transpile for us):

```jsx
class Toggle extends React.component {
  state = {
    toggled: false
  }
  render() {
    ...
  }
}
```

That's it!

#### Updating state

React provides all classes with a `setState` method. We can use this two ways:

1. If we're simply updating the state we can pass a new state object as the argument.
2. If we're basing the new state on the old state we can pass a function that receives the old state and props and returns the new state.

Example 1:

````jsx
class Toggle extends React.component {
  state = {
    toggled: false
  }
  toggleOn() {
    this.setState({ toggled: true });
  }
}

Example 2:
```jsx
class Toggle extends React.component {
  state = {
    toggled: false
  }
  toggle() {
    this.setState((prevState, props) => {
      return { toggled: !prevState.toggled};
    });
  }
}
````
