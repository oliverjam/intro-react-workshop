# Storm the Form

Forms are handled a little bit differently in React. This is because, unlike most HTML elements, forms inputs have their own internal state stored in the DOM. Ideally we want a single-source of truth in React. We also want to be able to use the input values without having to go into the DOM and get them.

### Controlled components

Generally we use a technique called "controlled components" to keep our input state in React instead of the DOM. This involves maintaining an entry in state for each input your form renders:

```jsx
class Form extends React.Component {
  state = {
    input: '',
  };
  render() {
    return (
      <form>
        <label htmlFor="name">Name:</label>
        <input type="text" id="name" value={this.state.input} />
        <button type="submit">Submit</button>
      </form>
    );
  }
}
```

We set the `value` prop of our `<input />` to be the correct entry in our state. Unfortunately this means the input is unusable, as we haven't provided any way for that state to change. This is the difference with controlled inputs—everything happens within React/JavaScript. The input is just reflecting our state.

```jsx
class Form extends React.Component {
  state = {
    input: '',
  };
  handleChange = event => {
    const value = event.target.value;
    this.setState({ input: value });
  };
  render() {
    return (
      <form>
        <label htmlFor="name">Name:</label>
        <input
          type="text"
          id="name"
          value={this.state.input}
          onChange={this.handleChange}
        />
        <button type="submit">Submit</button>
      </form>
    );
  }
}
```

Now our input has an `onChange` event handler that will set the state to the new value every time the user types into the field.

#### Different input types

Certain types of inputs have to be handled slightly differently.

##### `textarea`

`textarea`s usually defines the text inside via its children. React uses a `value` prop to make it consistent with other inputs.

##### `select`

A `select` usually contains several `option` elements, of which one will have the `selected` attribute. This is the option shown in the `select` in its unexpanded state. In React this is again controlled with a `value` prop on the parent `select`. This makes it easier to control as you only have to update it in one place.

##### Checkboxes and radios

These inputs are controlled with a `checked` prop instead of `value`. `checked` can either be true or false. It's worth remembering that groups of radios should only be able to have a single option selected, so you should have a single entry in state for each group:

```jsx
class Form extends React.Component {
  state = {
    radioSelected: 'phone',
  };
  handleChange = event => {
    const value = event.target.value;
    this.setState({ radioSelected: value });
  };
  render() {
    return (
      <form>
        <label htmlFor="phone">Phone</label>
        <input
          type="radio"
          id="phone"
          name="contact"
          value="phone"
          checked={this.state.radioSelected === 'phone'}
          onChange={this.handleChange}
        />

        <label htmlFor="email">Email</label>
        <input
          type="radio"
          id="email"
          name="contact"
          value="email"
          checked={this.state.radioSelected === 'email'}
          onChange={this.handleChange}
        />

        <label htmlFor="post">Post</label>
        <input
          type="radio"
          id="post"
          name="contact"
          value="post"
          checked={this.state.radioSelected === 'post'}
          onChange={this.handleChange}
        />

        <button type="submit">Submit</button>
      </form>
    );
  }
}
```

#### Multiple inputs

If we have more than one input it can get annoying writing event handlers for each. It's easier to use the `name` attribute to know which property in state should be updated. We also need to check whether the input is a checkbox, so we update the right prop:

```jsx
class Form extends React.Component {
  state = {
    name: '',
    email: '',
    consent: false,
  };
  handleChange = event => {
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    this.setState({ [name]: value });
  };
  render() {
    return (
      <form>
        <label htmlFor="name">Name:</label>
        <input
          type="text"
          id="name"
          name="name"
          value={this.state.name}
          onChange={this.handleChange}
        />

        <label htmlFor="email">Email:</label>
        <input
          type="email"
          id="email"
          name="email"
          value={this.state.email}
          onChange={this.handleChange}
        />

        <label htmlFor="consent">Can we email you spam?</label>
        <input
          type="checkbox"
          id="consent"
          name="consent"
          checked={this.state.consent}
          onChange={this.handleChange}
        />

        <button type="submit">Submit</button>
      </form>
    );
  }
}
```

### Form submission

Forms take an `onSubmit` event handler. It's much easier to get our input data now that it's all in state—we just have to grab it and do what we want with it. Don't forget to reset everything after you submit!

```jsx
class Form extends React.Component {
  state = {
    name: '',
    email: '',
    consent: false,
  }
  handleChange = event => {...}
  handleSubmit = event => {
    event.preventDefault();
    const data = JSON.stringify(this.state);
    fetch('https://myserver.com/submit-form', {
      method: 'post',
      body: data,
    });
    this.setState({ name: '', email: '', consent: false });
  }
  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        ...
      </form>
    );
  }
}
```

#### Final note:

You may have noticed we've been using `htmlFor` on our labels, instead of `for`. This is another JSX difference you just have to remember—`for` is a reserved word in JS so we can't use it.

## Exercise

<<<<<<< HEAD
1. Open `index.html` in your editor and browser.
2. Open your browser console and then play around with the inputs. See what values get logged.
3. Try removing the `onChange` prop from an input. See how you can't type anything anymore?
3. Try logging different things, like the actual events—you'll see that React events are a bit different to built-in DOM events.
=======
1.  Open `index.html` in your editor and browser.
2.  Open your browser console and then play around with the inputs. See what values get logged.
3.  Try logging different things, like the actual events—you'll see that React events are a bit different.
>>>>>>> Fix multiple input example code

## Further Reading

- [Forms | React Docs](https://reactjs.org/docs/forms.html)
