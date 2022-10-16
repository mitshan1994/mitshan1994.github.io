---
layout: post
title:  "React Learning Notes"
categories: javascript
---

A **React Component** takes in parameters, known as **props**, and return a hierarchy of views by **render()** to display contents in browser.

When needing data from multiple children components, or they need to communicate with each other, the parent component can declare shared state and pass it to children through props. In this way, state are in sync among them.

It's strongly recommended to specify **key** property, when building dynamic lists. React will use elements' **key** to compare between renderings. Components may be added for a new **key**, and moved for an existing key before, and destroyed if the key does not show up. If no key is specified, index in the array will be used, and this is problematic in some situations.
