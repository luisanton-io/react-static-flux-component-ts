# F L U X - C O M P O N E N T 
###### Created by [Luis Antonio Canettoli Ordoñez](http://luisanton.io)

Nobody likes `React-Redux` boilerplate code (and if you do... chances are [nobody likes *you* as well](https://tenor.com/view/notarapper-rapbattle-gif-4816410)).

While [somebody](https://medium.com/@morgler/dont-use-redux-9e23b5381291), together with [somebody else](http://blog.flaviocaetano.com/post/redux-sucks-with-react-native/) and the [rest of the world](https://www.google.com/search?q=redux+sucks) are complaining about this, [still someone else](https://medium.com/@shanebdavis/how-i-eliminated-redux-boilerplate-with-hooks-for-redux-bd308d5abbdd) is already trying to figure out a way to avoid all of this.

I'm just humbly presenting my solution:

```
npm install react-flux-component
```

This solution is fully working for class-based components in **TypeScript** React applications. 
A version for functional components is coming soon.

## Features

#### Automatic dispatching

You can now define **class properties** assigning them **references** to values in a static "shared store": thanks to their *setters*, assigning values to these references will instead automatically update the store, as well as each component "subscribed" (**binded**) to that same key.
    
Components `hardBind`-ed to a key in the store will automatically re-render **only** when that exact key gets updated. *(How cool is that? A lot indeed.)*

To avoid unnecessary re-renders, you can also `softBind` a value to the component: you may want to do so with a **value shared among other** `hardBind`**-ed components** (where it will actually get rendered), or with a **"static" method** (which never gets rendered).

#### Fully typed

This implementation relies on your **intial shared states** in input. All binded properties will inherit the **correct types** and preserve TS type-checking purpose.

#### Close to 0 boilerplate required!
This implementation happily relies on almost **no boilerplate**: just provide your initial state to the `makeComponent()`, and then always inherit from your `FluxComponent.tsx`

* Create your `FluxComponent.tsx` 
```TSX
import makeComponent from 'react-flux-component'

//  Albeit not mandatory, it's always a good idea to implement a defined interface:
//  interface IShared {
//    answer: number
//  }

const initialSharedState = { // : IShared
    answer: 42
}

export default makeComponent(initialSharedState)
```
* Have components inherit from `Component.tsx` instead of `React.Component`
```TSX
import Component from './FluxComponent'

class MyComponent extends Component {/*...*/}
```
* *Note*: this implementation won't generate a **store history**. Check the [History section](https://github.com/luisanton-io/react-static-flux-component-ts#history-actions) if you need **history actions**.

## Usage
* `hardBind()` or `softBind()` your property (e.g. `answer`) passing its node in the singleton as a parameter (e.g. `Component.shared.answer`)
```TSX
import Component from './FluxComponent'

class MyComponent extends Component {

    answer = this.hardBind(Component.shared.answer) // Type gets inherited from initial state 
    
    /* 
        (property) MyComponent.answer: {
            value: number;
        }
    */

    render() {
        return (
          <div> { this.answer.value } </div>
        )
    }
}
```
* You can freely access and mutate `this.answer.value`:
```TSX
//You can now do this!
this.answer.value++
console.log(this.answer.value)
```
* Updating the binded property will now **automagically** re-render any other component `hardBind`-ed to that same property

## History actions
* You can autogenerate a store history, so to `undo` and `redo` and get to an older or newer point in time.
* However, you might not want every single property to update the history: for example, properties defining part of the current *view*, like themes, filters etc. 
* Therefore you can specify two separate initial states as argument in `makeComponent()`, the first containing *untracked* and the second containing *tracked* shared properties.

```TSX
import makeComponent from 'react-flux-component'

const untrackedShared {
    darkTheme: false
}

const trackedShared {
    answer: 42
}

export default makeComponent(untrackedShared, trackedShared)
```
* You can now trigger `Component.actions.undo()` or `Component.actions.redo()` as you wish.

```TSX
<div>
    <button onClick= { Component.actions.undo }> Undo </button>
    <button onClick= { Component.actions.redo }> Redo </button>
</div>
```
* Should you need to handle **further logic**, note that these methods **return a** `boolean` **value**, reflecting whether the action went through correctly.
    
## Warning
* Components' state `interfaces` **must** extend `{ State }` from `'react-flux-component'` and **preserve** the data injected in FluxComponent constructor; unexpected behaviors will be encountered otherwise. This happens because all the shared data is stored in each component's state in `this.state.shared`:
```TSX
    import { State } from 'react-flux-component'
    
    interface MyComponentState extends State {
        whatever: number
    }
    
    class MyComponent extends Component<{}, MyComponentState> {
        state: MyComponentState = {
            ...this.state, // Don't forget this!
            whatever: 123
        }
        // ...
    }
```
* Mutating a `hardBind`-ed property inside the `render()` method will cause an **infinite render loop**! ;)

## Example Repo
* [Minimal Counter Example](https://codesandbox.io/s/fervent-banzai-168gu)
* [ToDo List With History](https://github.com/luisanton-io/todos-ts-flx) based on [Redux ToDo example](https://redux.js.org/basics/example)

