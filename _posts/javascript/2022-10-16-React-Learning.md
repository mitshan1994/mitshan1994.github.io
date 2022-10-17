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
