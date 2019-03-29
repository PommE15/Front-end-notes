# Front-end notes

# DOM (document object model)
`Document, Element, NodeList, Attribute`

# Javascript
## Event
### Terms: delegation, bubbling vs capturing

**Event delegation** sets the event listener to a parent node/element. Most events **bubble** up through the DOM with few exception, ex. `focus`. **Capturing** uses **event delegation** on events that don't **bubble**.

- **delegation** (代表) - set the event listener on a parent of a group of child elements, ex. inputs in a form or a list of itmes. click on one of child elements and have events that happen on them bubble up to their parent, rather than having to set the event listener on every child individually.
- **bubbling** - An event starts from the element that triggered it. Then it bubbles up to all parents until the `<html>`.
- **capturing** - ...

But usually you want to turn all capturing and bubbling off to keep functions from interfering with each other, especially when the document structure is very complex, ie. lots of nested tables, you may save system resources by turning off bubbling. 

- `preventDefault()` to prevent its default action but allow propagation to continue.
- `stopPropagation()` - to stop bubbling the event, propagation (傳播)
- `stopImmediatePropagation()` to stop other listeners of the same event from being called. If several listeners are attached to the same element for the same event type, they are called in the order in which they were added. If `stopImmediatePropagation()` is invoked during one such call, no remaining listeners will be called

### Event phases
There are [3 event phases @w3](https://www.w3.org/TR/DOM-Level-3-Events/): **capture**, **target** and **bubble** phases. A phase will be skipped if it is not supported, or if the event object’s propagation has been stopped. For example, if the bubbles attribute is set to false, the bubble phase will be skipped, and if `stopPropagation()` has been called prior to the dispatch, all phases will be skipped.

1. **capture** (top-down) - The event object propagates from the `Window` to the target’s parent.
2. **target** - The event reaches the target itself.
3. **bubble** (bottom-up)- The event object propagates from the target’s parent back to the `Window`.

(top) `Window` -> `Document` -> `<html>` -> `<body>` -> `...` (bottom)


### Ways of using events
Here are [3 mechanisims of event handler @mdn](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events):
```
// 1. Event handler properties
btn.onclick = btnClick;                     

// 2. HTML event handler attribute, don't use this
<button onclick="btnClick"></button>        

// 3. DOM Level 2 Events: add/removeEventListener
btn.addEventListener('click', btnClick)     
btn.removeEventListener('click', btnClick)
```

**HTML event handler attribute** is not recommended as separating programming logic from content makes a site more friendly to search engines.
**Event handler properties** have less power and options, but better cross-browser compatibility.
**DOM Level 2 Events: add/removeEventListener** can remove event handler code if needed, and you can add multiple listeners of the same type to elements if required.

```
myElement.onclick = functionA;  // replaced by B
myElement.onclick = functionB;

myElement.addEventListener('click', functionA); // both exist
myElement.addEventListener('click', functionB);
```

## Debounce and Throttle
[Debounce and Throttle: a visual explanation](http://drupalsun.com/david-corbacho/2012/10/10/debounce-and-throttle-visual-explanation)

Use Lodash custom builder to output a custom 2KB minified library of the _.debounce and _.throttle functions
```
npm i -g lodash-cli
lodash include = debounce, throttle
```

### debounce (once)
- "group" multiple sequential calls in a single one
-  guarantees that the function will only be executed a single time (given a specified threshhold), for example, the user has finished scrolling or typing a word
- `leadin` / `immediate` and `traling` option

use cases: 
- **window resize**
- **keypress on autocomplete form with Ajax request**
- **auto save**

### throttle (regular update)
- execute once at least every X milliseconds 
- guarantees the execution of the function regularly, but not (way) too often

use cases: 
- **Infinite scrolling**, 
- **mouse movements**

### requestAnimationFrame (a throttle alternative)
use cases:
- recalculates and renders elements on screen 
- guarantee smooth changes or animations

### queueing
[Debounce vs Throttle vs Queue execution](https://medium.com/ghostcoder/debounce-vs-throttle-vs-queue-execution-bcde259768)

## React.js
- Toggle, slider and checkbox using button, input: range, checkbox
- Grid and flex layout, position
- [sandbox](https://codesandbox.io/s/03m9l56mp0)

## Basics
### var, let, and const
|         | scope                                     | redeclear | update |
|---------|-------------------------------------------|-----------|--------|
| `var`   | global or local in function               | O         | O      | 
| `let`   | [{} block: block, statement, or expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)      | X         | O      | 
| `const` |                                           | X*        | X*     | 

```
var numVar = 0;     
let numLet = 1;     
const numConst = 2;

// redeclear
let numVar = 0.1;   // -> 0.1
var numLet = 1.1;   // -> Uncaught SyntaxError: Identifier 'numLet' has already been declared

// update
numLet = 1.2;       // -> 1.2
numConst = 2.1;     // -> Uncaught TypeError: Assignment to constant variable.

// mutable cases of const
const arrConst = [0, 1, 2];
const objConst = {a: 0, b: 1};

arrConst[2] = 2.1;  // -> [0, 1, 2.1]
objConst.b = 1.1;   // -> {a: 0, b: 1.1}

Object.freeze(objConst);
objConst.b = 1.2;   // -> 1.2, no *Error and no change, frozen!
objConst;           // -> {a: 0, b: 1.1}

this.numVar;        // -> 0.1
this.numLet;        // -> undefined
this.numConst;      // -> undefined
this.Const;         // -> {a: 0, b: 1.1}

// hoisting
let x = 1;
if (true) {
  var x = 2; // Identifier 'x' has already been declared
}
```
### hoisting
- `var` variable declaration is moved to the top of the function or global code which will start with the value `undefined`
- `let` bindings are created at the top of the (block) scope containing the declaration, otherwise results in a `ReferenceError`
- function declarations in JavaScript are hoisted to the top of the enclosing function or global scope

```
hoisted(); // logs "foo"
function hoisted() {
  console.log('foo');
}

notHoisted(); // TypeError: notHoisted is not a function
var notHoisted = function() {
   console.log('bar');
};
```

### Array and Object 
flatten an array, merge

nodelist to array 
```
nodelist = document.querySelectorAll("div")
nodeArr1 = Array.prototype.slice.call(nodelist) or
nodeArr2 = [].slice.call(nodelist) or
nodeArr3 = [...nodelist] //ES6
```

Copy an array / object / dom
```
arr.slice() or [...arr]
Object.assign({}, obj) or  {...obj} [in ES6]
node.cloneNode(?deep)
```

# CSS
## Layout
Flexbox layout is most appropriate to the components of an application, and small-scale layouts, while the Grid layout is intended for larger scale layouts.

### display: flex
- [FLEXBOX FROGGY](https://flexboxfroggy.com/)
- [A Complete Guide to Flexbox @css-tricks](https://css-tricks.com/snippets/css/a-guide-to-flexbox)

### display: grid
- [A Complete Guide to Grid @css-tricks](https://css-tricks.com/snippets/css/complete-guide-grid)

### block vs inline elements
- [block-level elements](https://developer.mozilla.org/en-US/docs/Web/HTML/Block-level_elements#Elements)
- [inline elements](https://developer.mozilla.org/en-US/docs/Web/HTML/Inline_elements#Elements)
- [block and inline layout in normal flow](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flow_Layout/Block_and_Inline_Layout_in_Normal_Flow)
- [block vs. inline-block vs. inline](https://stackoverflow.com/questions/9189810/css-display-inline-vs-inline-block)
- [content categories]()
