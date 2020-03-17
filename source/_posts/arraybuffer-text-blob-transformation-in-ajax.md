---
title: arrayBuffer, text, and Blob Transformation in Ajax
categories: js
tags:
  - arrayBuffer
  - Blob
  - fetch
  - XMLHttpRequest
  - Excel
comments: true
date: 2019-09-04 10:43:12
---

In some cases, we might need to fetch and handle some unusual data from a server, for example, Excel file, arrayBuffer, etc. If the backend has a united error handler things can be more complicated. In that case, the response got from backend can be a json, arrayBuffer or other types. For example,

- Normally the frontend would get a Excel file
- If some error happens in the backend or formData is not valid or the session expired, the backend would return a json like

```js
{
  message: "unknown error!",
  status: 500
};
```

So, how can we handle the unknown response type in ajax?

### `fetch`

If we are using `fetch`, the code can be like

```js
import { saveAs } from "file-saver";

fetch("http://www.your-api.com/getExcel", {
  credentials: "include",
  headers: {
    accept: "application/json, text/plain, */*",
    "content-type": "application/json;charset=UTF-8"
  },
  referrer: "",
  referrerPolicy: "no-referrer-when-downgrade",
  body: "{}",
  method: "POST",
  mode: "cors"
})
  .then(response => {
    return response
      .clone()
      .json()
      .catch(er => {
        return response.blob();
      });
  })
  .then(response => {
    if ({}.toString.call(response) !== "[object Blob]") {
      return alert(response.message);
    }
    return saveAs(
      new Blob([response], {
        type:
          "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet;charset=utf-8"
      }),
      "file.xls"
    );
  });
```

The key is the [Response](https://developer.mozilla.org/en-US/docs/Web/API/Response) api which can help us transform unknown data to json or blob or other types. However, you might need to take care of the [`clone`](https://developer.mozilla.org/en-US/docs/Web/API/Response/clone) api if you want to use the `Response` more than once.

### `XMLHttpRequest`

If you are using `xhr`(i.e. `XMLHttpRequest`), you have to set the response type before sending the request. For example,

```js
const xhr = new XMLHttpRequest();
xhr.open("POST", "http://www.your-api.com/getExcel");
xhr.responseType = "blob";
xhr.withCredentials = true;
xhr.setRequestHeader("Content-Type", "application/json");
xhr.onreadystatechange = e => {
  if (xhr.readyState === 4) {
    console.log(xhr.response);
  }
};
xhr.send('{"truck_team_id":[]}');
```

And the type of `xhr.response` is decided by `xhr.responseType` which means we will get a blob object with `xhr.responseType = "blob"` even if the server returns json. In this case, we might need to know some ways to transform data between blob, text, arrayBuffer, etc.

Luckily, the [Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob#Methods) api gives us many choices. The transformation between arrayBuffer, blob and text can be

```js
// arrayBuffer => text;

new Blob([arrayBuffer]).text(result => {
  console.log(result); //text
});
```

```js
// text => arrayBuffer;

new Blob([text]).arrayBuffer(result => {
  console.log(result); // arrayBuffer
});
```

And if not using `Blob`, we can still use `TextDecoder` and `TextEncoder` to do the transformation job between arrayBuffer and text.

```js
// arrayBuffer to text
new TextDecoder().decode(arrayBuffer);
//  text to arrayBuffer
new TextEncoder().encode(text);
```

### Universality

Considering the universality, the transformation between arrayBuffer, blob and text can be useful in many scenarios. Worthy of attention!

[**Issue**](https://github.com/xianshenglu/blog/issues/101)

[**Source**](https://github.com/xianshenglu/blog/blob/master/source/_posts/arraybuffer-text-blob-transformation-in-ajax.md)

## Reference
