## My Favorite Tech Stack for 2022 

 I just recently tweeted my favorite tech stack for 2022 (inspired by @[Jon Meyers](@dijonmusters) tweet). I'd like to share some more thoughts about my choices in this post.

%[https://twitter.com/JannikWempe/status/1473935933447839748]

## Frontend

First of all: I love frontend development. It is the direct touchpoint to the user for websites / -apps. It is the first impression for the user.

There is so much out there. It can be paralyzing. I already used a variety of frontend frameworks: React (CRA, Gatsby, NextJS), Vue, Angular, Svelte (SvelteKit). And as far as styling goes, I've tried a lot of things too: CSS (modules), SASS, CSS-in-JS, Material, Bootstrap, Bulma, Quasar, Tailwind, Chakra UI, and more. Therefore you can assume I've tried quite a lot and my choices are not the only ones I know. (Not saying that other tools don't do the job and are inferior. It also comes down to personal preference.)

### Svelte / SvelteKit

This blog post goes into detail [Why Svelte is different - and awesome](https://blog.jannikwempe.com/why-svelte-is-different-and-awesome). I just really enjoy using Svelte. It is more concise than React and more performant. Stores and animations are also great features. There is a reason why Svelte was the [most loved web framework in the Stack Overflow Developer Survey 2021](https://insights.stackoverflow.com/survey/2021#section-most-loved-dreaded-and-wanted-web-frameworks).

I think Svelte will make a jump in popularity with the release of SvelteKit version 1.0 which is my default for every Svelte app. In addition to that, Rich Harris (the creator of Svelte) was hired by Vercel and is now working full-time on Svelte / SvelteKit. 

Svelte will rise and shine ✨

[Learn more about Svelte](https://svelte.dev/)

[Learn more about SvelteKit](https://kit.svelte.dev/)

### NextJS

Currently, I am still often using NextJS. It is great! Just like SvelteKit is my default for every Svelte project, NextJS is my default for any React project. Mostly for the same reasons: Static Site Generation (SSG), Server-Side Rendering (SSR), built-in file-based routing based, and more. 

The ecosystem for React is way larger than the Svelte one and more people are familiar with React. Therefore NextJS is my choice for working together with other React-devs and when relying on a certain library that is not (yet) available in Svelte (can't think of any out of my head). In addition, the demand and job market for React / NextJS are much larger than for Svelte / SvelteKit.

[Learn more about NextJS](https://nextjs.org/)

### TailwindCSS

I love styling with utilities based on a predefined and easily customizable theme. If you have read my post [Debunking Tailwind Counterarguments](https://blog.jannikwempe.com/debunking-tailwind-counterarguments) you already know that I am a big fan. Most often I use [Headless UI](https://headlessui.dev/) as an addition to get some functionality like a select or a modal. I've also bought [Tailwind UI](https://tailwindui.com/) in order to move faster and also for some inspiration — and I don't regret it.

I just can't go back to UI libraries like Material UI or Bootstrap anymore 🤷🏼‍♂️

[Learn more about TailwindCSS](https://tailwindcss.com/)

### Chakra UI

Chakra UI is inspired by Tailwind. It is also based on a theme that uses very similar design tokens. The difference to TailwindCSS is that it comes with a lot of components (therefore it is framework-specific; originally created for React but also available for Vue). The components are created with accessibility in mind. Chakra UI feels like a headstart compared to Tailwind when initially getting started but it also is a little less flexible (framework-specific, peer dependencies, etc.) I love both!

[Learn more about Chakra UI](https://chakra-ui.com/)

## Backend

No frontend without backend (at least if you also consider static site hosting as backend). I do not only love frontend but I love backend as well — yes, I know, focusing is not one of my strengths, but I just can't go with just one of them. 

### Vercel

Vercel is my go-to for hosting my projects. It just provides a great Developer Experience (DX). Luckily they are not only the creators of NextJS but now also have Rich Harris and therefore SvelteKit expertise on board.

For some of my projects, Vercel alone is sufficient as it also provides also server-side functions. If it is not sufficient and I just need a little more, like auth, a DB or some storage, I go with Supabase next.

[Learn more about Vercel](https://vercel.com)

### Supabase

Supabase ("The Open SourceFirebase Alternative") is great. It has a great DX, is very easy to use while also being quite powerful, and has a generous free tier (and is also quite cheap beyond that).

Supabase will be sufficient for a lot of use cases as it provides auth, a DB with a good API through their SDK and storage. If it is not sufficient, I go with AWS.

[Learn more about Supabase](https://supabase.com/)

### AWS CDK / Serverless Framework

There is literally nothing you can't do with AWS. In addition, AWS skills make you very attractive on the job market (my LinkedIn inbox is pretty full since I earned the AWS Associate Developer certificate).

I've used Cloudformation, SAM, CDK and the Serverless Framework so far. I can't really decide between CDK and Serverless. I like writing my infrastructure in TypeScript but I also appreciate the ease of use and plugin system of Serverless. Both of them are well suited for serverless architectures which is what I personally almost exclusively use.

[Learn more about AWS CDK](https://aws.amazon.com/cdk/)

[Learn more about Serverless Framework](https://www.serverless.com/)

## Conclusion

That's it. Nothing highly sophisticated. It is mostly the tech I enjoy and I think is valuable in the future. There are also other libraries I really enjoy, like [XState](https://xstate.js.org/) and [React Query](https://react-query.tanstack.com/) (there is also [Svelte Query](https://sveltequery.vercel.app/)). Just to mention a few.

How does your go-to stack in 2021 look like?