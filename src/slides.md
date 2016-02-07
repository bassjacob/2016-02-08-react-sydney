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

## Jacob Bass

## bassjacob@gmail.com

## github.com/subshad

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

<img style="width:100%" src="public/flux.jpg">

---

class: center, middle

<img style="width:100%" src="public/magic.gif">

---

class: center, middle
background-color: #33114F

# .white-title[Q: Can We Do Better?]

---

class: center, middle
background-color: #11774F

# .white-title[Co-located Data Requirements]

## .white-title[&]

# .white-title[Universal Rendering]

---

class: middle

```javascript
import { Component } from 'react';

@data(({greetingId, targetId}) => {
  //do some magic and return { greeting, target }
});
export default class HelloWorld extends Component {
  render() {
    const { greeting, target } = this.props;

    return (
      <div>{greeting} to {target}</div>
    )
  }
}
```

---

class: center, middle
background-color: #33AA4F

# .white-title[[facebook/relay](https://github.com/facebook/relay)]

---

class: middle

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
```

---

class: middle

```
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
```

---

class: middle

```
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

class: middle

## * Colocated data requirements

## * Aggregated requests

## * Occurs in render phase

## * Universal rendering is not supported

&nbsp;

  https://github.com/denvned/isomorphic-relay

  https://github.com/facebook/relay/issues/36#issuecomment-130402024

---

class: center, middle
background-color: #77BB4F

# .white-title[[rackt/async-props](https://github.com/rackt/async-props)]

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
          {tacos.map(taco => <li>{taco}</li>)}
        </ul>
      </div>
    )
  }
}
```

---

class: middle

## * Static method on a root component

## * Executes pre-render, blocks render

## * Cannot compose specific component requirements

---

class: center, middle
background-color: #22BB4F

# .white-title[[RickWong/react-transmit](https://github.com/RickWong/react-transmit)]

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

class: middle

## * Uses HoC's to compose component data requests

## * Executes during render phase

## * Monkey-Patches React.createElement to block server render while pending

---

class: center, middle
background-color: #224FCC

# .white-title[[ericclemmons/react-resolver](https://github.com/ericclemmons/react-resolver)]

---

class: middle

```javascript
import { resolve } from "react-resolver";

@resolve("user", function(props) {
  return http.get(`/api/users/${props.params.userId}`);
})
class UserProfile extends React.Component {
  render() {
    const { user } = this.props;
    ...
  }
}
```

---

class: middle

## * Decorators!!! ❤❤❤
## * Need one decorator per prop required
## * Must define transport for each resolver
## * Doesn't enforce an ecosystem or standard

---

class: center, middle, inverse

# Introducing Proactive*

&nbsp;

&nbsp;

#### * Yet Another Microframework for React

