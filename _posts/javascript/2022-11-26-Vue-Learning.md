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

### 'script' vs 'script setup'
If `<script></script>` is used to declare some variables and functions, they need to be exported by `setup()` function inside. But if `<script setup><script>` is used, then manual exporting becomes unnecessary. This feature is used in SFCs.

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
Here, `increment` is referencing a function declared in `<script setup>`. Inline handlers is also supported:
```html
<button @click="count++">{{ count }}</button>
```

#### Event Handler Parameter
The event handler automatically receives the native DOM event object, which triggers the it.

#### Event Modifiers
It's common to call `event.preventDefault()` or `event.setPropagation()` inside event handlers. Vue can set this when binding event handlers, using **event modifiers**, which are postfixs to `v-on` directives. Here are some of them:
* `.stop`
* `.prevent`
* `.self`
* `.capture`
* `.once`
* `.passive`
For example: `<a @click.stop="doThis"> </a>`. And they can be chained one after another.

#### Key Modifiers
Vue allows adding key modifiers to `v-on` or `@` when listening for key events.
```
<input @keyup.enter="submitThis" />
```
Vue provides some common key alias:
* `.enter`
* `.tab`
* `.delete`
* `.esc`
* `.space`
* `.up`
* `.down`
* `.left`
* `.right`
And modifier keys:
* `.ctrl`
* `.alt`
* `.shift`
* `.meta` (This is WIN key on Windows)

For example:
```html
<input @keyup.alt.enter="clear" />
```

`.exact` modifier allows control of exact combination of system modifiers needed to trigger an event.
```html
<button @click.ctrl.exact="onlyCtrlPressedFunc"></button>
```

#### Mouse Modifiers
Some:
* `.left`
* `.right`
* `.middle`

These modifiers restrict the handler to events triggered by a specific mouse button.

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

### Computed Value
Introducing `computed()`. We can create a computed ref that computes its `.value` based on other reactive data sources.

`computed()` expects to be passed a getter function, whose returned value bacomes a `computed ref`. Computed refs are also auto-unwrapped in templates, so you can reference them without `.value` in template expressions.

`computed refs` are cached, and only update when their dependents change.

### Life Cycle Hook
To run code after mount, we can use the `onMount()` function:
```javascript
import { onMounted } from 'vue';

onMounted(() => {
  // Component is now mounted.
})
```
This is called a lifecycle hook.

### Watchers
Sometimes we need to perform some "side effects" reactively, for example when some value changed, to perform an action.
```javascript
watch(variable, calledFunction);
```

### Components
Typically, Vue components nest other components. To use a child component, we first import it:
```javascript
import ChildComp from './ChildComp.vue'
```
Then, we use it in the template as:
```html
<ChildComp />
```

### Props
A child component can accept input from parent via `props`.

First, child component needs to declare the props it accepts:
```javascript
<script setup>
const props = defineProps({
  msg: String
})
</script>
```
Here `defineProps()` is a compile time macro. And now we can pass the props to child like attributes:
```javascript
<ChildComp :msg="myVariable" />
```

### Emits
In addition to receiving props, a child component can also emit events to the parent:
```javascript
// Declare emitted events.
const emit = defineEmits(['response']);

// emit with arguments
emit('response', 'hello');
```
The first argument to `emit()` is the event name. Any additional arguments are passed on to the event listener.

The parent can listen to child-emitted events using `v-on`, and the handler receives the extra argument from the child emit call.

### ref Unwrapping in Templates
Only top-level `ref` properties will be unwrapped automatically in `template`, where a `.value` is unnecessary.

With an exception: when a ref value is the final expression in text interpolation(inside `{{ }}` tag), ref unwrapping can also take place.

### ref Unwrapping in Reactive Objects
When a `ref` is accessed or mutated as a property of a reactive object, it is also automatically unwrapped so it behaves like a normal property.

### HTML Class Binding
Vue has special rules when binding `class` attribute.

#### Binding to Objects
An object can be bound to `class`, where the key is the classname and value is true or false which determines whether the key is present in the `class` attribute. For example:
```html
<div :class="{ active: isAction }"> </div>
```
`:class` directive can co-exist with plain `class` attribute.

### Style Binding
`:style` directive binds an object to HTML style attribute. For example:
```h
const activeColor = ref('red');
const fontSize = ref(30);

<div :style="{ color: activeColor, fontSize: fontSize + 'px'}"> </div>

or

<div :style="{ 'font-size': fontSize + 'px' }"> </div>
```
camelCase keys are recommended.

#### Binding to Arrays
`:style` can be bound to an array of style objects, and they will be merged by Vue.
```html
<div :style="[firstStyleObj, secondStyleObj]"> </div>
```

#### Multiple Values
Multiple values can be provided to a style property as an array, and the last valid one (to browser) will be used.

### Conditional Rendering
Related directives are: `v-if`, `v-else-if`, `v-else`. They can apply to normal html elements, and also to `<template>` element, which serves as an invisible wrapper.

#### `v-if` vs `v-show`
When using `v-if`, the element is created/destroyed when truthiness changes. But when using `v-show`, only CSS `display` property is toggled, and the element is always rendered into DOM.

So, if some element show/hide frequently, `v-show` is better considering performance. And if some element is show/hide and is unlikely to change at runtime, `v-if` is a good option.
