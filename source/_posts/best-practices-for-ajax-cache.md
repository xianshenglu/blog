---
title: Best Practices For Ajax Cache?
categories: js
tags:
  - ajax
  - cache
  - vue
comments: true
date: 2020-04-04 13:57:02
---

In our daily coding, there might be some cases we might need to cache ajax response. For example, APIs like `getAllCenter`, `getAllProvinces` in centers or provinces select. Normally, we would get these data from the server each time. However, from the performance perspective, the best experience is no delay which means no ajax.

Considering the cases when the user's network is not stable and fast, they would need to wait for hundreds of milliseconds or even seconds to see the available centers and provinces. So, for these stable and commonly used data, wouldn't it be better if we only request once and cache the response for future use?

### Solutions Ever Tried In Vue

In Vue.js projects, I used to use the store to cache those data. For example,

```js
// src/api/utilities.js
export async function getAllCenter() {
  return http({
    method: "GET",
    url: "https://api.example.com/getAllCenter",
  });
}
```

```js
// src/store/baseData.js
import { getAllCenter } from "@/api/utilities";
const state = {
  allCenterList: [],
};
const mutations = {
  updateAllCenterList: (state, payload) => (state.allCenterList = payload),
};
const actions = {
  async getAllCenterIfNeeded({ state, commit }, payload) {
    if (state.allCenterList.length) {
      return state.allCenterList;
    }
    const result = await getAllCenter(payload);
    commit("updateAllCenterList", result);
    return result;
  },
};
export default {
  namespaced: true,
  state,
  mutations,
  actions,
};
```

```html
<!-- src/views/pageA.vue -->
<template>
  <el-select>
    <el-option
      v-for="{value,label} in allCenterList"
      :key="value"
      :label="label"
      :value="value"
    ></el-option>
  </el-select>
</template>
```

```js
// src/views/pageA.vue
export default {
  created() {
    this.getAllCenterIfNeeded();
  },
  computed: {
    ...mapState("baseData", ["allCenterList"]),
  },
  methods: {
    ...mapActions("baseData", ["getAllCenterIfNeeded"]),
  },
};
```

You will need to write `actions`, `mutations`, `state` code for the first time. And next time you will need less code.

At the beginning, I didn't find anything bad or worse. Until latest, I find it's not easy to migrate or refactor.

For example,

- If you move to `react`, the `actions`, `mutations`, `state` code would need to refactor.
- If you need to copy those codes to another project, it will be also not easy because
  - the `actions`, `mutations`, `state` might not be the same. And you have to change the store module name `baseData` in multiple places.
  - and you need to modify at least three files, the `api/utilities.js`, `store/baseData.js` and `views/pageA.vue`

So, I am thinking

**Do we have to put those cacheable, stable data in Vue reactive system?**

I think the answer is no and the data need to be reactive is `selectedCenter` , `selectedProvince` not the `allCenterList`, `allProvinceList` etc.

So, I think there is a better solution to handle this question.

### Pure JS Solutions

If we put those data in

```js
// src/api/utilities.js
export const getAllCenterIfNeeded = (() => {
  const prevResponse = [];
  return async (options) => {
    if (prevResponse.length) {
      return prevResponse;
    }
    return http({
      method: "GET",
      url: "https://api.example.com/getAllCenter",
      ...options
    });
  };
}());
```

And the usage code would be

```html
<!-- src/views/pageA.vue -->
<template>
  <el-select>
    <el-option
      v-for="{value,label} in allCenterList"
      :key="value"
      :label="label"
      :value="value"
    ></el-option>
  </el-select>
</template>
```

```js
// src/views/pageA.vue
import { getAllCenterIfNeeded } from "@/api/utilities";
export default {
  data() {
    return {
      allCenterList: [],
    };
  },
  created() {
    getAllCenterIfNeeded().then((result) => (this.allCenterList = result));
  },
};
```

See? Much cleaner! In this way,

- we don't need the `store`, and codes are less.
- also codes in _api/utilities_ are more frame-independent.

However, if we don't use `Object.freeze` in `api/utilities` the `prevResponse` would be rewritten by Vue multiple times which would consumes memory and cpu. But how much would it cost? I haven't tested.

<!--how much memory and cpu would it consume? -->

### About Browser Cache

Also, I was wondering if we can add `cache-control` header to make use of browser cache for those ajax data. Though I thought it was not possible because those data was searched from database which means they have no cache-related headers like `last-modified`, `etag`. So, if we want to enable `cache-control: no-cache` or other cache policies to avoid downloading big data the server or backend may need to set these headers by hand and get those header values from the database or other places.

[**Issue**](https://github.com/xianshenglu/blog/issues/111)

[**Source**](https://github.com/xianshenglu/blog/blob/master/source/_posts/best-practices-for-ajax-cache.md)

## Reference
