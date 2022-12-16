# Declare Event-listening Elements
An extension to native JavaScript which enables any element in a marked-up document to declare which event listeners are currently attached to it.
________

You can extend the native JavaScript `EventTarget.addEventListener()` method so that *any* element to which you add an `EventListener` will then declare that `EventListener` within its own markup, in an **HTML5 custom data-\* attribute**.

When declared, the **custom attribute** will look like this:

    data-eventlisteners="['mouseover:showButton','mouseout:fadeButton','click:animateButton']"

Once one or more elements in a document have such **custom attributes**, you may query the elements via JavaScript.

**e.g.**

 1. `document.querySelectorAll('[data-eventlisteners]')` will reveal which elements on the page have `EventListeners` attached

 2. `document.querySelectorAll('[data-eventlisteners*=","]')` will reveal which elements on the page have more than one `EventListener` attached

 3. `document.querySelectorAll('[data-eventlisteners*="mouseover:"]')` will reveal which elements on the page have a `mouseover` `EventListener` attached

 4. `document.querySelectorAll('[data-eventlisteners*="click:"][data-eventlisteners*="mouseout:"]')` will reveal which elements on the page have *both* a `click` *and* a `mouseout` `EventListener` attached

etc.

_______

## Short version:
The **short version** will set attributes like this:

    data-eventlisteners="['mouseover:showButton','mouseout:fadeButton','click:animateButton']"

```js
const declareEventListeners = () => {

  EventTarget.prototype._addEventListener = EventTarget.prototype.addEventListener;

  EventTarget.prototype.addEventListener = function(eventType, eventFunction, eventOptions) {
  
    // REINSTATE ORIGINAL FUNCTIONALITY FOR addEventListener() METHOD
    let _eventOptions = (eventOptions === undefined) ? false : eventOptions;
    this._addEventListener(eventType, eventFunction, _eventOptions);
   
    // THEN, IF EVENTTARGET IS NOT WINDOW OR DOCUMENT
    if (this.nodeType === 1) {
      let eventAction = eventFunction.name || 'anonymousFunction';
      let eventListenerLabel = `${eventType}:${eventAction}`;
      let eventListenerLabelsArray = (this.dataset.eventlisteners) ? JSON.parse(this.dataset.eventlisteners.replaceAll( "'", '"')) : [];
      eventListenerLabelsArray.push(eventListenerLabel);
      let eventListenerLabelsString = JSON.stringify(eventListenerLabelsArray).replaceAll('"', "'");
      this.dataset.eventlisteners = eventListenerLabelsString;
    }
  }
};
```

_______

## Long version (as used in Ashiva):


_______

**N.B.** I've modified the script above quite a lot but it was originally inspired by:

 - [Javascript - Listing active event listeners on a Web page | SQLPac | (2020-04-10)](https://www.sqlpac.com/en/documents/javascript-listing-active-event-listeners.html)
