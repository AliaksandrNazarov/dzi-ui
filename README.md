# Dzi-UI

Dzi-UI is a minimalistic UI framework
that powers the front-end with the true dynamic components.

## Application

An Application is top-level component which may provide app-scope features such as side-effects, resources and pipes.

```js
export class SampleApplication {
  // handles `-> ...` side-effect
  dispatch (key, payload) {
    this.store.dispatch(key, payload, (error, data) => this.assign({error}))
  }
  // handles `<- ...` side-effect
  subscribe (key, subscriberId, cb) {
    this.store.subscribe(key, subscriberId, cb)
  }
  // invoked from component `done()`
  unsubscribe (subscriberId) {
    this.store.unsubscribe(subscriberId)
  }
  // resolves static resources from `:key` pattern.
  res (key) {
    return RES[key] || key
  }
  // pipes used to adjust component properties values, `{{prop|pipe}}`
  get pipes() {
    return PIPES
  }
}
```

## Component

```js
class MyComponent {
    // returns a component template XML
    TEMPLATE(){
        return `<img src="{{src}}" data-src="{{other}}" click="{{assign}}"/>`
    }
    // this hook called once on component init
    init(ctx){
        return () => deferedCode
    }
    // this hook called once on component done
    done(){
    }
    // optional getter used to resolve specific property value.
    // (If no getter specified, then just `this.src` used)
    getSrc(){
        return this.url.toString()
    }
    // optional setter used from `assign()`
    setSrc(value){
        this.url = URL.parse(value)
    }
    // will be patched by framework. do not override.
    // assign(delta) { <library code> }
    // optional interceptor used to resolve expression placeholders (like `Proxy`).
    // get(key) { return this.state[key] }
}
```

... or `bare-template` definition

```html
<script type="text/x-template" id="MyHeader">
  <header class="header">
    <h1>{{title}}</h1>
    <input type="text" class="new-todo" placeholder=":new_todo_hint" autofocus="true" enter="-> add"/>
  </header>
</script>
```

## Template Cheatsheet

### tags

- `<ui:fragment>` - a transparent container. Useful to wrap multiple tags, apply `ui:if`, `ui:each`
- `<ui:then>` - a positive conditional container. Used with `ui:if` parent.
- `<ui:else>` - a negative conditional container. Used with `ui:if` parent.
- `<ui:transclude [key="key"]>` - a placeholder for a component content to be inserted instead of.
- `<ui:Some>` - a true dynamic tag/component based on a value of a `Some` property

### attributes

- `ui:if="prop"` conditional inclusion based on a value of a `prop` property
- `ui:each="item of prop"` - iteration over items of list from a `prop` property placing current list item in `item`.(`item.id` is used here to match and re-use item components)
- `ui:props="expr"` - spreads values of object from `expr` expression into properties of a target component.
- `ui:key="some"` - to mark a inner component to be transcluded in place of `<ui:transclude key="some"/>`.

### attribute expressions (also used for inner text)

- `"value"` - any primitive string. 'true', 'false' are narrowed to boolean.
- `":resId"` - invokes `app.res(resId)`
- `"{{prop[|pipe]*}}"` - value of `prop` property. Optional left-to-right chain pipes defined in `app.pipes` object.
- `"prefix{{prop}}suffix"` - interpolate string with value of `prop` property

### attribute side-effects

- `"<- expr"` - produces and register a callback to subscribe to `app.subscribe(key, target, cb)`.
- `"-> expr"` - produces an event handler which to send a data payload to an `app.dispatcher(key, payload)`.

### syntactic sugar (often allows `bare-template` component definition)

- use flags for conditional classes like `class="active:{{flag}}"`
- use equals operation for conditional classes like `class="active:{{v1}}={{v2}}"`
- `enter` event handler for `<input type="input">`
- `toggle` event handler for `<input type="checkbox">`
- use colon to iterate over resource `ui:each="item of :resId"`
- use excl to invert conditional `ui:if="!prop"`

## Run

```js
Dzi.launch(AppComponent, ...otherComponents)
```

## Style

[![JavaScript Style Guide](https://img.shields.io/badge/code_style-standard-brightgreen.svg)](https://standardjs.com)
