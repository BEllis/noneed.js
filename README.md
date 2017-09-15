# noneed.js

## Motivation

require.js is large and bloated for module loading.
script.js specializes in loading scripts.
The same resource on multiple sites.

## Usage

index.html
```html
<script defer async src="https://mydomain.com/noneed-client-1.0.0.js"></script>
```
noneed.config.js
```javascript
  var noneedConfig = noneedConfig || {};
  noneedConfig.links = noneedConfig.links || [];
  noneedConfig.links.push({ src: 'js/MyPage.js', integrity: "sha512-oqVuAfXRKap7fdgcCY5uykM6+R9GqQ8K/uxy9rx7HNQlGYl1kPzQho1wx4JwY8wC" });
  noneedConfig.links.push({ src: "vendor/jquery/dist/jquery.min.js", integrity: "sha512-oqVuAfXRKap7fdgcCY5uykM6+R9GqQ8K/uxy9rx7HNQlGYl1kPzQho1wx4JwY8wC" });
  noneedConfig.links.push({ src: "vendor/lodash/dist/lodash.min.js"});
  noneedConfig.links.push({ src: "vendor/_reactive/rxjs/dist/rx.min.js", integrity: "sha512-oqVuAfXRKap7fdgcCY5uykM6+R9GqQ8K/uxy9rx7HNQlGYl1kPzQho1wx4JwY8wC"});

  noneedConfig.mappings = noneedConfig.mappings || [];
  noneedConfig.mappings.push({
    target: "jquery",
    map: "vendor/jquery/dist/jquery.min.js"
  });
  noneedConfig.mappings.push({
    target: "*",
    function(target) {
      return "vendor/" + target + "/dist/" + target + ".min.js"
    });

  noneedConfig.entryPoints = noneedConfig.entryPoints || [];
  noneedConfig.entryPoints.push(
    {
      src: "js/index.js",
      dependencies: [
        "js/MyPage.js",
        "jquery",
        "noneed!rx"
      ]
    }
  );

```

## NoNeed dependencies

### How do I defer loading of my modules?

CommonJs and partially AMD require all modules ahead-of-time meaning users are waiting for all the modules to load before they can interact with a website. NoNeed dependencies modules change this by returning an Observable instead of the actualy module you are trying to load.

ES6
```javascript
import * as MyDeferredLibrary from "noneed!MyLibrary";
import { MyClass } from "noneed?MyClass!MyLibrary";
import { MyRequiredClass } from "MyRequiredClass";

// Do Stuff with My Required Class
MyDeferredLibrary.subscribe(result => {
  const MyDeferredClass = result.lib.MyDeferredClass;
  // Do Stuff with MyDeferredClass.
}, (err) => {
  // Handle Error case.
});
```

CommonJs
```javascript
var MyDeferredLibrary = require("noneed!MyLibrary");
var MyRequiredLibrary = require("MyRequiredLibrary");

// Do stuff iwth MyRequiredLibrary
MyDeferredLibrary.retry().subscribe(result => {
  const MyDeferredClass = result.lib.MyDeferredClass;
  // Do Stuff with MyDeferredClass.
}
```

AMD
```javascript
define("noneed!MyLibrary", "MyRequiredLibrary", function(MyDeferredLibrary, MyRequiredLibrary) {
  // Do Stuff with MyRequiredLibrary...
  MyDeferredLibrary.retry(3).subscribe(result => {
    const MyDeferredClass = result.lib.MyDeferredClass;
    // Do Stuff with MyDeferredClass.
  }, (err) => {
    // Handle Error case (after 3 retry attempts.)
  }
});
```
