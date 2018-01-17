# Vital Lifecycle

React goes through a whole cycle of stages when rendering a component. We can hook into this with a component's "lifecycle" methods to ensure our code runs at the correct time.

## Methods

These are (where possible) listed in the order React will call them.

### `constructor`

Although this isn't React specific it's technically the first function that runs when React instantiates your component instance. If you need to have access to props to set up your state then you can access that here:

```jsx
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      toggled: props.defaultOn || false
    }
  }
  render() {...}
}
```

Be aware that you should only use this to set initial/default state values. If the props passed to the component change once your application is running the constructor won't be re-run, so the state won't update.

### `componentWillMount`

This is called before React 'mounts' your component to the DOM. It's the only method that runs when you render your React on the server, so is most useful in that scenario.

### `componentDidMount`

You'll use this more than most. It's called right after React mounts your component, so you can do things involving the DOM in here. For example if you need to measure the width or position of a modal before rendering it you can do so here. This will technically call the render method twice (once before and once after `componentDidMount`, but React guarantees the second render will finish before the browser updates, so the user will never see the intermediate state).

This method is often used to fetch data required for initially rendering the component:

```jsx
class Profile extends React.Component {
  state = {
    loading: false,
    data: {}
  }
  componentDidMount() {
    this.setState({ loading: true});
    fetch('https://api.com/user')
      .then(res => this.setState({ data: res.data, loading: false }))
  }
  render() {
    return (
      <div>
        {this.state.loading ? 'Loading...': <img src={this.state.data.imgUrl}} />
      </div>
    )
  }
}
```

### `componentWillReceiveProps`

A long one, that I don't use much. This is called before a mounted component gets updated props. You can use it to update state manually based on props changing (i.e. to keep state in sync with props, since `constructor` only gets called once). It receives `nextProps` as an argument, so you can compare that to `this.props` and update your state if the props have changed.

### `shouldComponentUpdate`

You shouldn't need this for a while. React defaults to re-rendering a component whenever state changes. If you know that some state changes won't require a re-render you can use this method as a performance optimisation. It takes `nextProps` and `nextState`, which you can use to determine whether you want a re-render or not. Simply return true to tell React to re-render, or false to tell it not to bother.

### `componentWillUpdate`

This will be called right before your component re-renders due to new props or state being received. If you need to do any kind of preparation before a re-render this is the place. Just make sure you don't do anything that would trigger _another_ re-render (like calling `setState`), since that would never end.

### `componentDidUpdate`

Called right after the component updates. This is a good place to do DOM-stuff after props change, or fetch some more data based on the new props.

### `componentWillUnmount`

This one is useful for cleaning up after yourself. Anything that shouldn't continue once the component is gone should be removed here. For example you can clear intervals here, or cancel network requests that are still pending.

### `componentDidCatch`

New in React 16. This turns the component into an error-boundary. It will catch any unhandled exceptions thrown lower down in the tree (i.e. in any of its children), passing the error object to this method. You can then render whatever you like based on the error:

```jsx
class ErrorBoundary {
  state = { error: false }
  componentDidCatch(err) {
    console.log(err);
    this.setState({ error: true });
  }
  render() {
    {this.state.error ? <h1>Sorry, something went wrong!</h1> : this.props.children}
  }
}
```

## Holy shit

Yeah there are a lot of these. Don't worry about it—I've been writing React for a year and I've only used 4/9 of them. It's easy to look them up in the [React docs](https://reactjs.org/docs/react-component.html) when you think you need one.

Just try to understand and remember `componentDidMount` and `componentWillUnmount` for now.
