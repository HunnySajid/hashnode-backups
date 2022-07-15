## You can log function names this way 

 Recently while debugging the codebase at my job, I had a use-case where I needed to know which method call happened where each such method call results in some SQL statement. The main focus was to know the order in which these functions were called so that I can map them to sql statements being logged in another node_module.

The number of unique methods that existed within the Class responsible for this were 30. Now for urgency sake, I went berserk and added 30 **console.log** inside each such method to log the method names. 

But obviously, I didn't like it much. There has to be some way to achieve the same with some extra fancy code. 

![coz i m fancy](https://media0.giphy.com/media/db30h5xnsXGqVNF3zY/200.gif)

That's where I begin my intense google search to end up with 2 implementations. 

But before that, let's consider a simplified version of my problem :-


```js
Class OperationManager {

operation1(){}

operation2(){}

operation3(){}

operation4(){}

operation5(){}

operation6(){}

operation7(){}

operation8(){}

operation9(){}

operation10(){}

}

function managerFactory() {
  return new OperationManager();
}

//Assume usage inside another function like so :-
(function(manager) {
  manager.operation8();

  manager.operation1();

  manager.operation4();

})(managerFactory());
```

Now I want to know the name of each **operationX method** that is called without adding a single line of `console.log('operationX called')` line inside the function bodies. 

Time to check those 2 implementations :-

![its time](https://c.tenor.com/3cUNs401gkUAAAAC/hurry-up-time-is-ticking.gif)

### 1. Injecting logging behaviour

```
function logFnCall(func) { 
  const p = document.createElement('p');
  p.textContent = `${func} called`
  document.body.append(p);
}

function inject(obj, func) {
  for (let key in obj) {
    let value = obj[key];
    if (typeof value === 'function') {
      obj[key] = function() {
        func(key, value);
        return value.apply(this, arguments);
      }
    }
  }
}

// Modify the managerFactory() implementation like so :-
function managerFactory() {
  const manager = new OperationManager();
  inject(manager, logFnCall);
  return manager;
}
```

#### Explanation :-
The `inject` function is trying to loop over all the keys that are available inside the `obj` and then if the `value` obtained using that `key` is a **function**, we modify the `obj[key]` to accommodate our custom `func` implementation and then call the old implementation using appropriate **context** and **arguments**. 
So inside the `managerFactory` function, instead of returning new instance as it is, we create it and save it in `manager` and before returning the same, we  **inject** our custom `logFnCall` implementation. 

Here is the codepen for the same :-

%[https://codepen.io/lapstjup/pen/rNwKaxp?editors=0011]

Wow so nothing came up. It's totally blank !!!!!

![blank](https://media0.giphy.com/media/ZaKcIYMjNYNf4lEuC7/200.gif)

And there is a good reason for that:-

In the `inject` implementation, we have this statement - `let key in obj` which assumes that each `key` in `obj` is **enumerable**. But `obj` was created using a **class** and class methods are **non-enumerable**. So we just need to modify this line to help us fetch those method names. We can do it successfully by replacing the existing statement with `let key of Object.getOwnPropertyNames(obj.constructor.prototype)`. 

Here is a working codepen :-

%[https://codepen.io/lapstjup/pen/KKqewZv?editors=0010]


### 2. Proxifying logging behaviour (I personally liked this)

```js
function logFnCall(key,value) { 
  if(typeof value==='function' ){
  const p = document.createElement('p');
  p.textContent = `${key} called`
  document.body.append(p);
  }
}

function proxify(obj, func) {
  return new Proxy(obj, {
    get: function(target, key) {
      let value = target[key];
      func(key, value);
      return value;
      // or return Reflect.get(...arguments) 
    }
  })
}

// Modify the managerFactory() implementation like so :-
function managerFactory() {
  const manager = new OperationManager();
  const proxyManager = proxify(manager, logFnCall);
  return proxyManager;
}

```
#### Explanation :-
The `proxify` function returns a new `Proxy` object which has a `get` trap setup. The `get` trap has `target` which is `obj` itself and `key` which can be any `key` in the `obj` itself or it's prototype and so forth. Whenever we do something like `manager.operation1`, the internal **[[Get]]** implementation in the JS spec gets called to return us `operation1` property on `manager`. Proxy helps us intercept that call to **[[Get]]** to perform any operation that we want to. And we have a good use-case here of logging the function name that's called. To do that, we pass a custom `func` which takes the `key` and `value` that's being intercepted and according to inputs performs the relevant operation. We return the `value` because we want the underlying **[[Get]]** behavior to remain same. We can also return `Reflect.get(...)` to get the existing behavior but not using it doesn't pose a problem here.
So inside the `managerFactory` function, instead of returning new instance as it is, we create it and save it in `manager` and instead of returning the same, we  **proxify** the `manager` with our custom `logFnCall` implementation and return the `proxyManager`. 

Here is a working codepen :-

%[https://codepen.io/lapstjup/pen/eYRKmjK?editors=0010]



I like the second implementation because of following reasons :-

* No direct mutation of method implementation like in `inject`. 
* The conditional behavior of checking if the `value` is a function is implemented inside the `logFnCall` in second approach. Gives more power to the developer to compose custom behaviors.
* Not caring about the **enumerable** and **non-enumerable** stuff.

A good resource to know more about [Proxy](https://javascript.info/proxy)

Have encountered similar situation and have an alternate proposal ? 
Go smash the comment section then 💻

## Thank you for your time :D 
