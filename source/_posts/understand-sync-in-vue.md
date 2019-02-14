---
title: Understand .sync in Vue
categories: frame
tags:
  - vue
  - sync
comments: true
date: 2018-10-05 09:54:17
---

## Preface

The first time I met `.sync` modifier, I didn't know it very well. So, I seldom use that. Today, I am going to get it.

## Main

In the past, I use “two-way binding” like this:

```html
<div class="app" id="app">
  <button-counter :child-count="parCount" @add="add"></button-counter>
  <p>parent component {{parCount}}</p>
</div>
```

```js
let app = new Vue({
  el: '#app',
  data: {
    parCount: 0
  },
  methods: {
    add() {
      this.parCount++
    }
  },
  components: {
    'button-counter': {
      template:
        '<button @click="add">You clicked me {{ childCount }} times.</button>',
      props: ['childCount'],
      methods: {
        add() {
          this.$emit('add')
        }
      }
    }
  }
})
```

It was easy to understand except a little inconvenient. I need to listen and handle event in child and parent component. Also

_true two-way binding can create maintenance issues, because child components can mutate the parent without the source of that mutation being obvious in both the parent and the child._

So, Vue recommends emitting events in the pattern of `update:myPropName`. For example:

```html
<div class="app" id="app">
  <button-counter
    :child-count="parCount"
    @update:child-count="parCount=$event"
  ></button-counter>
  <p>parent component {{parCount}}</p>
</div>
```

```js
let app = new Vue({
  el: '#app',
  data: {
    parCount: 0
  },
  methods: {},
  components: {
    'button-counter': {
      template:
        '<button @click="add">You clicked me {{ childCount }} times.</button>',
      props: ['childCount'],
      methods: {
        add() {
          this.$emit('update:child-count', this.childCount + 1) // child-count is right while childCount won't work
        }
      }
    }
  }
})
```

See? In this case, we don't have to add event callback in parent component because vue have done that. And this is the principle of `.sync`. For convenience, Vue offers a shorthand for this pattern with the `.sync` modifier which would make the code above like:

```html
<div class="app" id="app">
  <button-counter :child-count.sync="parCount"></button-counter>
  <p>parent component {{parCount}}</p>
</div>
```

```js
let app = new Vue({
  el: '#app',
  data: {
    parCount: 0
  },
  methods: {},
  components: {
    'button-counter': {
      template:
        '<button @click="add">You clicked me {{ childCount }} times.</button>',
      props: ['childCount'],
      methods: {
        add() {
          this.$emit('update:childCount', this.childCount + 1) // childCount is right while child-count won't work
        }
      }
    }
  }
})
```

Also,

_The .sync modifier can also be used with v-bind when using an object to set multiple props at once:_

```html
<text-document v-bind.sync="doc"></text-document>
```

_This passes each property in the `doc` object (e.g. `title`) as an individual prop, then adds `v-on` update listeners for each one._

For more information, go [Vue.js](https://vuejs.org/v2/guide/components-custom-events.html#sync-Modifier)

## Ending

## Reference
