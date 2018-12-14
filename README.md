# Herbie Dashboard Monorepo
This monorepo contains modules of Cobli's main Dashboard and other microapps.

## Contents
1. [Setup](#setup), [build, run](#build-and-run) and [deploy](#deploy)
2. [Project architecture](#project-architecture)
2. [Development workflow](#development-workflow)

## Setup
Clone this repository to your workplace

```
$ git clone https://github.com/Cobliteam/herbie-dashboard.git
```

Install Node.js (>= 8)
```
$ brew install node
```

Install Sass
```
$ gem install sass
```

Install Lerna (it **must** be this forked version).
```
$ yarn global add https://github.com/Cobliteam/lerna/archive/v3.4.4.tar.gz
```

And finally, install all dependencies. **You must run this command from the root of the project** so lerna can bootstrap and hoist dependencies.
```
$ yarn
```

## Build and run

### Cobli's main Dashboard

You must run from the root folder of the project

```
$ yarn build <env>
$ yarn serve
```

or

```
$ yarn serve:<env>
```

The `<env>` options are `local`, `dev`, `beta`, `prod`

### Welcome

[Welcome's README](packages/welcome/README.md)

```
$ cd ./packages/welcome
$ yarn start
```


## Deploy

```
$ yarn create-deploy <package_name> <env>
```

The `<package_name>` options are `dashboard` and `welcome`<br>
The `<env>` options are `dev`, `beta`, `prod`


## Project Architecture

### Folder structure

- `packages` (Contains all modules)
  - `dashboard` (Main project importing everything else)
  - `dashboard-legacy` (Plain js+jquery code <- slowly dying)
  - `dashboard-builder` (Where the whole dashboard is compiled to in order to be deployed)
  - `base-components` (UI shared components)
  - _`other packages ...`_
- `scripts`
  - `create-package.js` (Create new package)
  - `test-package.js` (Run jest test)
  - `storybook.js` (Run storybook)
- `template` (New packages' template, for use of `create-package`. This probably shouldn't be edited.)
- `Gruntfile.js` (Scripts for building and deploying (primarily the legacy) dashboard)
- `lerna.json` (Lerna monorepo configs)
- `tsconfig.json` (Typescript configs)
- `tslint.json` (Typescript linter configs)
- `herbie-dashboard.d.ts` (Typescript types declarations for non-typescript code)

### Build process graph

_TL;DR_
<br>
It all starts on `package/dashboard` build. Then imported packages are compiled via webpack + babel-loader, with the exception of `packages/dashboard-legacy` that is built via grunt scripts. Those compilations happen on every build, but we have hot-reload (`yarn serve:env`)! So that only changed parts of the code are re-built.

_Now a (short) longer explanation:_
The building process start with `yarn build <env>` being run on the root directory. Then, two things happen
1. `dashboard-legacy` is build for that `<env>` following grunt-defined steps (see `Gruntfile.js` for `build:<env>` directives);
2. And once that's completed, `dashboard` is built.

The package `dashboard` is a CRA ([create-react-app](https://github.com/facebook/create-react-app)) that uses [webpack]() for compiling/transpiling `tsx` and `jsx` code to pure `js` via [babel](https://babeljs.io/) using [this preset](https://github.com/facebook/create-react-app/tree/master/packages/babel-preset-react-app). Once webpack starts parsing the `dashboard` package it'll follow `import`s to all the other packages included there.

> Usually CRA would only compile files that exist inside the CRA project itself. So all other packages would need to build themselves and load anything they need (`css`, `svg`, etc). To circumnvent that, we use [`craco`](https://github.com/sharegate/craco) to extend the scope of CRA compiling step, now including `packages/` folder.
> This config is in `packages/dashboard/craco.config.js`. Also, to tell CRA where each other project's entry point is, the line `"main": "src/index.tsx",` is included in each of those projects' `package.json`.


## Development Workflow

This is how one would create a new package to use it inside our dashboard.
<br>
Need to run this command on the root folder

```
yarn create-package <package-name>
```

> _Note that this command is **not** intended to be used for creating standalone (micro)apps, only for packages that will be imported by `dashboard` project_. If you want to create a standalone microapp, use `create-react-app` instead.

While developing run _from inside the `<package-name>` folder
```
yarn storybook; # start live storybook
yarn test; # run tests in watch mode
yarn tslint; # check typescript files
```

Check `<package-name>/package.json` for more info on those commands.

When you feel that the package is ready for prime-time (hopefully with tests and stories ;) ), you can import it inside the dashboard.

#### Importing a new package inside dashboard project

1. Check `<package-name>` version inside its `package.json` (by default the value is "0.1.0");
2. Now add `@cobli/<package-name>` in the `dependencies` field of **`dashboard`'s `package.json` file**;
3. Then also add a line on `dashboard/src/global.d.ts` with `declare module '@cobli/<package-name>';`;
4. Finally, run `yarn` from the top folder `herbie-dashboard`.
