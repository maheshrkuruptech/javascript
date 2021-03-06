# React

## What is React ? 
React is a JS  library and gives a convenient way to manipulate DOM. We could replace just plug-in the view layer (use it as a view library)  if its really needed. 
In general , React has a tendency to make us change the way we think about MVC Architecture. 

## React in MVC Architecture and how react is different (in React terms).
DOM sends events to react  and fire events to components ; components are the view of the application. 
Components can also replace the controller. Components are the class we write for the react.  
Components send this to model layer. Its the section handles the business logic. It could be a simple class component or we could make it as a Redux or Flux or (yeah!! We have to bring in a model library)any such library calling server side APIs and get back the data and sets the state of the component.
Once the state object gets updated , then the component gets re-rendered. And then they gets updated to the shadow DOM; 
React library takes a diff and update/sync  only the changed element in the DOM. 

In this architecture  , components are meant to do little things efficiently adhering to SRP. 

## JSX

Its only understood by transpilers , and more  like HTML  than using React.createElement.  
Note : JSX treats elements with lower case as HTML elements and upper case as functions and components and then calls render method.
and then ReactDOM.render(<AppCompoenent/> , the element);


## What is Shadow DOM
Shadow DOM is a hierarchy of objects React maintains , which will have both components and HTML elements. It’s a model of what we eventually want in the page. This hierarchy will help to ensure that the model is not directly accessible by the child components. It’s passed in by the parents. All of these Virtual DOM / Shadow DOM everything is in the memory. When a user action happens and the data changes  , the current component and all the child components will go for a re-render.  It results in a new  shadow DOM. Then react will merge in all the diff to real DOM. And the custom components will dis-appear. Merging is real performant and avoid the need to do DOM manipulation. 
But its always possible to do direct control , but thinking in REACT way will make it redundant. 


## Component Class
Component is how we interact with react. The only obligation is to create a method called render. Render method returns the shadow DOM. And whats written is only a hierarchy of JS Objects.  React provide some helper methods to construct shadow DOM. Components let you split the UI into independent, reusable pieces, adhering to SRP. 

Conceptually, components are like JavaScript functions. They accept arbitrary inputs (called “props”) and return  React elements describing what should appear on the screen.
Tip  : Recommend naming props from the component’s own point of view rather than the context in which it is being used.


eg: React.createElement(elementType , attributes , data) ; 
The Class should start with an upper case. Then extends React.Component.  It is using Prototype inheritance behind the scenes. 

Components uses “top-down” or “unidirectional” data flow. Any state is always owned by some specific component, and any data or UI derived from 
that state can only affect components “below” them in the tree.

## React DOM

To hook into the DOM is when react_dom comes to picture. To render , ReactDOM.render ; two params , first is a shadowDOM. ( React.createElement(elementType , attributes , data) ;)It could also pass a component and the attributes. When passing the component , its the reference of the class , which is JS function. 
Second argument is the DOM element , the component needs to be hooked to. **React elements are immutable**. Once you create an element, you can’t change its children or attributes. An element is like a single frame in a movie: it represents the UI at a certain point in time.


## State

Whether you declare a component as a function or a class, it must never modify its own props. ie, it should be PURE.

All React components must act like pure functions with respect to their props. And the update should happen via State. Doesnt mean ,we can do this.state.x = abc. This doesnt work. Its done via setState , by a shallow merge. By doing so , it will initiate a re-render.  

State is nothing more than a bag of properties just that its understood only by React. If props or state changes due to event or anything , 
React will re-render the component and all child and then merge to shadow DOM. Re-render is nothign but calling render function. 

Default state is set by the constructor. and set the state object. Always pass props to constructor and pass that along to the super constructor.  And this is invoked only when ReactDOM.render is invoked. Constructor should be the only place where we should set the state(initial). 

Extract state using object de-structure syntax ; in render. React may batch multiple setState() calls into a single update for performance.
Because this.props and this.state may be updated asynchronously, you should not rely on their values for calculating the next state.

For example, this code may fail to update the counter:

```
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

To fix it, use a second form of setState() that accepts a function rather than an object. That function will receive the previous state as the first argument, and the props at the time the update is applied as the second argument:

```
// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));

