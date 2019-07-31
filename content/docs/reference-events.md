---
id: events
title: SyntheticEvent
permalink: docs/events.html
layout: docs
category: Reference
---
এই রেফারেন্স গাইড `SyntheticEvent` wrapper যা React ইভেন্ট সিস্টেম এর অংশ তা সম্পর্কে ধারণা দেয়। আরও জানতে [হ্যান্ডলিং ইভেন্টস](/docs/handling-events.html) গাইড দেখুন।

## ওভারভিউ {#overview}

আপনার ইভেন্ট হ্যান্ডলার `SyntheticEvent` এর ইন্সট্যান্স আকারে passed হবে, যা পুরো ব্রাউজার এর ন্যাটিভ ইভেন্ট জুড়ে একটি ক্রস ব্রাউজার wrapper। ব্রাউজার এর ন্যাটিভ ইভেন্ট এর মতই এরও একটি ইন্টারফেস আছে, `stopPropagation()` ও `preventDefault()` সহ, শুধু ইভেন্টগুলি সমস্ত ব্রাউজার জুড়ে অভিন্নভাবে কাজ করা বাদে।

যদি আপনি মনে করেন যেকোনো কারণে আপনার ব্রাউজার এর ভিতরগত ইভেন্টগুলি দরকার, কেবলমাত্র `nativeEvent` attribute দিয়েই তা পেয়ে যাবেন। প্রত্যেক `SyntheticEvent` অবজেক্ট এ নিচের attribute গুলি থাকবে।

```javascript
boolean bubbles
boolean cancelable
DOMEventTarget currentTarget
boolean defaultPrevented
number eventPhase
boolean isTrusted
DOMEvent nativeEvent
void preventDefault()
boolean isDefaultPrevented()
void stopPropagation()
boolean isPropagationStopped()
DOMEventTarget target
number timeStamp
string type
```

> নোট:
>
> As of v0.14, returning `false` from an event handler will no longer stop event propagation. Instead, `e.stopPropagation()` or `e.preventDefault()` should be triggered manually, as appropriate.

### Event Pooling {#event-pooling}

The `SyntheticEvent` is pooled. This means that the `SyntheticEvent` object will be reused and all properties will be nullified after the event callback has been invoked.
This is for performance reasons.
As such, you cannot access the event in an asynchronous way.

```javascript
function onClick(event) {
  console.log(event); // => nullified object.
  console.log(event.type); // => "click"
  const eventType = event.type; // => "click"

  setTimeout(function() {
    console.log(event.type); // => null
    console.log(eventType); // => "click"
  }, 0);

  // Won't work. this.state.clickEvent will only contain null values.
  this.setState({clickEvent: event});

  // You can still export event properties.
  this.setState({eventType: event.type});
}
```

> Note:
>
> If you want to access the event properties in an asynchronous way, you should call `event.persist()` on the event, which will remove the synthetic event from the pool and allow references to the event to be retained by user code.

## Supported Events {#supported-events}

React normalizes events so that they have consistent properties across different browsers.

The event handlers below are triggered by an event in the bubbling phase. To register an event handler for the capture phase, append `Capture` to the event name; for example, instead of using `onClick`, you would use `onClickCapture` to handle the click event in the capture phase.

