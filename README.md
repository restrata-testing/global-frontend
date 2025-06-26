# NextGen Restrata Platform 

## What is this?

This is the front end component of Restrata Global initiative, the part of it written in React. Global Map and everything around it, up to Unified Experience (which is a separate repository, built in Angular).

## How to work with this

Before contributing to this project, please get acknowledged with this document thoroughly. It explains common approaches and workflows used in this project, and by itself is a contribution guide.

>**Note:** this document is a subject for change during the development process, the figuring out of the approaches convenient for us is a flowing process and this document summarises them together.

General rule:

1. if you can't understand something when working with the project, it's likely to be described here

2. if it's not described either here or in the links that are found here, ask from the Frontend Team.

3. They will probably have an answer for you, and if he does - it might be necessary to put it into this doc.

## Tech stack

**Running the project**

- [`node`](https://nodejs.org/en/) v16.x LTS to run everything
- [`yarn`](https://www.npmjs.com/package/yarn) v1.22.x to install deps and run scripts

**Deploying the project**

- [`docker`](https://www.docker.com/) v20.10.x to build the images

**Working with the project**

>**Note:** these are installed when you run dependency installation with `yarn`, no need to do it manually

- [`react`](https://reactjs.org/) - main FE tool
- [`typescript`](https://www.typescriptlang.org/) - main language
- [`mapbox-gl-js`](https://docs.mapbox.com/mapbox-gl-js/api/) - Map library, also get acquainted with the other [Mapbox](https://www.mapbox.com/) products, we might use some of them in the future
- [`react-map-gl`](https://visgl.github.io/react-map-gl/) - React connector for Mapbox GL JS
- [`mantine`](https://mantine.dev/getting-started/) - UI library
- [`rematch`](https://rematchjs.org/) - state management ([Redux](https://redux.js.org/) wrapper)

## Deployment

> **Note:** this section is only for deployments, not for local development. Please launch the project with `yarn` locally, without using Docker.

To propagate the changes to a new deployment, you'll need to re-build the image:

```bash
docker-compose build
```

To launch the image locally (to be served as `http://localhost:80`) you'll need to:

```bash
docker run -p 80:80 global-frontend
```

## Available scripts

- `start` runs the project in development mode
- `build` makes a production build of the application
- `test` runs the tests
- `lint:js` runs ESLint and notifies about coding style violations in the project
- `lint:styles` runs Stylelint to achieve the same ESLint does for JS, but for the stylesheets

## Before you start

### Code linting

This project has a defined coding style, controlled by ESLint and Stylelint, the rulesets can be found in `.eslintrc.json` and .`stylelintrc.js` correspondingly.

Please install ESLint and Stylelint plugins for your favourite code editor, and enable the autofix-on-save feature of both. This will speed you up drastically.

>**Note:** If you find any rules annoying / unnecessary, please verbalise it so that we can configure the tool to be convenient for everybody! IT IS VERY IMPORTANT THAT YOU VERBALISE IT. The rules are there for a reason and it's best for all of us to understand the reasons, and be aligned on why we do things this way and not the other.

### Pushing the code

The `master` branch of the GitHub repository is protected - pushing your code there right away is restricted. In order to make an update to the `master` branch, the flow is like this:

1. Ensure your code to be compliant with the linter rules
2. Push your branch to the remote repository
3. Open a Pull Request to the `master` branch
4. Wait for the CI to check your code again with `eslint` and `stylelint`
5. If any checks fail - go to step one
6. Checks are successful - assign a reviewer and let them know that your PR is ready to be reviewed!

## Project structure

The project is split into modules, which are to be put into the `packages` directory. Each module should have the same file structure as all the other modules.

We also use some functionality we are likely to modify in the future. Such packages (TS only, for now) should be cloned into `forks` directory, cleaned up and compiled alongside the project.

Some directories might be absent if not necessary in the corresponding module. Adding new ones that are not yet seen in the other modules is a subject to talk about with the Frontend Team.

Please stick to a flat file structure as long as it's possible and appropriate. The file structure defines how the new piece of functionality (be it a component, a new api connector, a hook) should be structured file-wise. In *some* cases it's convenient to keep some stuff together. Don't get too deep nesting-wise though.

### Allowed module directories

These directories are allowed to be children of a module. They have strictly defined structure (see examples all across the app). There will be cases when you need something else - please communicate these cases to verify that the idea is valid and we should update the doc accordingly.

`api` - everything related to fetching data from external resources (our backend, other backends etc.)

`controls` - Map controls (read more in "Working with the Map" section)

`layers` - Map layers (read more in "Working with the Map" section)

`components` - all the React components that don't fit into any other category

`store` - global state declarations (Redux / Rematch)

`types` - reusable type declarations

`workers` - web worker declarations

`icons` - icons of the module (svg / png)

`util` - reusable utility functions

`plugins` - React wrappers for Mapbox plugins

`hooks` - reusable hooks

### Module structure example

As you see below, modules have directories that are necessary for their functionality. We don't create empty directories. If you have `controls` in a module - you will have a `controls` directory, otherwise you won't.

```
some-module/
├── api/
│   └── asset-location.ts
├── layers/
│   └── // same structure as components (below)
├── controls/
│   └── // same structure as components (below)
├── components/
│   └── AssetLocationMap/
│       ├── AssetLocationMap.container.tsx
│       ├── AssetLocationMap.component.tsx
│       ├── AssetLocationMap.styles.ts
│       ├── AssetLocationMap.config.ts
│       └── index.ts
├── icons/
│   └── some-icon.svg
├── hooks/
│   └── useSomething.ts
├── store/
│   └── asset-location/
│       ├── asset-location.state.ts
│       ├── asset-location.model.ts
│       ├── asset-location.reducers.ts
│       └── asset-location.effects.ts
├── types/
│   └── asset-location.ts
├── util/
│   └── some-util.ts
├── workers/
│   ├── some-worker.external.ts
│   ├── some-worker.isolated.ts
│   └── some-worker.types.ts
└── plugins/
    └── SomePlugin/
        └── index.tsx
```

## Anatomy of a component

Any React component in this application is located in the `components` directory of a corresponding module and has a defined structure, see below.

```
# In this example, let's say that we are implementing UserList

src/
└── components/
    └── SomeComponent/
        ├── index.ts
        ├── UserList.component.tsx
        ├── UserList.container.tsx
        ├── UserList.config.ts
        └── UserList.styles.ts
```

### What these files **are**:

`UserList.component.tsx` contains only the *render* template of the component. Basically, it is an old-school React Functional component.

`UserList.container.tsx` contains only the *business* logic of the component. Any event handlers, connections to global states, prop filtering - should be here. Stateful. "Brain" of UserList.

`UserList.styles.ts` contains styling declarations for the corresponding component (only), encapsulated. This project uses css-in-js [approach ensured by Mantine](https://mantine.dev/theming/create-styles/).

`UserList.config.ts` contains all the constants necessary for this part of the functionality. It is important to put them into a separate file for the other components to import, to give way for a better code splitting later in the project.

`index.ts` exports default from the proper file. If there is no container - exports the default export from the `.component.js`. You want to add a container? Implement it and change the export in this file to `.container.js`.

### What these files **are not**:

`UserList.component.tsx` should be stateless, should not define any additional functionality. This is *only* a visual representation of the application's state. "Face" of UserList. To add an event handler to a button of this template, define it in the **container**.

`UserList.container.tsx` should be stateful and should not contain any render logic! The `return`s of this will be very similar across the project:

```tsx
const UserListContainer: FC<Props> = ({
  someProp,
  ...otherProps
}) => {
  /* Logic here */
  /* .......... */

  return (
    <UserListContainer
      users={ users }
      // ...other container-defined props
    />
  )
}
```

`UserList.config.ts` is declarative and does not contain any logic, only the constants used in the components. This is necessary to enable smarter code splitting - unfortunately, Webpack's treeshaking does not cover everything. Prefer keeping constants within these files, not in `component` or `container` ones.

`index.ts` contains only the reexport from the corresponding file. Nothing else.

## Working with the Map

The application is map-centric. This means that at (almost) all times the central focus of user's attention is going to be on the map component. You will see it right away when you start the project.

At the moment, there are no views in the application where the map disappears from the screen. Therefore, we need to put all the stuff we can on the map, and utilise the map APIs to visualise data, interactions and everything else.

We use Mapbox to do maps whenever they are necessary. We use a [React wrapper](https://visgl.github.io/react-map-gl/) for it, since it's a React project. All this wrapper does is provide React components that trigger `mapbox-gl-js` APIs, knitting them into React-centric world in a native manner.

Remember that this wrapper is a level of abstraction over Mapbox sometimes, so if you have a `Map` ref from `useMap` hook of `react-map-gl`, it exposes only the "safe" properties of the corresponding library.

If you are **absolutely sure** that you **need** to do something considered **unsafe** (you probably shouldn't do that), there will be a way to get to the `mapbox-gl-js` level, which is "unsafe" in React paradigm.

Mapbox has a [very extensive collection](https://docs.mapbox.com/mapbox-gl-js/example/) of examples for its capabilities. Please get acquainted before starting to work with Mapbox.

There are several types of stuff you can put on the map:

1. [Sources](https://docs.mapbox.com/mapbox-gl-js/style-spec/sources/) - data sources for the layers. Layers get data from sources.

2. [Layers](https://docs.mapbox.com/mapbox-gl-js/style-spec/layers/) - visualised form of data retrieved from a source.

3. Controls - buttons (or anything else) rendered **on top of** the map canvas, that you can press to do something. DOM content with absolute positioning. Some examples [here](https://docs.mapbox.com/mapbox-gl-js/api/markers/), if you are creating one for this project please refer to examples within the project, e.g. `core/controls/DataOptionsControl`.

4. [Popups](https://docs.mapbox.com/mapbox-gl-js/example/popup/) - see `useMapPopup` util of this app, and use cases of that.

## State management principles

This application uses **Redux** as its state management solution. Though, knowing that Redux has a lot of boilerplate code by default, we have decided to go with a wrapper around it - [Rematch](https://rematchjs.org/).

To read further, please get acquainted with the Rematch documentation, at least on base level.

When you have written a store, you should introduce it to the global state by adding it to `core/store/index.ts` alongside all the other declarations.

The simplest (atm) example of state is `core/store/drawer`.

We prefer splitting our models into separate files:

```
src/
└── store/
    └── some-store/
        ├── some-store.state.ts
        ├── some-store.reducers.ts
        ├── some-store.effects.ts
        └── some-store.model.ts
```

`some-store.state.ts` - State typings and default value declarations

`some-store.reducers.ts` - Work as default Redux (Flux) reducers, the reducers themselves should use uppercase (e.g. a reducer to close a drawer should be called `CLOSE_DRAWER` rather than `closeDrawer`).

`some-store.effects.ts` - A way to dispatch multiple actions / add some processing to the results. An abstraction over reducers. Prefer reducers for simple actions, effects for the ones that require more processing logic.

`some-store.model.ts` - Unifies all of the above under one roof. [Doc.](https://rematchjs.org/docs/api-reference/models)

## Adding a worker

Webpack 5 used in this project ensures simple interactions with Web Workers.

>**Note:** you can refer to `core/workers/example-worker` to get an example of worker functionality.

A worker should be located in `workers` directory of its module and should consist of 3 files.

```ts
src/
└── workers/
    └── some-worker/
        ├── some-worker.types.ts
        ├── some-worker.external.ts
        └── some-worker.isolated.ts
```

`some-worker.types.ts`: A worker of this project is a function with 1 argument separated to its own thread. As any other function, it has typings for this argument and its return type. These types are declared in this file and imported in both files below to ensure type-safety of the facades' APIs.

`some-worker.isolated.ts` Declares worker's internal mechanics (isolated in the worker's own context) - how it accepts messages, how it processes them and what it returns. This file should never be imported from anywhere, the only legitimate usage is described in the point below.

Always use `WorkerInternalFacade` class to ensure type safety in your worker's internals.

>**Note:** Never access `self` directly, use the functionality offered by `WorkerInternalFacade`.

```ts
WorkerIsolatedFacade.create<
  ExampleWorkerMessageType,
  ExampleWorkerResponseType
>()
  .listen((message, postMessage) => {
    const { question } = message;

    postMessage({
      answer: '42'
    });
  });
```

`some-worker.external.ts` Is an abstraction over the message-sending process to the worker declared in `internal.ts`. Should create the worker using WorkerExternalFacade (**exactly** as shown below) and export a function that posts a message to the worker.

>**Note:** Never export the worker itself (`exampleWorker` in the example below) directly, export a function that accesses it.

```ts
const exampleWorker = WorkerExternalFacade.create<
  ExampleWorkerMessageType,
  ExampleWorkerResponseType
>(
  new Worker(new URL(
    './example-worker.isolated.ts',
    import.meta.url
  ))
);

const askQuestion = async (question: string) => {
  const response = await exampleWorker.execute({
    question
  });

  console.log(response);

  return response;
};

export { askQuestion };
```


## Why Did You Render
We use the [why-did-you-render (WDYR)](https://github.com/welldone-software/why-did-you-render#readme) library.
If you want to "debug" the reasons of re-render some of your components, add this fragment below: 
```ts
// eslint-disable-next-line @typescript-eslint/no-explicit-any
(NavigationSidebar as any).whyDidYouRender = true;
```

