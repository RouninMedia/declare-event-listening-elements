# Declare Event-listening Elements
An extension to native JavaScript which enables any element in a marked-up document to declare which event listeners are currently attached to it.
________

You can extend the native JavaScript `EventTarget.addEventListener()` method so that *any* element to which you add an `EventListener` will then declare that `EventListener` within its own markup, in an **HTML5 custom data-\* attribute**.

When declared, the **custom attribute** will look like this:

    data-eventlisteners="['mouseover:showButton','mouseout:fadeButton','click:animateButton']"

Once one or more elements in a document have such **custom attributes**, you may query the elements via JavaScript.

 1. This will reveal which elements on the page have `EventListeners` attached:

    `document.querySelectorAll('[data-eventlisteners]')`

 2. This will reveal which elements on the page have more than one `EventListener` attached:

    `document.querySelectorAll('[data-eventlisteners~=","]')`

 3. This will reveal which elements on the page have a `mouseover EventListener` attached:

    `document.querySelectorAll('[data-eventlisteners~="mouseover:"]')`

 4. This will reveal which elements on the page have both a `click EventListener` *and* a `mouseout EventListener` attached:

    `document.querySelectorAll('[data-eventlisteners~="click:"][data-eventlisteners~="mouseout:"]')`

etc.

_________

## Short version:

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

const clickMe = (e) => {
  e.target.classList.toggle('circle');
}

const mouseoverMe = (e) => {
  e.target.style.setProperty('background-color', 'rgb(255, 127, 0)');
}

const mouseoutMe = (e) => {
  e.target.removeAttribute('style');
}

const logMarkup = () => {
  
  console.log(document.querySelector('section').innerHTML);
}

declareEventListeners();
document.querySelector('.div1').addEventListener('click', clickMe, false);
document.querySelector('.div2').addEventListener('mouseover', mouseoverMe, false);
document.querySelector('.div2').addEventListener('mouseout', mouseoutMe, false);
logMarkup();
```

```css
.div1,
.div2 {
  float: left;
  width: 100px;
  height: 100px;
  line-height: 50px;
  margin-right: 12px;
  text-align: center;
  color: rgb(255, 255, 255);
  background-color: rgb(255, 0, 0);
}

.div1 {
  line-height: 100px;
  cursor: pointer;
}

.div1.circle {
  border-radius: 50%;
}
```

```html
<section>
  <div class="div1">click</div>
  <div class="div2">mouseover<br />mouseout</div>
</section>
```

_____

## Long version (as used in Ashiva):


_______

**N.B.** The script above has been modified quite a lot but was originally inspired by:

 - [Javascript - Listing active event listeners on a Web page | SQLPac | (2020-04-10)](https://www.sqlpac.com/en/documents/javascript-listing-active-event-listeners.html)
