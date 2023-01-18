---
sidebar_position: 1
name: "Summary"
desc: "Simplified explanation of DMIx"
---

# DMIx

:::info
Code snippets used in this document are not actually functional.
These are merley summarative descriptions of how it works.
:::


### Blocking
Blocking the original DeltaMath main.js file.
This way, the original main.js doesn't load, and we'll be able to inject our modifiable main.js.
(Using DeclarativeNetRequest rules).
```json
"action": {
    "type": "block"
},
"condition": {
    "urlFilter": "*://*deltamath.com/app/main*js",
    "resourceTypes": ["script"]
}
```

### CSP Removal
Removing CSP headers from DeltaMath domains.
While it poses a minor security vulnerability, this is necesary to inject our modifiable main.js.
(Using DeclarativeNetRequest rules).
```json
"modifyHeaders",
"responseHeaders": [
    {
        "header": "Content-Security-Policy",
        "operation": "remove"
    } /* ...and X-frame headers, too */
"condition": {
    "urlFilter": "*://*deltamath.com/*",
    "resourceTypes": [/* All */]
}
```

### Get main.js URL
DeltaMath's main.js URL changes whenever it's updated.
To be able to fetch the latest main.js, use RegEx to find the main.js url from the DeltaMath website.
(Content script on DeltaMath domains).
```js
const website = await (await fetch("https://www.deltamath.com/app/")).text();
const MainJS_URL = "https://www.deltamath.com/app/" + website.match(/main\..{0,40}\.js/);
```

### Fetch DeltaMath code
With the DeltaMath main.js URL now known, we can fetch the file.
(Content script on DeltaMath domains).
```js
const OriginalMainJS = await (await fetch(MainJS_URL)).text();
```

### Apply Patches 
Time for the fun part- modifying the DeltaMath script!
We're going to 
```ts
  /** DeltaMath code that we modify */
  let delta : string = code;
  /** Array of patches we must apply to the DeltaMath code */
  let patches : [string | RegExp, string][] = [];
```
