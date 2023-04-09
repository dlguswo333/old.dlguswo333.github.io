---
layout: post
toc: true
math: false
title: "What I don't like in React"
categories: ["Programming"]
tags: [web, react, javascript]
author:
  - 이현재
---

We all agree that React is awesome compared to the era
where we all had been suffered from doing Vanilla JavaScript
to render DOM without JSX, reactive states, and much more.
There is no doubt that React has the biggest ecosystem
among SPA tools, that's why more and more people try to learn
React as time goes by.
<!--more-->

It's my pleasure to use React at home, at work.
However, nothing can be perfect or satisfy everyone on the earth.
There are a few things that I really don't like in React.

# States as a Snapshot
In React's functional component, you create a state calling `useState`
function and it returns an array of the state and a setter.
And the state values are from a snapshot right at the moment when a component renders.
It does not gurantee the latest state value will be fetched inside states.

```jsx
import { useState } from 'react';

export default function App() {
  const [number, setNumber] = useState(0);

  return (
    <>
      <h1>{number}</h1>
      <button onClick={() => {
        setNumber(number + 1);
        setNumber(number + 1);
      }}>+2</button>
    </>
  )
}
```

Consider the above example component.
You might expect clicking the button will set the state to `2`,
since you are calling the setter twice incrementing by `1`.
But `number` variable, inside function `App` is from a snapshot.
It represents the same value from the same snapshot
no matter where or how you reference the state.
Basically the click callback is same as:

```jsx
  setNumber(0 + 1);
  setNumber(0 + 1);
```

This is so famous among React developers that even React's brand new
official documents include a section for this: <https://react.dev/learn/state-as-a-snapshot>

Without knowing this property of React, new React developers might get confused
why it does not work the way they intended.
But I think this is not that bad, since it is fair and justice.
Functional component is also a JavaScript function.
A variable does not change as long as you do not directly assign to the variable (`number += 1`).
And we **don't do that** in React.
This might be confusing, but once you understand this, you will get used to it.

If you need the latest state value, pass a callback function inside state setters.
React executes the function with parameter set to the latest state value,
thus letting you get the latest value inside the callback function.
This _updater function_ pattern is useful if you want the latest value.

>https://react.dev/reference/react/useState#updating-state-based-on-the-previous-state

```jsx
  setNumber(num => num + 1);
  setNumber(num => num + 1);
```

# States are Updated Asynchronously
In React, states are not updated immediately, but asynchronously.
This is for performance not to re-render too many times,
but it gives programmers too much frustration.

```jsx
import { useState } from 'react';

export default function App() {
  const [didMouseLeave, setDidMouseLeave] = useState(false);
  const [state, setState] = useState('no');

  const onMouseLeave = () => setDidMouseLeave(true);
  const onMouseEnter = () => didMouseLeave && setState('YES!');

  return <>
    <div onMouseLeave={onMouseLeave} style={{backgroundColor: 'yellow'}}>
      didMouseLeave: {didMouseLeave ? 'true' : 'false'}
    </div>
    <div onMouseEnter={onMouseEnter} style={{backgroundColor: 'orange'}}>
      state: {state}
    </div>
  </>;
}
```

The above is an example of showing states are updated asynchronously.
Place mouse cursor on the first div, and then move it onto the second div.
What will happen? Upon the mouse leaving the first div,
`setDidMouseLeave(true)` will be fired.
Right after that, `didMouseLeave && setState('YES!')` will be fired immediately,
right? Since those divs are adjacent to each other.
As `didMouseLeave` is just updated to `true`, `state` will be updated.

![example-not-work](/img/2023-03-31-what-i-dont-like-in-react/example-not-work.gif)

No, it does not work that way. There is no flaw in our strategy except that
we did not consider asynchronous state updates in React.

In previous section, I explained that states inside components are from a snapshot,
and it also applies here in this example.
React does not update states immediately, thus does not re-render immediately.
We expected things would go like this:
1. `onMouseLeave` fires, and the state got a update.
2. Component re-renders.
3. `onMouseEnter` fires, and since `didMouseLeave` is updated, `setState(true)` is called.

But actually things go like this:
1. `onMouseLeave` fires, and the state got a update.
2. `onMouseEnter` fires, and `didMouseLeave` is updated, but the component did not re-render yet.
  `didMouseLeave` is still `false` in the snapshot, thus `setState(true)` is not called.
3. `App` re-renders, new snapshot has arrived, now `didMouseLeave` is `true`,
  but `mouseenter` event does not fire again.

`didMouseLeave` state is `false` inside `onMouseEnter` callback.
Things will be much more clear if we add bunch of console logs in the component.

```jsx
import { useState } from 'react';

export default function App() {
  console.log('render');
  const [didMouseLeave, setDidMouseLeave] = useState(false);
  const [state, setState] = useState('no');

  const onMouseLeave = () => {
    console.log('onMouseLeave');
    setDidMouseLeave(true);
  }
  const onMouseEnter = () => {
    console.log('didMouseLeave:', didMouseLeave);
    didMouseLeave && setState('YES!');
  }

  return <>
    <div onMouseLeave={onMouseLeave} style={{backgroundColor: 'yellow'}}>
      didMouseLeave: {didMouseLeave ? 'true' : 'false'}
    </div>
    <div onMouseEnter={onMouseEnter} style={{backgroundColor: 'orange'}}>
      state: {state}
    </div>
  </>;
}
```

![react-states-update-async](/img/2023-03-31-what-i-dont-like-in-react/react-states-update-async.gif)

Then how can we solve this? To my best knowledge,
There is no reliable way to solve this problem,
where you have two different callbacks that one sets a state
and the other consumes the lastest state value right away.

