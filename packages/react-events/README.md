# `react-events`

*This package is experimental. It is intended for use with the experimental React
events API that is not available in open source builds.*

Event components do not render a host node. They listen to native browser events
dispatched on the host node of their child and transform those events into
high-level events for applications.


## Focus

The `Focus` module responds to focus and blur events on the element it wraps.
Focus events are dispatched for `mouse`, `pen`, `touch`, and `keyboard`
pointer types.

```js
// Example
const TextField = (props) => (
  <Focus
    onBlur={props.onBlur}
    onFocus={props.onFocus}
  >
    <textarea></textarea>
  </Focus>
);
```

```js
// Types
type FocusEvent = {
  target: Element,
  type: 'blur' | 'focus' | 'focuschange'
}
```

### disabled: boolean

Disables all `Focus` events.

### onBlur: (e: FocusEvent) => void

Called when the element loses focus.

### onFocus: (e: FocusEvent) => void

Called when the element gains focus.

### onFocusChange: boolean => void

Called when the element changes hover state (i.e., after `onBlur` and
`onFocus`).


## Hover

The `Hover` module responds to hover events on the element it wraps. Hover
events are only dispatched for `mouse` pointer types. Hover begins when the
pointer enters the element's bounds and ends when the pointer leaves.

```js
// Example
const Link = (props) => (
  const [ hovered, setHovered ] = useState(false);
  return (
    <Hover onHoverChange={setHovered}>
      <a
        {...props}
        href={props.href}
        style={{
          ...props.style,
          textDecoration: hovered ? 'underline': 'none'
        }}
      />
    </Hover>
  );
);
```

```js
// Types
type HoverEvent = {
  pointerType: 'mouse',
  target: Element,
  type: 'hoverstart' | 'hoverend' | 'hovermove' | 'hoverchange'
}
```

### delayHoverEnd: number

The duration of the delay between when hover ends and when `onHoverEnd` is
called.

### delayHoverStart: number

The duration of the delay between when hover starts and when `onHoverStart` is
called.

### disabled: boolean

Disables all `Hover` events.

### onHoverChange: boolean => void

Called when the element changes hover state (i.e., after `onHoverStart` and
`onHoverEnd`).

### onHoverEnd: (e: HoverEvent) => void

Called once the element is no longer hovered. It will be cancelled if the
pointer leaves the element before the `delayHoverStart` threshold is exceeded.

### onHoverMove: (e: HoverEvent) => void

Called when the pointer moves within the hit bounds of the element. `onHoverMove` is
called immediately and doesn't wait for delayed `onHoverStart`.

### onHoverStart: (e: HoverEvent) => void

Called once the element is hovered. It will not be called if the pointer leaves
the element before the `delayHoverStart` threshold is exceeded. And it will not
be called more than once before `onHoverEnd` is called.

### preventDefault: boolean = true

Whether to `preventDefault()` native events.


## Press

The `Press` module responds to press events on the element it wraps. Press
events are dispatched for `mouse`, `pen`, `touch`, and `keyboard` pointer types.
Press events are only dispatched for keyboards when pressing the Enter or
Spacebar keys. If neither `onPress` nor `onLongPress` are called, this signifies
that the press ended outside of the element hit bounds (i.e., the user aborted
the press).

```js
// Example
const Button = (props) => (
  const [ pressed, setPressed ] = useState(false);
  return (
    <Press
      onPress={props.onPress}
      onPressChange={setPressed}
      onLongPress={props.onLongPress}
    >
      <div
        {...props}
        role="button"
        tabIndex={0}
        style={
          ...buttonStyles,
          ...(pressed && pressedStyles)
        }}
      />
    </Press>
  );
);
```

```js
// Types
type PressEvent = {
  pointerType: 'mouse' | 'touch' | 'pen' | 'keyboard',
  target: Element,
  type: 'press' | 'pressstart' | 'pressend' | 'presschange' | 'pressmove' | 'longpress' | 'longpresschange'
}

type PressOffset = {
  top: number,
  right: number,
  bottom: number,
  right: number
};
```

### delayLongPress: number = 500ms

The duration of a press before `onLongPress` and `onLongPressChange` are called.

### delayPressEnd: number

The duration of the delay between when the press ends and when `onPressEnd` is
called.

### delayPressStart: number

The duration of a delay between when the press starts and when `onPressStart` is
called. This delay is cut short (and `onPressStart` is called) if the press is
released before the threshold is exceeded.

### disabled: boolean

Disables all `Press` events.

### onLongPress: (e: PressEvent) => void

Called once the element has been pressed for the length of `delayLongPress`. If
the press point moves more than 10px `onLongPress` is cancelled.

### onLongPressChange: boolean => void

Called when the element changes long-press state.

### onLongPressShouldCancelPress: () => boolean

Determines whether calling `onPress` should be cancelled if `onLongPress` or
`onLongPressChange` have already been called. Default is `false`.

### onPress: (e: PressEvent) => void

Called immediately after a press is released, unless either 1) the press is
released outside the hit bounds of the element (accounting for
`pressRetentionOffset` and `TouchHitTarget`), or 2) the press was a long press,
and `onLongPress` or `onLongPressChange` props are provided, and
`onLongPressCancelsPress()` is `true`.

### onPressChange: boolean => void

Called when the element changes press state (i.e., after `onPressStart` and
`onPressEnd`).

### onPressEnd: (e: PressEvent) => void

Called once the element is no longer pressed (because it was released, or moved
beyond the hit bounds). If the press starts again before the `delayPressEnd`
threshold is exceeded then the delay is reset to prevent `onPressEnd` being
called during a press.

### onPressMove: (e: PressEvent) => void

Called when a press moves within the hit bounds of the element. `onPressMove` is
called immediately and doesn't wait for delayed `onPressStart`. Never called for
keyboard-initiated press events.  

### onPressStart: (e: PressEvent) => void

Called once the element is pressed down. If the press is released before the
`delayPressStart` threshold is exceeded then the delay is cut short and
`onPressStart` is called immediately.

### pressRetentionOffset: PressOffset

Defines how far the pointer (while held down) may move outside the bounds of the
element before it is deactivated. Once deactivated, the pointer (still held
down) can be moved back within the bounds of the element to reactivate it.
Ensure you pass in a constant to reduce memory allocations.

### preventDefault: boolean = true

Whether to `preventDefault()` native events. Native behavior is prevented by
default. If an anchor is the child of `Press`, internal and external navigation
should be performed in `onPress`/`onLongPress`. To rely on native behavior
instead, set `preventDefault` to `false`, but be aware that native behavior will
take place immediately after interaction without respect for delays or long
press.
