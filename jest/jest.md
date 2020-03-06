```javascript
// JSDom does not support scrollTo on its elements.
// This leads to an error when trying to render components
// which use useRef and scrollTo.
// This code mocks the function.

Object.defineProperty(window.Element.prototype, 'scrollTo', {
    writable: true,
    value: scrollTo
});
```
