---
    author: 'Rishav Ghosh'
    title:
      "Amazing New Features In React & React Router v6"
    hashTag: ['react router 6', 'react js']
    createdAt: 'Nov, 7, 2021'
---

I love React!! I have been using the latest stuff from React in my latest project and found a lot of important and cool differences from the previous versions.

The following APIs will not only help you increase your productivity but also improve your code structure.

- Outlet
- forwardRef
- useRoutes

Any developer who is looking forward to learn React or even the ones who have been using React for a while should be able to use and enjoy the stuff in this article!! 😄

# Outlet

```js
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom'

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path='/' element={<Home />} />
        <Route path='users/*' element={<Users />} />
      </Routes>
    </BrowserRouter>
  )
}

function Users() {
  /* All <Route path> and <Link to> values in this
     component will automatically be "mounted" at the
     /users URL prefix since the <Users> element is only
     ever rendered when the URL matches /users/*
  */
  return (
    <div>
      <nav>
        <Link to='me'>My Profile</Link>
      </nav>

      <Routes>
        <Route path='/' element={<UsersIndex />} />
        <Route path=':id' element={<UserProfile />} />
        <Route path='me' element={<OwnUserProfile />} />
      </Routes>
    </div>
  )
}
```

The above is standard code that is used to created routes for navigation in React projects.

Now let's check React router v6 code:

```js
import { BrowserRouter, Routes, Route, Link, Outlet } from 'react-router-dom'

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path='/' element={<Home />} />
        <Route path='users' element={<Users />}>
          <Route path='/' element={<UsersIndex />} />
          <Route path=':id' element={<UserProfile />} />
          <Route path='me' element={<OwnUserProfile />} />
        </Route>
      </Routes>
    </BrowserRouter>
  )
}

function Users() {
  return (
    <div>
      <nav>
        <Link to='me'>My Profile</Link>
      </nav>

      <Outlet />
    </div>
  )
}
```

The `<Outlet>` element is used as a placeholder. In this case an `<Outlet>`enables the `Users` component to render its child routes. Thus the`<Outlet>` element will render either a `<UserProfile>` or `<OwnUserProfile>` element depending on the current location.

Thus the best way this element can be used is in layouts. You can simply create multiple layouts, for e.g.- the Dashboard Layout(for the profile and members page) or the Main Layout(for login and logout, basically when the user is not signed in). Finally, you can simply inject whichever component one needs depending on its route rather than wrapping each route component in a parent layout component like:

```js
<Route path='/' element={<DashboardLayout />}>
  <Route path='/' element={<HomePage />} />
  <Route path=':id' element={<UserProfile />} />
  <Route path='me' element={<OwnUserProfile />} />
</Route>
```

# useRoutes

```js
import { BrowserRouter, Link, Outlet, useRoutes } from 'react-router-dom'

function App() {
  // The <BrowserRouter> element is removed from App because the
  // useRoutes hook needs to be in the context of a <BrowserRouter>
  // element. This is a common pattern with React Router apps that
  // are rendered in different environments. To render an <App>,
  // you'll need to wrap it in your own <BrowserRouter> element.
  let element = useRoutes([
    // A route object has the same properties as a <Route>
    // element. The `children` is just an array of child routes.
    { path: '/', element: <Home /> },
    {
      path: 'users',
      element: <Users />,
      children: [
        { path: '/', element: <UsersIndex /> },
        { path: ':id', element: <UserProfile /> },
        { path: 'me', element: <OwnUserProfile /> },
      ],
    },
  ])

  return element
}

function Users() {
  return (
    <div>
      <nav>
        <Link to='me'>My Profile</Link>
      </nav>

      <Outlet />
    </div>
  )
}
```

