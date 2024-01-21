### File Structure

```bash
.
├── .github
│   ├── ISSUE_TEMPLATE
│   │   └── bug_report.yml
│   ├── workflows
│   │   ├── chromatic.yml: Job marco to push to Chromatic
│   │   ├── deploy-chromatic.yml: Deploy storybook to Chromatic
│   │   └── docs-add-modified-date.yml: Add modified date to docs files when changed
│   └── PULL_REQUEST_TEMPLATE.md
├── .storybook: Settings of storybook runtime
├── .vscode
│   ├── settings.json: File format, i18n config
│   ├── launch.json: NPM scripts shortcut for debugging
│   └── extensions.json: Recommanded vscode extension
├── assets: Images, any bundle-sensitive files
├── components
├── composables: Vue hooks
├── config
│   ├── config.ts: General config
│   ├── local.ts: Config to be leveraged when local developement
│   └── nuxt-config.ts: Nuxt config. Lifted up to distribute to other environments
├── i18n
│   ├── zh
│   └── en
├── layouts: Layouts that apply to specific pages
│   └── default
├── pages: Route
├── plugins: Vue plugins
│   ├── i18n
│   └── vue-query
├── public: Any files that will be exposed under the /
├── server: NOT USED. SSR
├── services
│   ├── api: Handling API end points
│   ├── axios: Axios instance and related config
│   ├── composites: Complex logic related to API
│   └── models: Types, interfaces and zod models for data
├── stores: Vue store, ie. state manager, Pinia 🍍
├── stories: Storybook stories
├── styles: Some CSS files, wait for evaluated
├── theme: Design tokens, system
└── utils
```

### Other Files On The Root

```bash
├── .eslintignore
├── .eslintrc.json
├── .gitignore
├── .npmrc
├── .prettierrc
├── app.vue: App entry
├── components.json: shadcn vue config
├── nuxt.config.ts
├── package-lock.json
├── package.json
├── tailwind.config.js
├── tsconfig.json
└── vitest.config.ts
```

### File Formatting and Linting

STAR uses `prettier` for formatting, and `eslint` for linting. But we didn't install `prettier` as a dependency, because [integrating `eslint` and `prettier`](https://prettier.io/docs/en/integrating-with-linters.html) is such a pain. Both of them have settings on how to format a file, there is a `eslint` plugin called [eslint-config-prettier](https://prettier.io/docs/en/integrating-with-linters.html).

#### Format on save

> Install the `prettier` vscode plugin [here](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

Please choose prettier as your default formatter on your IDE.

You can turn on "format on save" on your IDE, it can be specific to the code you changed (based on git), or the whole file.

Make sure that your _end-of-line_ (eol) is `LF` on your IDE, which is the case on Linux and Mac, but it would be `CRLF` for window, so make sure that 🙏.

you can run this to lint the code following by formatting:

```bash
npm run lint:fix && npx prettier .
```
