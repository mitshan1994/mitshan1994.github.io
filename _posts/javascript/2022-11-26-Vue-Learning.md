---
layout: post
title:  "Vue Learning Notes"
categories: javascript
---

### Single-File Component (SFC)
A Vue SFC encapsulates the component's logic (JavaScript), template (HTML), and style (CSS) in a single file. Sample:
```
<script>
export default {
  data() {
    return {
      count: 0
    }
  }
}
</script>

<template>
  <button @click="count++">Count is: {{ count }}</button>
</template>

<style scoped>
button {
  font-weight: bold;
}
</style>
```

### API Styles
#### Options API
With options API, we define a component's logic using an object of options such as `data`, `methods`, `mounted`. Properties defined by options are exposed on `this` inside functions.

#### Composition API
With composition API, we define a component's logic using imported functions. For example:
```html
<script setup>
import { ref, onMounted } from 'vue'

// reactive state
const count = ref(0)

// functions that mutate state and trigger updates
function increment() {
  count.value++
}

// lifecycle hooks
onMounted(() => {
  console.log(`The initial count is ${count.value}.`)
})
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```

### Attribute Binding
In Vue, mustaches are only used for text interpolation. To bind an attribute to a dynamic value, we use `v-bind` directive. A **directive** is a special attribute that starts with the `v-` prefix. Directive values are Javascript expressions that have access to the component's state.

The part after the colon (`:id`) is the "argument" of the directive. Because `v-bind` is used so frequently, it has a dedicated shorthand syntax:
```html
<div :id="dynamicId"></div>
```

### Event Listening
We can listen to DOM event using `v-on` directive:
```html
<button v-on:click="increment">{{ count }}</button>
```
Due to its frequent use, `v-on` also has a shorthand syntax:
```html
<button @click="increment">{{ count }}</button>
```
Here, `increment` is referencing a function declared in `<script setup>`.

### Form Bindings
Using `v-bind` and `v-on` together, we can create two-way bindings on form input elements:
```html
<input :value="text" @input="onInput">

function onInput(e) {
  // A v-on handler receives the native DOM event as the argument.
  text.value = e.target.value;
}
```

To simplify two-way bindings, Vue provides a directive, `v-model`, which is essentially a syntax suger for the above:
```html
<input v-model="text">
```
`v-model` automatically syncs the `<input>`'s value with the bounded state, so we no longer need to use an event handler for that.
