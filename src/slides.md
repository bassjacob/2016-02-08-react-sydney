class: center, middle, inverse

# Asynchronous Universal Component Rendering

## Beyond Flux/Redux/Relay for component data requirement definitions and fetching

---

class: center

# Why React?

--

## Component driven design

--

## Modular re-usable software - DRY

--

## I don't need to convince you, you're already here.

--

# But something is wrong...

---

class: center, middle, inverse

# Server-side Rendering

---

# Data APIs

---

## Q: How can we do better?

## A: Co-located data requirements / data fetching

---

## async-props

### https://github.com/rackt/async-props

* Static Method on the Component
* Executes method before entering render phase, passes result into Root as props
* Cannot aggregate requests
* Intended for use with a Router root component

---

## The Component

```javascript
import AsyncProps from 'async-props'
import React from 'react'

class App extends React.Component {
  static loadProps(params, cb) {
    cb(null, {
      tacos: [ 'Pollo', 'Carnitas' ]
    })
  }

  render() {
    const tacos = this.props.tacos
    return (
      <div>
        <ul>
          {tacos.map(taco => (
            <li>{taco}</li>
          ))}
        </ul>
      </div>
    )
  }
}
```

---

## react-transmit

### https://github.com/RickWong/react-transmit

* Uses HoC's for declarative data requirement fetching
* Executes inside the render phase
* Monkey-Patches React.createElement to use promises and callbacks to control flow while fetching data

---

## The Component

```javascript
import React    from "react";
import Transmit from "react-transmit";

const Story = React.createClass({
    render () {
        const {story} = this.props;
        return <p>{story.content}</p>;
    }
});

export default Transmit.createContainer(Story, {
    fragments: {
        story: ({storyId}) => {
            return fetch(
              `https://some.api/stories/${storyId}`
            ).then(
              res => res.json()
            );
        }
    }
});
```

---

## React-Resolver

### https://github.com/ericclemmons/react-resolver

---

## Relay

### https://github.com/facebook/relay

* Colocated data requirements
* Aggregated requests
* Occurs in render phase
* Universal rendering is not supported yet. See
  * https://github.com/denvned/isomorphic-relay
  * https://github.com/facebook/relay/issues/36#issuecomment-130402024

---

## Can we still do better?

---

class: center, middle, inverse

# Introducing Proactive*

#### * Yet Another Microframework for React

