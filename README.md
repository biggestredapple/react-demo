This is a collection of simple demos of React.js.

These demos are purposely written in a simple and clear style. You will find no difficulty in following them to learn the powerful library.

## Index

1. [Render JSX](#demo01-render-jsx)
2. [Use JavaScript in JSX](#demo02-use-javascript-in-jsx)
3. [Use array in JSX](#demo03-use-array-in-jsx)
4. [Define a component](#demo04-define-a-component)
5. [this.props.children](#demo05-thispropschildren)
6. [Finding a DOM node](#demo6-finding-a-dom-node)
7. this.state
8. Form
9. Component Lifecycle
10. Ajax
11. Server-side rendering

---

## How to use

First copy the repo into your disk.

```bash
$ git clone git@github.com:ruanyf/react-demos.git
```

Then play with the source files under the repo's demo* directories.

```html
<!DOCTYPE html>
<html>
  <head>
    <script src="../build/react.js"></script>
    <script src="../build/JSXTransformer.js"></script>
  </head>
  <body>
    <div id="example"></div>
    <script type="text/jsx">

      // ** Our code goes here! **

    </script>
  </body>
</html>
```

## Demo01: Render JSX

The template syntax in React is called [JSX](http://facebook.github.io/react/docs/displaying-data.html#jsx-syntax). It is allowed in JSX to put HTML tags directly into JavaScript codes. React.render() is the method which translates JSX into HTML, and renders it into the specified DOM node.

```js
React.render(
  <h1>Hello, world!</h1>,
  document.getElementById('example')
);
```

Please be minded that you have to use `<script type="text/jsx">` to indicate JSX codes, and include `JSXTransformer.js` to actually perform the transformation in the browser.

## Demo02: Use JavaScript in JSX

You could also use JavaScript in JSX. It takes angle brackets (&lt;) as the beginning of HTML syntax, and curly brackets ({) as the beginning of JavaScript syntax.

```js
var names = ['Alice', 'Emily', 'Kate'];

React.render(
  <div>
  {
    names.map(function (name) {
      return <div>Hello, {name}!</div>
    })
  }
  </div>,
  document.getElementById('example')
);
```

## Demo03: Use array in JSX

If a JavaScript variable is array, JSX will implicitly concat all members of the array.

```js
var arr = [
  <h1>Hello world!</h1>,
  <h2>React is awesome</h2>,
];
React.render(
  <div>{arr}</div>,
  document.getElementById('example')
);
```

## Demo04: Define a component

React.createClass() creates a component class, which implements a render method to return an component instance of the class. You don't need to call `new` on the class in order to get an instance, just use it as a normal HTML tag.

```js
var HelloMessage = React.createClass({
  render: function() {
    return <h1>Hello {this.props.name}</h1>;
  }
});

React.render(
  <HelloMessage name="John" />,
  document.getElementById('example')
);
```

Components can have attributes, and you can use `this.props.[attribute]` to access them, just like `this.props.name` of `<HelloMessage name="John" />` is John.

## Demo05: this.props.children

React uses `this.props.children` to access a component's children nodes.

```js
var NotesList = React.createClass({
  render: function() {
    return (
      <ol>
      {
        this.props.children.map(function (child) {
          return <li>{child}</li>
        })
      }
      </ol>
    );
  }
});

React.render(
  <NotesList>
    <span>hello</span>
    <span>world</span>
  </NotesList>,
  document.body
);
```

Please be minded that only if the component has more than one child node, you could take `this.props.children` as an array, otherwise `this.props.children.map` throws a TypeError.

## Demo6: Finding a DOM node

Sometimes you need to reference a DOM node in a component. React gives you React.findDOMNode() to find it.

```js
var MyComponent = React.createClass({
  handleClick: function() {
    React.findDOMNode(this.refs.myTextInput).focus();
  },
  render: function() {
    return (
      <div>
        <input type="text" ref="myTextInput" />
        <input type="button" value="Focus the text input" onClick={this.handleClick} />
      </div>
    );
  }
});

React.render(
  <MyComponent />,
  document.getElementById('example')
);
```

The desired DOM node should have a `ref` attribute, and `React.findDOMNode(this.refs.[refName])` would return the corresponding DOM node. Please be minded that you could do that only after this component has been mounted into the DOM, otherwise you get `null`.

## Demo07: this.state

React thinks of component as state machines, and uses `this.state` to hold component's state, `getInitialState()` to initialize `this.state`, `this.setState()` to update `this.state` and re-render the component.

```js
var LikeButton = React.createClass({
  getInitialState: function() {
    return {liked: false};
  },
  handleClick: function(event) {
    this.setState({liked: !this.state.liked});
  },
  render: function() {
    var text = this.state.liked ? 'like' : 'haven\'t liked';
    return (
      <p onClick={this.handleClick}>
        You {text} this. Click to toggle.
      </p>
    );
  }
});

React.render(
  <LikeButton />,
  document.getElementById('example')
);
```

You could use component attributes to register event handlers, just like `onClick`, `onKeyDown`, `onCopy`, etc. Official Document has all [supported events](http://facebook.github.io/react/docs/events.html#supported-events).

## Demo08: Form

According to React's design philosophy, `this.state` describes the state of component and is mutated via user interactions, and `this.props` describes the properties of component and is stable adn immutable.

Since that, the `value` attribute of Form components, such as &lt;input$gt;, &lt;textarea$gt;, and &lt;option$gt;, is unaffected by any user input. If you wanted to access or update the value in response to user input, you could use the onChange event.

```js
var Input = React.createClass({
  getInitialState: function() {
    return {value: 'Hello!'};
  },
  handleChange: function(event) {
    this.setState({value: event.target.value});
  },
  render: function () {
    var value = this.state.value;
    return (
      <div>
        <input type="text" value={value} onChange={this.handleChange} />
        <p>{value}</p>
      </div>
    );
  }
});

React.render(<Input/>, document.body);
```

More information on [officail document](http://facebook.github.io/react/docs/forms.html).

## Demo09: Component Lifecycle

Components have three main parts of their lifecycle: Mounting, Updating and Unmounting. React provides hooks into these lifecycle part. `will` methods are called right before something happens, and `did` methods which are called right after something happens. 

```js
var Hello = React.createClass({
  getInitialState: function () {
    return {
      opacity: 1.0
    };
  },

  componentDidMount: function () {
    this.timer = setInterval(function () {
      var opacity = this.state.opacity;
      opacity -= .05;
      if (opacity < 0.1) {
        opacity = 1.0;
      }
      this.setState({
        opacity: opacity
      });
    }.bind(this), 100);
  },

  render: function () {
    return (
      <div style={{opacity: this.state.opacity}}>
        Hello {this.props.name}
      </div>
    );
  }
});

React.render(
  <Hello name="world"/>,
  document.body
);
```

## Demo10: Ajax

Fetch data in componentDidMount. When the response arrives, store the data in state, triggering a render to update your UI.

```js
var UserGist = React.createClass({
  getInitialState: function() {
    return {
      username: '',
      lastGistUrl: ''
    };
  },

  componentDidMount: function() {
    $.get(this.props.source, function(result) {
      var lastGist = result[0];
      if (this.isMounted()) {
        this.setState({
          username: lastGist.owner.login,
          lastGistUrl: lastGist.html_url
        });
      }
    }.bind(this));
  },

  render: function() {
    return (
      <div>
        {this.state.username}'s last gist is
        <a href={this.state.lastGistUrl}>here</a>.
      </div>
    );
  }
});

React.render(
  <UserGist source="https://api.github.com/users/octocat/gists" />,
  document.body
);
```

## Demo11: Server-side rendering

Please visit [here](https://github.com/mhart/react-server-example).

## Extras

### Precompiling JSX

All above demos don't use JSX compilation for clarity. In production environment, ensure to precompile JSX files before putting them online.

First, install the command-line tools.

```bash
$ npm install -g react-tools
```

Then precompile your JSX files(.jsx) into JavaScript(.js).

```bash
$ jsx -x src/ build/
```

Put the compiled JS files into HTML.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Hello React!</title>
    <script src="build/react.js"></script>
    <!-- No need for JSXTransformer! -->
  </head>
  <body>
    <div id="example"></div>
    <script src="build/helloworld.js"></script>
  </body>
</html>
```

## Useful links

- [React's official site](http://facebook.github.io/react)
- [React's official examples](https://github.com/facebook/react/tree/master/examples)
- [React JS Tutorial and Guide to the Gotchas](https://zapier.com/engineering/react-js-tutorial-guide-gotchas/), by Justin Deal
- [React Primer](https://github.com/BinaryMuse/react-primer), by Binary Muse
- [jQuery versus React.js thinking](http://blog.zigomir.com/react.js/jquery/2015/01/11/jquery-versus-react-thinking.html), by zigomir

## License

BSD licensed
