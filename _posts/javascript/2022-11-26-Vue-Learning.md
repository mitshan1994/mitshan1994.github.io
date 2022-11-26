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
With composition API, we define a component's logic using imported functions.

