We mentioned before that in order to have an API that updates a final value when something changes, we’re going to have to set new values when something changes. We do this in the handler, in a function called `track`, where pass in the `target` and `key`.



```js
const dinner = {
  meal: 'tacos'
}

const handler = {
  get(target, prop, receiver) {
    track(target, prop)
    return Reflect.get(...arguments)
  }
}

const proxy = new Proxy(dinner, handler)
```



Vue3 响应式 不再检查是否有更新变化发生，而是利用了proxy的能力去拦截对象。

```js
setup(props, context) {
    
}
```

由于`props`是reactive，所以不能使用`ES6 destructuring`，因为这会使`props`失去reactive。

如果一定需要使用`ES6 destructuring`，可以使用`toRefs()`来包裹`props`







