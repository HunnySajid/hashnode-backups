## Creating Reusable React Components with TypeScript 

 Often we write React components that get bigger and bigger and at some point, we extract parts of it into separate components. Either because the component is getting too big or because we need parts of it somewhere else.

This is generally a good approach but after a while, we can up with several components that are similar (e.g. some kind of list, card, or whatever). Often they have some similarities. Wouldn't it be nice to have some basic building blocks that can be reused in order to encapsulate such similarities?

In this post, I'll show you how to use the React techniques "render props" and "as-prop" and how to use them with TypeScript.

You can find the [finished code on GitHub](https://github.com/JannikWempe/react-reusable-components).

## Starting Point: Non-Generic JS Component

This is the component we are starting with:

```
import React from "react";
import { Pokemon } from "../api/pokemon";

type Props = {
  pokemons: Pokemon[]; // Pokemon is { name: string; url: string; }
};

export function PokemonList({ pokemons }: Props) {
  return (
    <ul>
      {pokemons.map((pokemon) => (
        <li key={pokemon.name}>
          <a href={pokemon.url} target="_blank" rel="noreferrer">
            {pokemon.name}
          </a>
        </li>
      ))}
    </ul>
  );
};
```

*Note: I know that "Pokemons" isn't the plural, but I use the "s" to distinguish it from the singular.*

It is just a list of Pokemon which is rendering some links – nothing special here. But imagine at another place we are either creating a similar list with trainers or a list which is containing more information about the Pokemon.

We could come with a `<LinkList />` which is also usable for trainers or add an optional prop to this component indicating that it should also render more details. But these solutions aren't really reusable either. 

*💡 Boolean flags like `showDetails` are often a code smell. It indicates that the component is doing more than one thing – it is violating the [Single Responsibility Principle (SRP)](https://en.wikipedia.org/wiki/Single-responsibility_principle). That is not only true for React components, but for functions in general.*

Okay, let us create a truly reusable `<List />` component.

## React Render Props

First of all, what is the React Render Props technique? It is even mentioned in the official React docs:

> The term “render prop” refers to a technique for sharing code between React components using a prop whose value is a function.
>
>A component with a render prop takes a function that returns a React element and calls it instead of implementing its own render logic.

Render props are a way to achieve [Inversion of Control (IaC)](https://en.wikipedia.org/wiki/Inversion_of_control). Instead of having the child component control the rendering of the list items, we reverse control and have the parent component control the rendering of the items.

This is what a generic `<List />` component could look like:

```
import React from "react";

type Props<Item> = {
  items: Item[];
  renderItem: (item: Item) => React.ReactNode;
}

export function List<Item>({ items, renderItem }: Props<Item>) {
  return <ul>{items.map(renderItem)}</ul>;
};
```

Note that the component now has no reference to Pokemon anymore. It could render anything, no matter if Pokemon, trainers, or something else.

It not only is a generic component not, but it also uses a TypeScript generic for the component props. We use the generic `Item` type for the list of `items`and for the single `item`. When we pass one of the props to this component, if we use it somewhere, React (or rather TypeScript) knows that the other prop has the same type.

This is how we would use it to achieve the same output as in our initial example:

```
import React from "react";
import { List } from "./components/List";
import { getPokemons } from "./api/pokemon";

function App() {
  const pokemons = getPokemons(); // returns some fix dummy data

  return (
    <List
      items={pokemons}
      renderItem={(pokemon) => (
        <li key={pokemon.name}>
          <a href={pokemon.url} target="_blank" rel="noreferrer">
            {pokemon.name}
          </a>
        </li>
      )}
    />
  );
}

export default App;
```

If we pass `items`, which is of type `Pokemon[]` first, then the single item in `renderItem` is inferred to be `Pokemon`. We could also pass the single item in `renderItem` first, but in that case, we have to explicitly type it like `renderItem={(pokemon: Pokemon) => (`. Now, `items` must be of type `Pokemon[]`.

Now the parent controls how the items of the list are rendered. That is nice, but it has a major flaw: `<List />` renders an outer `<ul>` and therefore we must return an `<li>` from `renderItem` in order to end up with valid HTML. We would have to remember that and we can't use it for more generic lists where we don't want to use an `<ul>` at all. This is where the as-prop comes into play.

## React As Prop

Our goal: We not only want to reverse control over how a single item is rendered but also for the HTML tag used by `<List />`. We can achieve that with the as-prop:

```
import React from "react";

type Props<Item, As extends React.ElementType> = {
  items: Item[];
  renderItem: (item: Item) => React.ReactNode;
  as?: As;
}

export function List<Item, As extends React.ElementType>({
  items,
  renderItem,
  as
}: Props<Item, As>) {
  const Component = as ?? "ul";
  return <Component>{items.map(renderItem)}</Component>;
}
```

Now the parent component can decide what HTML tag `<List />` renders. It could also make it not render an outer tag at all by passing `as` like this `<List as={React.Fragment} />`. Per default `<List />` renders an `<ul>` tag. Therefore our current usage in the parent doesn't have to change at all.

*Note: We can't just use the `as` prop like `<as>content</as>` because that would not be valid JSX. Non-native HTML tags have to be uppercased. You could uppercase the `As` prop in the first place but I personally find it quite awkward.*

There is still one caveat. If we decide to render an outer `a` or `img` tag (which we probably won't in our example, but it is very relevant when dealing with the as-prop in general), then we can't pass required props like `href` or `src` to `<List />`. Not only TypeScript would complain, but also the props would not be forwarded to the `<Component />`within `<List />`. This is how we can deal with that (this is the final version):

```
import React from "react";

type Props<Item, As extends React.ElementType> = {
  items: Item[];
  renderItem: (item: Item) => React.ReactNode;
  as?: As;
}

export function List<Item, As extends React.ElementType>({
  items,
  renderItem,
  as,
  ...rest
}: Props<Item, As> & Omit<React.ComponentPropsWithoutRef<As>, keyof Props<Item, As>>) {
  const Component = as ?? "ul";
  return <Component {...rest}>{items.map(renderItem)}</Component>;
}
```

We now pass all props besides `items`, `renderItem` and `as` to `Component` by using the spread operator for `rest`. Now we technically could pass an `href` from the parent component, but TypeScript would still complain. We can solve this with `React.ComponentPropsWithoutRef<As>`, which results – as the name already implies – in all prop types of the `As` component excluding the `ref` prop. If we would now pass `as={"a"}`, TypeScript autocomplete would suggest props from an `<a>` tag, like `href` to us.

What is `Omit<React.ComponentPropsWithoutRef<As>, keyof Props<Item, As>>` doing here? If we would include something like `href: MyHrefType` in our `Props` type and use `as="a"`, then we would end up with an error when trying to pass any `href`: `Type 'string' is not assignable to type 'never'.`. `Omit` excludes all prop types that we explicitly defined in our `Props` type from the result of `React.ComponentPropsWithoutRef<As>`. In our case – passing `as="a"` – `Omit<React.ComponentPropsWithoutRef<As>, keyof Props<Item, As>>` would not include the `href` type anymore. We now can pass the `href` prop of type `MyHrefType` again. **TLDR;** it deduplicates types.

## The Result

Now our `<List />` is truly generic and reusable for a lot of cases. I often still prefer creating something like a `<PokemonList />` which uses the `<List />` as a building block:

```
import React from "react";
import { Pokemon } from "../api/pokemon";
import { List } from "./List";


type Props = {
  pokemons: Pokemon[];
};

export function PokemonList({ pokemons }: Props) {
  return (
    <List
      items={pokemons}
      renderItem={(pokemon) => (
        <li key={pokemon.name}>
          <a href={pokemon.url} target="_blank" rel="noreferrer">
            {pokemon.name}
          </a>
        </li>
      )}
    />
  );
}
```

Now we can easily create something like `<PokemonDetailsList />`, `<TrainersList />` or whatever – or use the `<List />` directly.

## Conclusion

React techniques like render props and the as prop enable us to build our own reusable, generic building blocks. Typing those generic components isn't that easy (at least this is how I feel about it). Therefore we also learned how to type those generic components using TypeScript.

I admit that this `<List />` component is a contrived example as it seems to offer not that many benefits compared to our initial solution. But the techniques that I have shown here are very relevant and the simple example allowed me to focus on the techniques. These techniques are widely used in libraries like [Chakra UI](https://chakra-ui.com/) and [headless UI](https://headlessui.dev/) which I really enjoy using.

There are many other techniques for creating reusable React components. Some of them utilize React context and composition (rather than inheritance). These techniques might be the topics for future articles.