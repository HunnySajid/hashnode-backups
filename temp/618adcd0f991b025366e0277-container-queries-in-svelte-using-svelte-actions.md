## Container Queries in Svelte using Svelte Actions 

 Container queries are a neat upcoming CSS feature enabling us developers to build more reusable components. But they [aren't available in browsers just yet](https://caniuse.com/css-container-queries).

In this article you learn about the problem container queries are solving and how to already get that functionality in Svelte by implementing a custom action (with an even better API in my opinion). 

## What Problem do Container Queries Solve?

Nowadays we try to build awesome reusable components. To achieve this, the components need to look good in different sizes, independent of where they are rendered (mobile, desktop, in a sidebar, full screen, ...). But the size of the component doesn't actually depend on the viewport but rather on the container in which it is rendered. So how do apply styles based on the size of a container?

Container queries to the rescue! Container queries are part of [CSS Containment Module Specification](https://drafts.csswg.org/css-contain-3/) which is currently at Level 3 (and therefore unfortunately not available yet).

Container queries are like media queries but different. With media queries, you can adjust CSS based on the viewport width (or height etc.). But with container queries, you can adjust the CSS based on the width of a given container.

This is especially useful when building reusable components. For example, let us imagine the post (highlighted in red) would be a component. Does it matter for the styling of the component how wide the viewport is? Not really (maybe implicitly, but stuff can break if you for example change the pages padding). More important is the actual width of the container in which the component is rendered. This is where container queries come into play.

![container-query-showcase.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1636481770868/0rwxR90K6.png)

> [...] many designs have common components that change layout depending on the available width of their container. This may not always relate to the size of the viewport, but instead, relate to where in the layout the component is placed.
<br>— [MDN - CSS Container Queries](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Container_Queries)

## Using Svelte Actions

*You can play with it in [this Svelte REPL](https://svelte.dev/repl/cd0a53fb3672441eb49ef69a1c918cd7?version=3.44.1).*

Now the [Svelte actions](https://svelte.dev/docs#use_action) come into play. An action in Svelte is a function that you attach to an HTML element by using the `use:` keyword (e.g. `<div use:actionName />`. In the function, you have access to the actual HTML element and optionally additional parameters you can pass to the action. From there on, the possibilities are almost endless. You can tinker with the classes being applied, dispatch custom events, apply styles and everything you can actually do with an HTML element and some JS.

For our purpose, we can use an action in combination with a [ResizeObserver]https://developer.mozilla.org/en-US/docs/Web/API/ResizeObserver). The `ResizeObserver` can observe the size of an element and execute a callback if the size has changed. The size of the actual element is exactly what we are interested in for building our container query functionality.

Then we can pass a configuration object as a parameter to the action. The config contains a list of classes per breakpoint (being min-width) that should be applied if a certain element width is reached or exceeded. We always only want to apply the classes of the largest applicable breakpoint.

And this is how the action can look like:

```javascript
export function containerClasses(node, breakpoints) {
	const resizeObserver = new ResizeObserver(() => {
		const containerWidth = node.clientWidth;
		
		const breakpointsArray = Object.entries(breakpoints);
		const classes = breakpointsArray.reduce((prev, [breakpointStr, classes], idx) => {
			node.classList.remove(...classes);
			const breakpoint = +breakpointStr;
			const prevBreakpoint = idx > 0 ? +breakpointsArray[idx - 1][0] : 0;
			
			if (containerWidth >= breakpoint && breakpoint >= prevBreakpoint) return classes;
			return prev;
		}, []);
		
		node.classList.add(...classes);
  });

  resizeObserver.observe(node);
	
	return {
    destroy() {
      resizeObserver.disconnect();
    },
  };
}
```

And this is how you can use it:

```javascript
<script>
	import { containerClasses } from "./containerClasses.js";
	
	let name = 'world';
	
	const breakpoints = {
		400: ["sm"],
		800: ["md"],
		1000: ["lg"]
	};
</script>

<div use:containerClasses={breakpoints}>
	<h1>Hello {name}!</h1>	
</div>

<style>
	:global(.sm) > h1 {
		background-color: #00aa00;
	}
	
	:global(.md) > h1 {
		background-color: #ffaadd;
	}
	
	:global(.lg) > h1 {
		background-color: #ff00dd;
	}
</style>
```

ℹ️ Note that you have to use the `:global()` selector in order to select the attached class.

Now you can essentially write your container queries by adding classes at certain widths of the element where the action is attached to.

This **works great with Tailwind**. Just add your Tailwind classes in the classes list for the breakpoint and et voilà your Tailwind styles will be applied.

## Conclusion

Svelte actions are awesome! Being able to apply styles based on elements width is much more useful for building reusable components than using media queries. You will be able to do that with plain CSS in a bit but the action I showcased is already able to mimic that behavior.