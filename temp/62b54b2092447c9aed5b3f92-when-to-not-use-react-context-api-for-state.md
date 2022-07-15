## When to (Not) Use React Context API for State? 

 [React's Context API](https://reactjs.org/docs/context.html) is a popular choice for global state *(my definition: state that is shared amongst components)*. It is easy to use and we are used to it because a lot of libraries leverage them. There are characteristics of React Context that you should be aware of. They make context not always the best choice for global state.

## Why Does React Context Exist?
Technically we could just place our whole state at our top-level component and pass it down the React element tree to the components that need access to the state. But in any application but a very simple one that would require us to pass down the state several levels down the tree and through components that are not using the state themselves at all. It would pollute the code and ruin the Developer Experience (DX). That problem is known as **prop-drilling**. React's Context API was created to mitigate this issue. This is an excerpt from the [React Context API docs](https://reactjs.org/docs/context.html):

> Context provides a way to pass data through the component tree without having to pass props down manually at every level.

By combining React's state-related hooks (`useState` and `useReducer`) with React context you can provide a shared state to all components nested within the contexts `Provider`. Problem solved, right? Well, no. The context API has a major issue:

## The Issue With Using React Context API for State
**Consumers of a context always re-render if the state provided by the context changes. It does not matter if a component actually uses the piece of the state that has changed.** Example: `ContextA` provides the state `{ a: 1, b: 1 }` and `ComponentA` reads only `a`. Even if only `b` changes `ComponentA` will re-render – for no reason, it will render the same content. This is called an extra or unnecessary re-render.

For that reason, it is bad practice to have a single, huge state provided by a context. Instead, you should split the state up and create separate contexts like `AuthContext`, `ThemeContext`etc. Ask yourself if consumers mostly consume the majority of the state. Only in that case, you don't end up with a lot of extra re-renders. *(A few extra renders are not an issues at all but it can get out of control if a lot of components and their children re-render.)*

*Note: There are ways to combine context with other techniques like subscriptions to mitigate this issue. But here I am referring to using plain context + ùseState` / ùseReducer`.*

Besides the extra re-renders it can become hard to keep track of the data flow in your application. You won't be able to easily see which data is being used where, as it's the case with props. The React docs include a section [Before You Use Context](https://reactjs.org/docs/context.html#before-you-use-context) for a reason. One highlighted excerpt:

> If you only want to avoid passing some props through many levels, component composition is often a simpler solution than context.

Don't get me wrong, the React Context API is a great tool. But don't see everything as <s>a nail</s> global state just because you have <s>a hammer</s> React's Context API.

## When to Use React's Context API?
Now you may ask yourself when it is a good idea to use context for global state? I am glad you asked, this chart is my answer:

![react-context-api-state.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1656014273759/RdKu5o_hW.png align="center")

As you can see, there are a lot of scenarios where other tools are preferable. I will explore a few of the alternatives in future posts.