```

## Events

We can create custom events; but there are certain restrictions on the events to be used.

But when the event happens and the function get invoked , if call like {this.test} ; then the issue is the function , which access the state like
this.setState ,  cant access **this** keyword. Since this is undefined.

Two ways to handle , one is to  write an anonymous function ; other one is to use fat arrow {() => this.test} ; arrow function will capture 
in static scope and bind anonymous function to this. 

**Issue** : Performance ; when state or props change , there will trigger a re-render and it creates a **closure** , could result in memory leak.

One potential solution {this.test.bind(this)}; every time calling bind , it will create another function , might have the same issue as above. 

So bind this in the constructor usign this.test.bind(this). No matter what the dynamic context is , it will bind and make it available. 

Events are abstrataion over browser events. React events are synthetic events.





## Stateless Component
Its a function takes in prop and render shadow DOM.Doesnt have a state. So it accepts certain props and only render;still they can handle events. Stateless Components will be the view and Stateful components can be used to represent the model. 
They are PURE components by definition.
Always add a key to the list elements so that React knows which element to update. 
```
function TodoList(props) {
    return (<ul><li></li></ul>);
}
```

### Handle events / state in stateless component from stateful component. 
Say Shadow DOM is in stateless component and not in the stateful. This can be managed by 
creating a function in the stateful component and then pass it as a delegate. 
Then on any event on stateless function , the passed in function can be used. 
(It creates a closure) and any action will trigger the function passed in, 
which is owned by the parent stateful component.

If the stateless component is a list of checkbox or so , then the idea approach to do setState is 
in an immutable manner. 

```
function StatelessComponent({prpName}) {
    return (
        <test onclick = {e => prpName(element, e.trget.checked)}}>
    )
}
<StatelessComponent prpName = {this.__setStatus}>

</StatelessComponent>


__setStatus(listItem , changedData) {
 const {listData} = this.state;
 const newList = listData.map(old => {
     if (old.id != listItem.id)
            return old;
    return Object.assign({} , old , {element : changedData})
});
this.setState({listData : newList});
```


### How to reach out to DOM element from React. 
    When filling forms , react should reachout to HTML and get the value. v
    Proper way to get the reference to actual DOM element is to use ref element and then use a closure.
    This should happen in the render .
    ```
    {input => this.__methodName  = input} ; 

    onSubmit {
        const me = this.__methodName.value;
    }
    ```
    Another use case is say to bring focus on a text field on page load. 
    ```
    <Component>
      focusInput() {
          this.__text.focus();
      }
    </Component>
    <Component ref = {form => this__Component = form}/>

    componentDidMount() {
        this.Component.focusInput();
    }
    ```
### PropTypes 
Way to validate props , mention mandatory vs Optional. 
```
Components.propTypes = {
    onABC : React.propType.func.isRequired
}
```    

## Build Process

A JavaScript build toolchain typically consists of:

A package manager, such as Yarn or npm. It lets you take advantage of a vast ecosystem of third-party packages, and easily install or update them.
A bundler, such as webpack or Parcel. It lets you write modular code and bundle it together into small packages to optimize load time.
A compiler such as Babel. It lets you write modern JavaScript code that still works in older browsers.React uses  ES2015 / ES 2016 , class like syntax and also created JSX. 
Idea of JSX is to use HTML like syntax in react. This needs a transpiler for web application to understand , hence Babel.


### Using create-react-app

Install node js
install webpack cli
Install create-react-app



### Long way
 
Install node js
Install webpack
npm init
Install webpack-cli and webpack-dev-server as dev dependency
Install path package. 
npm install path html-webpack-plugin --save-dev

npm install --save-dev @babel/core @babel/node @babel/preset-env @babel/preset-react babel-loader

Babel is to tell how to compile react code . 

a. @babel /core: used to compile ES6 and above to ES5.

b. @babel /preset-env: determines which transformations or plugins to use and polyfills (i.e it provides modern functionality on older browsers that do not natively support it) based on the browser matrix you want to support.

c. @babel /preset-react: compiles the React code into ES5 code.

d. babel-loader: a Webpack helper that transforms your JavaScript dependencies with Babel (i.e. will transform the import statements into require ones)

npm install style-loader css-loader sass-loader node-sass image-webpack-loader --save-dev 

a. style-loader: will add styles to the DOM (injects a style tag inside the HTML file).

b. css-loader: lets us import CSS files in our project.

c. sass-loader: lets us import SCSS files in our project.

d. node-sass: compiles SCSS files into normal CSS files.

e. image-webpack-loader: lets us load images in our project.

To work with class and OOP ; 
npm install file-loader @babel/plugin-proposal-class-properties --save-dev


npm install react react-dom --save


Create webpack.config.js
```
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
    entry: path.join(__dirname, "src", "index.js"),
    output: { path: path.join(__dirname, "build"), filename: "index.bundle.js" },
    mode: process.env.NODE_ENV || "development",
    resolve: { modules: [path.resolve(__dirname, "src"), "node_modules"] },
    devServer: { contentBase: path.join(__dirname, "src") },
    module: {
        rules: [
            { 
                test: /\.(js|jsx)$/, 
                exclude: /node_modules/, 
                use: ["babel-loader"] 
            },
            {
                test: /\.(css|scss)$/,
                use: ["style-loader", "css-loader"],
            },
            { 
                test: /\.(jpg|jpeg|png|gif|mp3|svg)$/,
                use: ["file-loader"] 
            },
        ],
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: path.join(__dirname, "src", "index.html"),
        }),
    ],
};
```

Create .babelrc and add follwing content
```
{
    "presets": [
        "@babel/env",
        "@babel/react"
    ],
    "plugins": [
        "@babel/plugin-proposal-class-properties"
    ]
}
```

npm run webpack

npm start


## Simplest way to start 

With legacy html , include react js  , react dom , babel browser.js to get started .And the js file with ES6 and JSX , add the type as text/babel. 

```
ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
```



 

