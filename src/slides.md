class: center, middle
background-color: #0A6C8A

# .white-title[Asynchronous Universal Component Rendering]

&nbsp;

## .white-title[Beyond Flux/Redux/Relay for component data requirement definitions and fetching]

---

class: right

&nbsp;

# $ whoami

&nbsp;

&nbsp;

&nbsp;

&nbsp;

## Jacob Bass

&nbsp;

## bassjacob@gmail.com

&nbsp;

## github.com/subshad

&nbsp;

## http://jacobbass.net

---

class: center, middle, inverse

# Why React?

---

class: center, middle, image-background
background-image: linear-gradient(rgba(0,0,0,0.55),rgba(0,0,0,0.55)), url('public/components.jpg')

# .white-title[Components]

---

class: center, middle, image-background
background-image: linear-gradient(rgba(0,0,0,0.55),rgba(0,0,0,0.55)), url('public/predictability.jpg')

# .white-title[Predictability]

---

class: center, middle, image-background
background-image: linear-gradient(rgba(0,0,0,0.55),rgba(0,0,0,0.55)), url('public/modularity.jpg')

# .white-title[Modularity]
## .white-title[&]
# .white-title[Composability]

---

class: center, middle, image-background
background-image: linear-gradient(rgba(0,0,0,0.55),rgba(0,0,0,0.55)), url('public/community.jpg')

# .white-title[Community]

---

class: center, middle
background-color: #4FAA33

# .white-title[But something is wrong...]

---

class: center, middle
background-color: #22334F

# .white-title[Flux in 5 Minutes]

---

class: center, middle
background-color: #00334F

# .white-title[Server-side Rendering]

---

class: center, middle
background-color: #33114F

# .white-title[Q: Can We Do Better?]

---

class: center, middle
background-color: #11774F

# .white-title[A: Co-located Data Requirements]

---

class: center, middle
background-color: #33AA4F

# .white-title[[facebook/relay](https://github.com/facebook/relay)]

---

class: center, middle

## Colocated data requirements

## Aggregated requests

## Occurs in render phase

## Universal rendering is not supported yet.

  https://github.com/denvned/isomorphic-relay

  https://github.com/facebook/relay/issues/36#issuecomment-130402024

---

```javascript
class Tea extends React.Component {
  render() {
    var {name, steepingTime} = this.props.tea;
    return (
      <li key={name}>
        {name} (<em>{steepingTime} min</em>)
      </li>
    );
  }
}
Tea = Relay.createContainer(Tea, {
  fragments: {
    tea: () => Relay.QL`
      fragment on Tea {
        name,
        steepingTime,
      }
    `,
  },
});

class TeaStore extends React.Component {
  render() {
    return <ul>
      {this.props.store.teas.map(
        tea => <Tea tea={tea} />
      )}
    </ul>;
  }
}
TeaStore = Relay.createContainer(TeaStore, {
  fragments: {
    store: () => Relay.QL`
      fragment on Store {
        teas { ${Tea.getFragment('tea')} },
      }
    `,
  },
});

class TeaHomeRoute extends Relay.Route {
  static routeName = 'Home';
  static queries = {
    store: (Component) => Relay.QL`
      query TeaStoreQuery {
        store { ${Component.getFragment('store')} },
      }
    `,
  };
}

ReactDOM.render(
  <Relay.RootContainer
    Component={TeaStore}
    route={new TeaHomeRoute()}
  />,
  mountNode
);
```

---

class: center, middle
background-color: #77BB4F

# .white-title[[rackt/async-props](https://github.com/rackt/async-props)]

---

## Static Method on the Component

## Executes method before entering render phase, passes result into Root as props

## Cannot aggregate requests

## Intended for use with a Router root component

---

class: middle

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

class: center, middle
background-color: #22BB4F

# .white-title[[RickWong/react-transmit](https://github.com/RickWong/react-transmit)]

---

## Uses HoC's for declarative data requirement fetching

## Executes inside the render phase

## Monkey-Patches React.createElement to use promises and callbacks to control flow while fetching data

---

class: middle

```javascript
import React  from "react";
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

class: center, middle
background-color: #224FCC

# .white-title[[ericclemmons/react-resolver](https://github.com/ericclemmons/react-resolver)]

---

class: center, middle, inverse

# Introducing Proactive*

&nbsp;

&nbsp;

#### * Yet Another Microframework for React

