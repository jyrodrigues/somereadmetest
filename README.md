# Herbie Dashboard Monorepo
This monorepo contains modules of Cobli Main Dashboard and other microapps.

## Contents
1. [Setup, build, run and deploy](#setup)
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

And finally, install all dependencies. _You must run this command from the root of the project_ so lerna can bootstrap and hoist dependencies.
```
$ yarn
```

## Build and run

## Deploy


## Setup
Folder structure:

- Packages (Contains all modules)
  - dashboard (Main project importing everything else)
  - dashboard-legacy (Plain js+jquery code <- slowly dying)
  - dashboard-builder (Where the whole dashboard is compiled to in order to be deployed)
  - base-components (UI shared components)
  - _other packages ..._
- Scripts
  - create-package.js (Create new package)
  - test-package.js (Run jest test)
  - storybook.js (Run storybook)
- Template (New packages' template)

## Creating and developing new packages

Need to run this command on the root folder

```
yarn create-package <package-name>
```

> _Note that this command is **not** intended to be used for creating standalone (micro)apps, only for packages that will be imported by `dashboard` project_. If you want to create a standalone microapp, use `create-react-app` instead.

While developing run _from inside the `<new-package>` folder
```
yarn storybook; # start live storybook
yarn test; # run tests in watch mode
yarn tslint; # check typescript files
```

Check `<new-package>/package.json` for more info on those commands.

#### Importing a new package inside dashboard project

1. Check `<new-package>` version inside its `package.json` (by default the value is "0.1.0");
2. Now add `@cobli/<new-package>` in the `dependencies` field of **`dashboard`'s `package.json` file**;
3. Then also add a line on `dashboard/src/global.d.ts` with `declare module '@cobli/<new-package>';`;
4. Finally, run `yarn` from the top folder `herbie-dashboard`.

## Building and Running

### Cobli Main Dashboard

You must run in the root folder of the project

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

## Deploying

```
$ yarn create-deploy <package_name> <env>
```

The `<package_name>` options are `dashboard` and `welcome`<br>
The `<env>` options are `dev`, `beta`, `prod`
