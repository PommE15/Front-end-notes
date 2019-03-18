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

- `stopPropagation()` - to stop bubbling the event, propagation (傳播)
- `stopImmediatePropagation()` to not call any further listeners for the same event at the same level in the DOM
- `preventDefault()` to allow propagation to continue but to disallow the browser to perform its default action should no listeners handle the event

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

### debounce, t*

