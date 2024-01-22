# Component implementation

STAR leverages [Primevue](https://primevue.org/), [Radix Vue](https://www.radix-vue.com/) and [Shadcn Vue](https://www.shadcn-vue.com/) for building up components. 

Where Primevue is a powerful component library that provides great API for creating complex components while maintaining the ability to customize. 

Radix Vue and Shadcn Vue are actually the same, Radix Vue is a *headless* component library that provides structured functional components without any styling, which leases the ability to us to customize the component ourselves based on the design system. Shadcn Vue built on top of Radix Vue and provides a basic style.

## Atomic Design
STAR leverages the idea of Atomic Design, which splits the component into 5 categories:
1. Atoms
2. Molecules
3. Organisms
4. Templates
5. Pages  

STAR mainly put components into *atoms* and *pages*. That is because I found it to be confusing to distinguish between atoms, molecules and organisms, they just vary in size and have some the usage as long as they are reusable. 

For these aren't meant to be re-used, they are considered to be either *templates* or *pages*. The difference is that templates didn't have the actual data, instead templates were just a *dumb* display for the data. While pages are assembled with the data. I found this to be similar to the [*container/presentational*](https://www.patterns.dev/react/presentational-container-pattern/) pattern in older React. Which separated the data display from the data, the *presentational* knows nothing about the data, while the *container* is responsible to feed the data into it (that is why it called container, it contains the display), form a *smart vs dumb* pattern.

In modern React (above 16), this pattern is rarely used because we can just extract the data logic using *hooks*. I feel the same here, so STAR didn't prefer to separate the data and display, it is ok to do *data fetching*, *data transformations*, *view-model transformations* and *UI logic* within a single component, as long as we separate the hooks. With that, we can create lesser components
