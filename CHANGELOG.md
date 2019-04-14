# Changelog

## 5.0.0 Release

**Summary:** major release introducing additional gestures on top of **drag**: **pinch**, **scroll**, **wheel**, **hover** and **move** are now supported. This release is dropping support for high-order and render-props component and can only be used through React hooks 🎣. Therefore `react-with-gesture` is now `react-use-gesture`. 

### Added

1. Support for pinch, scroll, wheel, move and hover events. Each gesture has now an `active` boolean prop that lets you know whether it’s currently active. State also include `dragging`, `moving`, `scrolling`, `wheeling`, `pinching`, and `hovering` booleans so that you can know within a gesture handler the running state of all others.

2. Support for dynamically enabling or disabling gestures in the `config` object: `useGesture(actions, { enabled: false })` or `useGesture(actions, { drag: true, move: false })` if you want to disable or enable gestures individually.

3. You can now add a gesture to a dom node or the `window` (useful for scroll) with the `domTarget` config prop. In that case you shouldn’t spread `bind` in a component but use the `useEffect` hook that will take care of adding and removing listeners: 

```jsx
const bind = useGesture({ onScroll: () => {...} }, { domTarget: window })
React.useEffect(bind, [bind])
```

4. Individual per-axis velocity has been added in gesture state props as `vxvy: vector2`.

5. There's a new `transform` config prop that can be passed to change the way `x` and `y` are calculated (useful for canvas which inverts axis compared to the dom).

6. Experimental support for pointer events through config: `{ event : { pointerEvents: true } }`. 

### Improved

1. `useGesture` returned value is now cached which should produce better performance in case of frequent renders produced by external factors (i.e. prop change).

2. State and props are no longer frozen, meaning you can now use state or props values from your component _inside_ your handler and they will be up to date.

```jsx
const [dragCount, setDragCount] = useState(0)
const bind = useGesture({
  onDrag: ({ first }) => {
    if (first) setDragCount(dragCount + 1)
    console.log(count) //<-- count will be up to date
  }
})
```

3. Readme should be clearer (hopefully)!

### Breaking

1. HOC and render-props component support has been dropped. Hooks usage is enforced, therefore this package requires React 16.8+.

2. Default syntax `[bind, props] = useGesture()` has been dropped in favor of `bind = useGesture({ onDrag: () => {...} })` which is more performance-effective since it doesn’t render on each frame.

3. `onAction` prop is now an alias of `onDrag` but should be avoided as its support could be dropped at any time. Subsequently, `onUp` and `onDown` have been dropped, and there's now `on[Gesture]Start` and `on[Gesture]End` handlers. 

4. `config` object should now be passed as a second argument.

```jsx
// from this:
useGesture({ onAction: () => {}, config })
// to this:
useGesture({ onDrag: () => {} }, {...config})
```

5. `touch` and `mouse`  config props are dropped.