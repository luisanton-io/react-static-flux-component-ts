# F L U X - C O M P O N E N T 
###### Created by [Luis Antonio Canettoli Ordoñez](http://luisanton.io)

## Features


You can now define **class properties** assigning them **references** to values in a static "shared store": thanks to their *setters*, mutating these references will instead automatically update the store, as well as each component "subscribed" (**binded**) to that same key.
    
Components `hardBind`-ed to a key in the store will automatically re-render **only** when that exact key gets updated. *(How cool is that? A lot indeed.)*

To avoid unnecessary re-renders, you can also `softBind` a value to the component: you may want to do so with a **value shared among other `hardBind`-ed components** (where it will actually get rendered), or with a **"static" function** (which never gets rendered).

#### Fully typed

This implementation relies on your **custom interface** in input. All binded properties will inherit the **correct types** and preserve TS type-checking purpose.

## Boilerplate

#### Close to 0 boilerplate required!
This implementation happily relies on almost **no boilerplate**: just provide your interface to the `makeComponent()`, and then always inherit from your `FluxComponent.tsx`

* Create your `FluxComponent.tsx` 
```typescript
import makeComponent from flux-component

interface ISharedState {
    whatever: number
}

const initialSharedState: ISharedState {
    whatever: 42
}

export default makeComponent(initialSharedState)
```
* Have components inherit from `Component.tsx` instead of `React.Component`
```typescript
import Component from './FluxComponent'

class MyComponent extends Component {/*...*/}
```
## Usage
* `hardBind()` or `softBind()` your `whatever` property passing `Component.shared.whatever` as a parameter
```typescript
import Component from './FluxComponent'

class MyComponent extends Component {

    whatever = this.hardBind(Component.shared.whatever)

    render() {
        return (
          <div> { this.whatever.value } </div>
        )
    }
}
```
* You can freely access and mutate `this.whatever.value`:
```typescript
//You can now do this!
this.whatever.value++
console.log(this.whatever.value)
```
* Updating the binded property will now *automagically* re-render any other component `hardBind`-ed to that same property
    
## Warning
* Components' states interfaces **must** extend `{ State }` from `'flux-component'`
* Mutating a hard-binded property inside the `render()` method will cause an **infinite render loop**! ;)
