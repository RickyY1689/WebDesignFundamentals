# Understanding React Functions and Hooks

## The State Hook 

React hooks provides a new way to create and update state variables allowing users to create multiple different state objects rather than the original single state object with multiple variables stored within it. To initialize a variable as a state using hooks we simple write the following:

``` jsx
import React, { useState } from 'react';

const App = () => {
    const [data, setData] = useState(0)
}
```

 When declaring a state object there are always two things to declare. The first is the name of the variable which will store the data. The second is the function which will be called to **overwrite** the data stored in the first variable. **Note** that this will **overwrite** rather than update meaning that we can't directly add new data into our state components like before. However we can still append new data through different ways such as this. The value in the parenthesis of `useState()` is the value the first variable is initialized with. 

```jsx
const newData = 5;
const [data, setData] = useState(0)
setData([...data, newdata]);
```

State hook components can be initialized with anything whether it's a integer value, a character, an empty array, a hashtable, or whatever else. Here's an example of a more complex state component. 

```jsx
   const [interestsChecked, setInterestsChecked] = useState({
        database: false, 
        machineLearning: false,
        computerVision: false,
        augmentedReality: false,
        blockchain: false,
        virtualReality: false, 
        iot: false,
        robotic: false, 
        security: false
      });
```



## The Effect Hook  (Component Lifecycles)

UseEffects are the new react hooks method of implementing component mounting processes. These functions are meant to be used for things on your web page which needs to be constantly updated without the need of having to manually trigger it via a button or some other component. 

There are three types of useEffect functions. Ones that run when the component is first loaded (mounted), ones that run when the component is unloaded (unmounted) and ones that run as long as the component is loaded and being shown to the user. 

To implement a function which runs when a component is initially loaded, we write a function like the following:

```jsx
import React, { useEffect } from 'react';
useEffect(() => {
    ...
}, []);
```

To implement a function which runs as long as the component is loaded and being rendered, we write the following:

``` jsx
import React, { useEffect } from 'react';
useEffect(() => {
    ...
});
```

To implement a function which runs when the component is destroyed (unmounted) we can write the following: 

```jsx
import React, { useEffect } from 'react';
useEffect(() => {
    return () => {
        ...
    }
}, [])
```

**Note:** These functions can be put either within your main function within the component or outside of it. However, if you plan on updating variables within some function then you better place the useEffect functions within it. 

## General Reactjs Things to Know

### Passing Data During Rerouting

When rerouting onto a different react component you can pass information via a state like so:

``` JSX
history.push({
    pathname: '/profile',
    state: {data: res.data}
})
```

In order to store this data once you arrive at the new webpage, simply store it within a variable as follows:

``` JSX
const AppComp = (props) => {
    const {name, password} = props.location.state //This line stores the data into local variables
  return (
    ...
  );
}
```



### Passing Data to Another Component

To pass data to another component we utilize React's prop features. When we call the new component we simply just add in the data we wish to pass thought. For example. say I needed to pass through some variable called `quantity` which held some numerical value which I needed for my `<Table/>` component. I could add it in the following manor: 

```jsx
<TableBody quantity={quantity} />
```

**Note** that the variable in blue doesn't need to match the variable name of the data being thrown it. It's just convenient. Now to retrieve that data within that component we simply add in the `props` argument during the function declaration. 

``` jsx
const TableBody = (props) => {
    console.log(props.characterData)
}
```

