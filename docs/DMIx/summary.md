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
```js
const OriginalMainJS = await (await fetch(MainJS_URL)).text();
```

### Apply Patches 
Time for the fun part- modifying the DeltaMath script!
We're going to run String and RegEx replacements through a mutable string, then make a final one with the result.
```ts
let mutable : string = OriginalMainJS; // Mutable string that we'll modify
let patches : [string | RegExp, string][] = []; // Array of replacements: [from, to]
patches.push(["the timer", "the TIMER"]); // Replace all 'the timer' with 'the TIMER'
patches.push([/alertDialouge\(..{0,100}\)/, "alert('Nope')"]) // Replace alertDialouges with 'Nope' alerts
patches.forEach((from, to) => mutable.replace(from, to); ); // Run replacements
const PatchedFile = `window._.allowEscapingTimed = false;\n` + mutable; // Final patched file
```

### Inject File
Let's finally load in the patched main.js file.
The document onreset dispatchEvent is a great way to inject JS from a string into the webpage.
```js
document.documentElement.setAttribute("onreset", PatchedFile);
document.documentElement.dispatchEvent(new CustomEvent("reset"));
document.documentElement.removeAttribute("onreset");
```

### Load GUI
Now we've got the modified main.js in the page, we can use mods.
However, the main.js doesn't come with any user-friendly way to use the mods.
Let's load in [dGUI](https://github.com/DxltaMath/dGUI).
```js
const url = "https://raw.githubusercontent.com/DxltaMath/dGUI/master/dist/bundle.js";
const dGUI = await (await fetch(url)).text();
inject(dGUI) // then do the same thing that we did with PatchedFile in the Inject File section
```

### Done!
Horray, we've loaded modifiable DeltaMath and a mod menu!