- [Clipboard Events](#clipboard-events)
- [Composition Events](#composition-events)
- [Keyboard Events](#keyboard-events)
- [Focus Events](#focus-events)
- [Form Events](#form-events)
- [Mouse Events](#mouse-events)
- [Pointer Events](#pointer-events)
- [Selection Events](#selection-events)
- [Touch Events](#touch-events)
- [UI Events](#ui-events)
- [Wheel Events](#wheel-events)
- [Media Events](#media-events)
- [Image Events](#image-events)
- [Animation Events](#animation-events)
- [Transition Events](#transition-events)
- [Other Events](#other-events)

* * *

## Reference {#reference}

### Clipboard Events {#clipboard-events}

Event names:

```
onCopy onCut onPaste
```

Properties:

```javascript
DOMDataTransfer clipboardData
```

* * *

### Composition Events {#composition-events}

Event names:

```
onCompositionEnd onCompositionStart onCompositionUpdate
```

Properties:

```javascript
string data

```

* * *

### Keyboard Events {#keyboard-events}

Event names:

```
onKeyDown onKeyPress onKeyUp
```

Properties:

```javascript
boolean altKey
number charCode
boolean ctrlKey
boolean getModifierState(key)
string key
number keyCode
string locale
number location
boolean metaKey
boolean repeat
boolean shiftKey
number which
```

The `key` property can take any of the values documented in the [DOM Level 3 Events spec](https://www.w3.org/TR/uievents-key/#named-key-attribute-values).

* * *

### Focus Events {#focus-events}

Event names:

```
onFocus onBlur
```

These focus events work on all elements in the React DOM, not just form elements.

Properties:

```javascript
DOMEventTarget relatedTarget
```

* * *

### Form Events {#form-events}

Event names:

```
onChange onInput onInvalid onSubmit
```

For more information about the onChange event, see [Forms](/docs/forms.html).

* * *

### Mouse Events {#mouse-events}

Event names:

```
onClick onContextMenu onDoubleClick onDrag onDragEnd onDragEnter onDragExit
onDragLeave onDragOver onDragStart onDrop onMouseDown onMouseEnter onMouseLeave
onMouseMove onMouseOut onMouseOver onMouseUp
```

The `onMouseEnter` and `onMouseLeave` events propagate from the element being left to the one being entered instead of ordinary bubbling and do not have a capture phase.

Properties:

```javascript
boolean altKey
number button
number buttons
number clientX
number clientY
boolean ctrlKey
boolean getModifierState(key)
boolean metaKey
number pageX
number pageY
DOMEventTarget relatedTarget
number screenX
number screenY
boolean shiftKey
```

* * *

### Pointer Events {#pointer-events}

Event names:

```
onPointerDown onPointerMove onPointerUp onPointerCancel onGotPointerCapture
onLostPointerCapture onPointerEnter onPointerLeave onPointerOver onPointerOut
```

The `onPointerEnter` and `onPointerLeave` events propagate from the element being left to the one being entered instead of ordinary bubbling and do not have a capture phase.

Properties:

As defined in the [W3 spec](https://www.w3.org/TR/pointerevents/), pointer events extend [Mouse Events](#mouse-events) with the following properties:

```javascript
number pointerId
number width
number height
number pressure
number tangentialPressure
number tiltX
number tiltY
number twist
string pointerType
boolean isPrimary
```

A note on cross-browser support:

Pointer events are not yet supported in every browser (at the time of writing this article, supported browsers include: Chrome, Firefox, Edge, and Internet Explorer). React deliberately does not polyfill support for other browsers because a standard-conform polyfill would significantly increase the bundle size of `react-dom`.

If your application requires pointer events, we recommend adding a third party pointer event polyfill.

* * *

### Selection Events {#selection-events}

Event names:

```
onSelect
```

* * *

### Touch Events {#touch-events}

Event names:

```
onTouchCancel onTouchEnd onTouchMove onTouchStart
```

Properties:

```javascript
boolean altKey
DOMTouchList changedTouches
boolean ctrlKey
boolean getModifierState(key)
boolean metaKey
boolean shiftKey
DOMTouchList targetTouches
DOMTouchList touches
```

* * *

### UI Events {#ui-events}

Event names:

```
onScroll
```

Properties:

```javascript
number detail
DOMAbstractView view
```

* * *

### Wheel Events {#wheel-events}

Event names:

```
onWheel
```

Properties:

```javascript
number deltaMode
number deltaX
number deltaY
number deltaZ
```

* * *

### Media Events {#media-events}

Event names:

```
onAbort onCanPlay onCanPlayThrough onDurationChange onEmptied onEncrypted
onEnded onError onLoadedData onLoadedMetadata onLoadStart onPause onPlay
onPlaying onProgress onRateChange onSeeked onSeeking onStalled onSuspend
onTimeUpdate onVolumeChange onWaiting
```

* * *

### Image Events {#image-events}

Event names:

```
onLoad onError
```

* * *

### Animation Events {#animation-events}

Event names:

```
onAnimationStart onAnimationEnd onAnimationIteration
```

Properties:

```javascript
string animationName
string pseudoElement
float elapsedTime
```

* * *

### Transition Events {#transition-events}

Event names:

```
onTransitionEnd
```

Properties:

```javascript
string propertyName
string pseudoElement
float elapsedTime
```

* * *

### Other Events {#other-events}

Event names:

```
onToggle
```
