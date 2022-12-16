# Declare Event-listening Elements
An extension to native JavaScript which enables any element in a marked-up document to declare which event listeners are currently attached to it.
_______

**N.B.** This script was substantially inspired by this post on **SQLPac** (2020-04-10):
 - https://www.sqlpac.com/en/documents/javascript-listing-active-event-listeners.html
________

You can extend the native JavaScript `EventTarget.addEventListener()` method so that *any* element to which you add an `EventListener` will then declare that `EventListener` within its own markup, in an **HTML5 custom data-\* attribute**.

When declared, the **custom attribute** will look like this:

    data-eventlisteners="['mouseover:showButton','mouseout:fadeButton','click:animateButton']"

Once one or more elements have such **custom attributes**, you may query the elements via JavaScript.

 1. This will reveal which elements on the page have `EventListeners` attached:

    `document.querySelectorAll('[data-eventlisteners]')`

 2. This will reveal which elements on the page have more than one `EventListeners` attached:

    `document.querySelectorAll('[data-eventlisteners~=","]')`

 3. This will reveal which elements on the page have a `mouseover` `EventListener` attached:

    `document.querySelectorAll('[data-eventlisteners~="mouseover:"]')`

 4. This will reveal which elements on the page have both a `click` *and* a `mouseout` `EventListener` attached:

    `document.querySelectorAll('[data-eventlisteners~="click:"][data-eventlisteners~="mouseout:"]')`

etc.

_________

## Short version:

```js
EventTarget.prototype._addEventListener = EventTarget.prototype.addEventListener;

EventTarget.prototype.addEventListener = function(eventType, eventFunction, eventOptions) {
  
  // REINSTATE ORIGINAL FUNCTIONALITY FOR addEventListener() METHOD
  let _eventOptions = (eventOptions === undefined) ? false : eventOptions;
  this._addEventListener(eventType, eventFunction, _eventOptions);
   
  // THEN, IF EVENTTARGET IS NOT WINDOW OR DOCUMENT
  if (this.nodeType === 1) {
    let eventAction = eventFunction.name || 'anonymousFunction';
    let eventListenerLabel = `${eventType}:${eventAction}`;
    let eventListenerLabelsArray = (this.dataset.eventlisteners) ? JSON.parse(this.dataset.eventlisteners) : [];
    eventListenerLabelsArray.push(eventListenerLabel);
    let eventListenerLabelsString = JSON.stringify().replaceAll('"', "'");
    this.dataset.eventlisteners = eventListenerLabelsString;
  }
};
```

_____

## Long version (as used in Ashiva):
