# Component implementation

STAR leverages [Primevue](https://primevue.org/), [Radix Vue](https://www.radix-vue.com/) and [Shadcn Vue](https://www.shadcn-vue.com/) for building up components.

Where Primevue is a powerful component library that provides great API for creating complex components while maintaining the ability to customize.

Radix Vue and Shadcn Vue are actually the same, Radix Vue is a _headless_ component library that provides structured functional components without any styling, which leases the ability to us to customize the component ourselves based on the design system. Shadcn Vue built on top of Radix Vue and provides a basic style.

## Atomic Design

STAR leverages the idea of Atomic Design, which splits the component into 5 categories:

1. Atoms
2. Molecules
3. Organisms
4. Templates
5. Pages

STAR mainly put components into _atoms_ and _pages_. That is because I found it to be confusing to distinguish between atoms, molecules and organisms, they just vary in size and have some the usage as long as they are reusable.

For these aren't meant to be re-used, they are considered to be either _templates_ or _pages_. The difference is that templates didn't have the actual data, instead templates were just a _dumb_ display for the data. While pages are assembled with the data. I found this to be similar to the [_container/presentational_](https://www.patterns.dev/react/presentational-container-pattern/) pattern in older React. Which separated the data display from the data, the _presentational_ knows nothing about the data, while the _container_ is responsible to feed the data into it (that is why it called container, it contains the display), form a _smart vs dumb_ pattern.

In modern React (above 16), this pattern is rarely used because we can just extract the data logic using _hooks_. I feel the same here, so STAR didn't prefer to separate the data and display, it is okay to do _data fetching_, _data transformations_, _view-model transformations_ and _UI logic_ within a single component, as long as we separate the hooks. With that, we can create lesser components hierarchy, lesser data transformations, and get faster development speed.

So we do:

```js
// Dashboard.vue
// template
<Input :data="data1"/>
<Dashboard :data="data2"/>

// setup
const data1 = useData1();
const data2 = useData2();
```
Instead of:
```js
// template
<DashboardTemplate :data1="data1" :data2="data2" />

// setup
const data1 = useData1();
const data2 = useData2();
```

So our pages would consist of some atomic components, along with the data. We can still test the pages using [Mock Service Worker](https://mswjs.io/). Which based on the network layer, that means we can test any data without modifying the code. And it is compatible with node, browser and storybook.

## Writing CSS
STAR leverages [Tailwind](https://tailwindcss.com/) for styling. Which is a utility CSS framework that aims to provide a scalable way to write CSS, with lesser cognitive overhead to look at each classes and worry about unintended behavior when writing CSS [#](https://tailwindcss.com/docs/utility-first#overview).

As tailwind taken care of the scope, but not the specificity problem, STAR also leverages [Tailwind Variants](https://www.tailwind-variants.org/) to handle style overwriting. Tailwind Variants provided a way to describe how `props` affect the style, under the hook, it uses *tw-merge* to merge tailwind classes.

For atomic components, we hope that a component can be composited or extended into a more specific component. For that, the props of a component need to be merged along with the component tree, all the way down. *Primevue*,*MUI* and *NextUI* use a similar approach to write the style in object as a props to the component. STAR used a hook `usePassThrough` to pass the style all the way down to the component.

STAR intended to support these styling solutions:
1. Global CSS
2. CSS Modules
3. Tailwind CSS

This is implemented by passing the class name, for CSS modules, we uses [@vanilla-extract/css](https://vanilla-extract.style/) for generate the CSS module class names. 

It will look like:
```js
<AtomicComp :pt="{
  root: "px-2", // tailwind classes
  wrapper: "sui--home-page__open", // global classes
  icon: "container" // CSS module classes
}"/>
```
The CSS module classes can be generated with:
```js
import { style } from '@vanilla-extract/css';

export const container = style({
  padding: 10
});
```
That will result in something like:
```css
.app_container__sznanj0 {
  padding: 10px;
}
```
in the style sheet.

In general, a styling object called `pt` is passes in a reusable component, the style of `pt` should override the that of the component. And with the `pt` passing down layer by layer, we can customize the component even with arbitrary layers.

[![](https://mermaid.ink/img/pako:eNp9jstqwzAQRX9FzKoFx7VlYVmiBNIEuioU0lXRIsKWHZPogSzTpo7_vapLF9l0VjOXc4Y7QW0bBRzas_2oj9IH9LYTBsXZ3Al4RK-yU-hhLeD-N12t1lcnh6E3HTq4cLiip4V7sV7tlav7tq-3Vrv_ne3iPCuj_C0uDCSgldeyb2Kr6eeBgHBUWgngcW2kPwkQZo6cHIPdX0wNPPhRJTC6Rga162XnpQbeyvMQUyfNu7X6D4on8Ak-gRc0T3HJGGaEElwQksAFOCVpQfKyooSxLC8zOifwtfhZWpWkxDTHuKIsw1UxfwN6HV82?type=png)](https://mermaid.live/edit#pako:eNp9jstqwzAQRX9FzKoFx7VlYVmiBNIEuioU0lXRIsKWHZPogSzTpo7_vapLF9l0VjOXc4Y7QW0bBRzas_2oj9IH9LYTBsXZ3Al4RK-yU-hhLeD-N12t1lcnh6E3HTq4cLiip4V7sV7tlav7tq-3Vrv_ne3iPCuj_C0uDCSgldeyb2Kr6eeBgHBUWgngcW2kPwkQZo6cHIPdX0wNPPhRJTC6Rga162XnpQbeyvMQUyfNu7X6D4on8Ak-gRc0T3HJGGaEElwQksAFOCVpQfKyooSxLC8zOifwtfhZWpWkxDTHuKIsw1UxfwN6HV82)

This should be familiar to people who used *styled components*. I found that the ability to pass through the style is critical, and the implementation I did here is very flawed and complex compared to *styled component*'s API. `@vanilla-extract/css` has a similar usage for that, and it may worth taking a look.