This kind of things is pretty much frustrating and not easy to understand
why things work this way.
_"Why has it not been updated? Did I do something wrong?"_
Futhermore, this is also absurd that there is no easy way to solve this problem.
However, I tried to find solutions to this problem.
Some of the following do not work, and can not work in the future.

## updater function
I thought the code would work:

```jsx
  const onMouseEnter = () => {
    let _didMouseLeave;
    setDidMouseLeave(value => {
      _didMouseLeave = value;
      console.log('inside updater function');
      return value;
    })
    _didMouseLeave && setState('YES!');
    console.log('onMouseEnter');
  }
```

The updater function returns the value as it is,
and a local variable `_didMouseLeave` gets the latest value.
And we use that variable for the rest of `onMouseEnter` callback.

But it does not work since the updater function executes
asynchronously after the `onMouseEnter` has finished.

![updater-function-not-work](/img/2023-03-31-what-i-dont-like-in-react/updater-function-not-work.gif)

I assume it might have something to do with performance.
While executing event callbacks, React does not execute updater functions
to focus solely on handling events. After all the handlers finish,
React calls updater functions for next re-render.
I don't know. It's all my guess.

## `useRef`
Use `useRef` to save `didMouseLeave`.
But be aware that `ref` changes do not incur re-render.

>When you change the ref.current property, React does not re-render your component. React is not aware of when you change it because a ref is a plain JavaScript object.<br>
><https://react.dev/reference/react/useRef#caveats>

```jsx
import { useState, useRef } from 'react';

export default function App() {
  const didMouseLeave = useRef(false);
  const [state, setState] = useState('no');

  const onMouseLeave = () => {
    didMouseLeave.current = true;
  }
  const onMouseEnter = () => {
    didMouseLeave.current && setState('YES!');
  }

  return <>
    <div onMouseLeave={onMouseLeave} style={{backgroundColor: 'yellow'}}>
      didMouseLeave: {didMouseLeave.current ? 'true' : 'false'}
    </div>
    <div onMouseEnter={onMouseEnter} style={{backgroundColor: 'orange'}}>
      state: {state}
    </div>
  </>;
}
```

But the above does not 100% resemble the original component.
The original component re-render upon solely on first div `mouseleave` event.
This component, does not.
To replicate the original component, we need one `useState` and one `useRef`
to save **the same state**.

```jsx
import { useState, useRef } from 'react';

export default function App() {
  const didMouseLeaveRef = useRef(false);
  const [didMouseLeave, setDidMouseLeave] = useState(didMouseLeaveRef.current);
  const [state, setState] = useState('no');

  const onMouseLeave = () => {
    didMouseLeave.current = true;
    setDidMouseLeave(didMouseLeave.current);
  }
  const onMouseEnter = () => {
    didMouseLeave.current && setState('YES!');
  }

  return <>
    <div onMouseLeave={onMouseLeave} style={{backgroundColor: 'yellow'}}>
      didMouseLeave: {didMouseLeave.current ? 'true' : 'false'}
    </div>
    <div onMouseEnter={onMouseEnter} style={{backgroundColor: 'orange'}}>
      state: {state}
    </div>
  </>;
}
```

...I really don't like it.<br>
Maybe defining a custom hook that has an `useState` and an `useRef`
could be a good idea to recycle and refactor the code.

<!-- TODO Add flushSync -->

## `recoil`
If you use `recoil`, at least in `^0.7.x` versions,
updater functions execute synchronously unlike React states.
But be aware that I could not find any documentation for this
thus **this behavior may change on future releases**.

```jsx
  const [didMouseLeave, setDidMouseLeave] = useRecoilState(mouseLeaveAtom);

  const onMouseEnter = () => {
    let _didMouseLeave;
    setDidMouseLeave(value => {
      _didMouseLeave = value;
      console.log('inside recoil updater function');
      return value;
    })
    _didMouseLeave && setState('YES!');
    console.log('onMouseEnter');
  }
```

![recoil-updater-function-works](/img/2023-03-31-what-i-dont-like-in-react/recoil-updater-function-works.gif)

## Other SPA Tools
In this section, I compare other SPA frameworks/libraries with the specific example component.

### Svelte
Svelte just works out of the box.

```svelte
<script>
	let didMouseLeave = false;
	let state = 'no';

	const onMouseLeave = () => {didMouseLeave = true};
	const onMouseEnter = () => {didMouseLeave && (state = 'YES!')};
</script>

<div on:mouseleave={onMouseLeave} style:background-color='yellow'>
	didMouseLeave: {didMouseLeave}
</div>
<div on:mouseenter={onMouseEnter} style:background-color='orange'>
	state: {state}
</div>
```

### SolidJS
SolidJS is a SPA framework and it has react-like grammars and syntax but
does not have virtual DOM, or asynchronous updates (though you can if you want).
React developers may easily understand SolidJS code without learning it.
SolidJS works out of the box.

```jsx
function Counter() {
  const [didMouseLeave, setDidMouseLeave] = createSignal(false);
  const [state, setState] = createSignal('no');

  const onMouseLeave = () => {
    setDidMouseLeave(true);
  }
  const onMouseEnter = () => {
    didMouseLeave() && setState('YES!');
  }

  return <>
    <div onMouseLeave={onMouseLeave} style={{'background-color': 'yellow'}}>
      didMouseLeave: {didMouseLeave() ? 'true' : 'false'}
    </div>
    <div onMouseEnter={onMouseEnter} style={{'background-color': 'orange'}}>
      state: {state()}
    </div>
  </>;
}
```

SolidJS gurantees its granular render/update in its own document.
>Solid's reactivity is synchronous which means, by the next line after any change,
>the DOM will have updated. And for the most part this is perfectly fine,
>as Solid's granular rendering is just a propagation of the update in the reactive system.
><https://www.solidjs.com/tutorial/reactivity_batch>
