### Module Structure

```bash
├── index.ts
├── <ComponentName>.styles.ts
├── <ComponentName>.types.ts
├── <ComponentName>.vue
├── <ComponentName>.test.ts
└── any subcomponent directories
```

All modules must have a `index.ts` that defines the external API to the public.

### Module Design

STAR prefers module design pattern. Every folder is a module.

For example, to create a `Car` module, which exposes a `Car`component. it would be the same for this two:

```bash
└── Car.ts
```

```bash
└── Car
    ├── index.ts
    └── Car.vue
```

As people from outside can import it the same way:

```js
import { Car } from "./Car";
```

For a simple module, a single file might be enough, but for a complex one, the later is preferred.

### Why

Sticking to modular design gives us some advantages.

#### Open Closed

It fits the open-closed principle. Implementation details are hidden from the user, and user can only use it through the public interface.

#### Outsider favored

New users can peek the module with their IDE's intellisense. This make it easy to use even if they have no idea of the module.

#### Consistent import path

User import the module from a public interface, this means the changes within the module will not affect user as long as the public interface is maintained. User can also import multiple API from a module, which leads to shorter and consistent import path.

#### Named imports favored

This favored named imports over default imports. Which reduces the diversity of the code and usually more readable.

### Avoiding circular imports

Circular imports can be easy to occur:

1. Importing itself
2. Importing a file that uses itself (eg. A->B and B->A)
3. Importing a module that contains itself

Usually `eslint` will raise an error.

This is an example, say we want to create a `Car` module under `@/components/` and it needs a module `Icon` which also comes from `@/components/`.

```javascript
import { Icon } from "@/components"; // This is wrong because `@/components/` also contains you
```
This is correct
```javascript
import { Icon } from "@/components/Icon"; // 👍
```
This usually don't cause error, as the bundler can figure out itself.

### Orphan

A module that hasn't been imported is a orphan. This doesn't mean it is useless, as it may be used by `node` and has some side-effects. And it is normally harmless as it would be tree-shaken during the build time.

#### Tools

There are tools to check your import dependency graph

1. eslint
2. nuxt's devtool
3. dependency cruiser