React Router v6 ships with an awesome API for routing that uses plain JavaScript objects to declare your routes. In fact, if you look at [the source of ](https://github.com/ReactTraining/react-router/blob/f59ee5488bc343cf3c957b7e0cc395ef5eb572d2/packages/react-router/index.js#L234-L237)`[<Routes>](https://github.com/ReactTraining/react-router/blob/f59ee5488bc343cf3c957b7e0cc395ef5eb572d2/packages/react-router/index.js#L234-L237)`, you'll see that it's really just a tiny wrapper around a hook that is at the heart of the router's matching algorithm: `useRoutes`.

The `useRoutes` hook is a first-class API for routing that lets you declare and compose your routes using [_JavaScript objects _](https://www.w3schools.com/js/js_object_definition.asp)instead of React elements. Continuing with the example above, let's see what it looks like with `useRoutes`.

The `useRoutes` hook accepts a (possibly nested) array of JavaScript objects that represent the available routes in your app. Each route has a `path`, `element`, and (optionally) `children`, which is just another array of routes.

This nested array of Javascript objects keeps the code DRY and improves the readability of the code.

The object-based route configuration may look familiar if you were using the `react-router-config` package in v5. In v6, this configuration format has been promoted to a first-class API in core and the `react-router-config` package will be deprecated.

Moreover, the layout example in the Outlet section can be simplified as follows:

```js
let element = useRoutes([
  {
    path: '/',
    element: <DashboardLayout />,
    children: [
      { path: '/', element: <HomePage /> },
      { path: ':id', element: <UserProfile /> },
      { path: 'me', element: <OwnUserProfile /> },
    ],
  },
])
```

# forwardRef

Ref forwarding is a cool technique for automatically passing a [ref](https://reactjs.org/docs/refs-and-the-dom.html) through a component to one of its children. It gives the child component a reference to a DOM element created by its parent component. This then allows the child to read and modify that element anywhere it is being used.

Before I show you how to 'forward' refs lets first learn what they are and how to create them.

## Creating refs

To create a ref, use the React function called `React.createRef()`. These refs can then be attached to React elements via the ref attribute. Refs are somewhat similar to state. On assigning refs to instance properties of that component we can ensure that they can be referenced anywhere in the component. Check the example below:

```js
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.newRef = React.createRef(); //newRef is now available for use throughout our component
  }
 ...
}
class MyComponent extends React.Component {
 ...
  render() {
    return <div ref={this.myRef} />;
  }
}
```

Here I created a ref called `newRef and` and attached it to the div element in the `MyComponent` component. As a result, I now have the ability to update the div without changing state.

This is the significance of using refs as you can update and modify elements without using state variables that result in the re-rendering of components.

A few points of significance directly from the [documentation](https://reactjs.org/docs/refs-and-the-dom.html):

- When the ref attribute is used on an HTML element, the ref created in the constructor with `React.createRef()` receives the underlying DOM element as its `current` property
- When the ref attribute is used on a custom class component, the ref object receives the mounted instance of the component as its `current` i.e the components props, state, and methods

As seen in the official [React documentation](https://reactjs.org/docs/refs-and-the-dom.html), there are a few good use cases for refs:

## Managing focus, text selection, or media playback

Let's imagine you have an input component. In some parts of your application, you may want the cursor focused on it when a user clicks a button. It makes more sense to modify only that particular instance of the input component without changing the state (via refs), rather than changing the state (via *props*) which will cause the component to re-render every-time. Similarly, you can use refs to control the state of music or video players (pause, play, stop) without them re-rendering anytime you click a button (change the state).

## Incrementing values

Think about a Medium clap button. A quick way to implement a similar feature would be to increment the count value stored in the state every time a user clicks a clap. However, this may not be very efficient. Every time a user clicks the clap button it will re-render, and if you are sending a network request to store the value in a server it will get sent as many times as the button is clicked. With refs, you can target that particular node and increment it every time a user clicks the button without causing a re-render and finally, you can send one request to our server with the final value.

## Triggering imperative animations

You can use refs to trigger animation between elements that rely on themselves for their next state but exist in different components (this concept is called ref forwarding). Refs can also be used to simplify integration with third-party DOM libraries and managing multistep form value states etc.

Now let's move to the technique of ref forwarding:

[Ref forwarding](https://reactjs.org/docs/forwarding-refs.html) is a technique that automatically passes a ref through a component to one of its children. Ref forwarding is very useful when building reusable component libraries. `forwardRef` is the function used to pass the ref to a child component. Let's check out an example below:

```js
function SampleButton(props) {
  return <button className='button'>{props.children}</button>
}
```

The `SampleButton()` component is a modified button that will be used throughout the application in a similar manner as a regular DOM button, therefore accessing its DOM node may be unavoidable for managing focus, selection, or animations related to it.

In the example below, `SampleComponent()` uses `React.forwardRef` to obtain the ref passed to it, and then forward it to the DOM button that it renders:

```js
const SampleButton = React.forwardRef((props, ref) => (
  <button ref={ref} className='button'>
    {props.children}
  </button>
))
const ref = React.createRef()
;<SampleButton ref={ref}>Click me!</SampleButton>
```

Now that I've wrapped the `SampleButton` component with the `forwardRef` method, components using it can get a ref to the underlying button DOM node and access it if necessary ---just like if they used a DOM button directly.

Here's a clarification for the code above:

- A ref is defined in the component that needs the ref and pass it to the button component
- React will pass the ref through and forward it down to `<button ref={ref}>` by specifying it as a JSX attribute
- When the ref is attached, `ref.current` will point to the `<button>` DOM node.

I have just covered the basics *of these very extensive topics. Hope you try them out in your coming React projects. 🔥*

_References:_

1.  [_https://blog.logrocket.com/cleaning-up-the-dom-with-forwardref-in-react/_](https://blog.logrocket.com/cleaning-up-the-dom-with-forwardref-in-react/)
2.  [_https://blog.bitsrc.io/understanding-ref-forwarding-in-react-80accd93ed74_](https://blog.bitsrc.io/understanding-ref-forwarding-in-react-80accd93ed74)

[Article reference](https://frontend-digest.com/whats-new-in-react-router-6-732b06cc83e4#:~:text=Routes%20Replaces%20Switch,Routes%20inside%20of%20a%20Switch%20.)