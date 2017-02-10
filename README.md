# AsyncProps for React Router

This is a shameless ripoff of [Async Props](https://github.com/ryanflorence/async-props/)
This library is promise based which allows for async/await goodness. 
View transitions happen immediately, async props are then populated when the promise resolves.

## Installation

Using [npm](https://www.npmjs.com/):

    $ npm install async-props-promise

Then with a module bundler like [webpack](https://webpack.github.io/), use as you would anything else:

```js
// using an ES6 transpiler, like babel
import AsyncProps from 'async-props-promise'
```

The UMD build is also available on [npmcdn](https://npmcdn.com):

```html
<script src="https://npmcdn.com/async-props-promise/umd/AsyncProps.min.js"></script>
```

You can find the library on `window.AsyncProps`.

## Notes

This is pre-release, it's pretty close though. If you are using it then you are
a contributor. Please add tests with all pull requests.

## Usage

```js
import { Router, Route } from 'react-router'
import AsyncProps from 'async-props-promise'
import React from 'react'
import { render } from 'react-dom'
import tacoFetcher from './taco-fetcher';
class App extends React.Component {

  // 1. define a `loadProps` static method
  static async loadProps(params, cb) {
      const tacos=tacoFetcher.getTacos();    
      return {tacos};
  }

  render() {
      if(!this.props.tacos){
          return <div>Loading...</div>
      }
    // 2. access data as props :D
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

// 3. Render `Router` with AsyncProps middleware
render((
  <Router render={(props) => <AsyncProps {...props}/>}>
    <Route path="/" component={App}/>
  </Router>
), el)
```

### Server

```js
import { renderToString } from 'react-dom/server'
import { match, RoutingContext } from 'react-router'
import AsyncProps, { loadPropsOnServer } from 'async-props-promise'

app.get('*', (req, res) => {
  match({ routes, location: req.url }, (err, redirect, renderProps) => {

    // 1. load the props
    loadPropsOnServer(renderProps, (err, asyncProps, scriptTag) => {

      // 2. use `AsyncProps` instead of `RoutingContext` and pass it
      //    `renderProps` and `asyncProps`
      const appHTML = renderToString(
        <AsyncProps {...renderProps} {...asyncProps} />
      )

      // 3. render the script tag into the server markup
      const html = createPage(appHTML, scriptTag)
      res.send(html)
    })
  })
})

function createPage(html, scriptTag) {
  return `
    <!doctype html>
    <html>
      <!-- etc. --->
      <body>
        <div id="app">${html}</div>

        <!-- its a string -->
        ${scriptTag}
      </body>
    </html>
  `
}
```

## API

Please refer to the example, as it exercises the entire API. Docs will
come eventually :)

