---
layout: post
title:  "React Learning Notes"
categories: javascript
---

A **React Component** takes in parameters, known as **props**, and return a hierarchy of views by **render()** to display contents in browser.

When needing data from multiple children components, or they need to communicate with each other, the parent component can declare shared state and pass it to children through props. In this way, state are in sync among them.

It's strongly recommended to specify **key** property, when building dynamic lists. React will use elements' **key** to compare between renderings. Components may be added for a new **key**, and moved for an existing key before, and destroyed if the key does not show up. If no key is specified, index in the array will be used, and this is problematic in some situations.

**JSX** is an extension to JavaScript in React. JSX produce React "elements". All javascript expressions can be put in the JSX surrounded by curly braces.

**JSX** is compiled to **createElement()** function call, and finally becomes a javascript object. Theses objects are called "React elements". React reads them and constructs DOM and keep it up to date.

### Component
When React sees user-defined element in JSX, it parses the attributes into a single object. We call this object "props".

Component names should start with a captical letter. Lower letter started words will be viewed as a HTML tag.

All react components should act like pure functions with respect to their props.

If you want to disable a component to render, return **null** as the React element (for example in render() function).

#### Function Style Component
A function is called a React Component if it accepts a single "props" argument, and returns a React element (which can be created using JSX).

#### Mounting and Unmounting (Lifecycle Methods)
When a Component is rendered to the DOM for the first time, it's called "mounting" in React. When a Component instance is removed, it's called "unmounting" in React. The following are two special member functions used when "mounting" and "unmounting":
```
class XXX extends React.Component {
	...
	componentDidMount() { ... }
	componentWillUnmount() { ... }
}
```

### States
You shouldn't modify components' state directly. Instead, **this.setState(...)** should be used. React can automatically re-render after the function calls.

#### State Update are Merged
If a field is not appeared in the setState parameter object, it will preserve its current value.

### Events
Event handlers in React are similar to them in h5. But there are some differences.
* Event names are camelcase style, while they are lowercase in h5.
* In JSX, function is passed as event handler, not string.
* "preventDefault" should be called explicitly, while in h5 false can be returned, to prevent the default behaviour.

### Select Forms
In HTML, **\<select\>** is used to create a drop-down list.

### "Source of Truth"
There should be a "source of truth", for any data which may change in React.

### this.props.children
Inside the React component tag, elements will be generated and pass to the component as **props.children**.
