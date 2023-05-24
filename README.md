# TypeScript Monorepo Starter

This is intended as a very simple "Hello, world" type application that can be used as a starting point for applications that:
- Are written in TypeScript
- Run on Node.js
- Is a monorepo containing multiple NodeJs packages
- Contain shared packages that other packages in the monorepo depend on

# Local setup

To test this on your local machine:
- Install nvm then use it to install NodeJs and npm. I used Node 16, but other versions should also work.
- Clone the repository to a directory of your choice.
- Open a terminal window at the repository root folder. I used Ubuntu 20.04 via WSL on Windows 10, but others should work.
- Run the `npm install` command to populate the `node_modules` directory.
- Change to the `packages\package-a` folder and run the `npm run compile` command to compile package A.
- Test package A by executing `node ./dist/index.js`. It should print "Hello package a".
- Change to the `packages\package-b` folder and run the `npm run compile` command to compile package B.
- Test package B by executing `node ./dist/index.js`. It should print "Hello package a" and "Hello package b".


# Repository contents

This monorepo contains 2 NodeJs packages to demonstrate the setup.

Package A contains a `Greeter` class that prints a greeting. When this module is loaded, it uses the `Greeter` 
class to print "Hello package a".

Package B depends on Package A and imports the `Greeter` class from it with full typings support. When this module is 
loaded, it uses the `Greeter` class to print "Hello package b".

# Notes

Important things for you to be aware of.

## package.json files

The `package.json` file in the root folder sets up a NodeJs [workspece](https://docs.npmjs.com/cli/v9/using-npm/workspaces), 
which is essential for a monorepo. The workspace is defined as all sub-directories of the `packages` directory, so 
you can add more packages later provided you put each package its own directory inside the `packages` directory.

Any dependencies that are shared by all packages in the monorepo should be added to `package.json` in the root folder.

I set the package names to have a scope of `@app`. It is recommended that you change this to the actual name of your
application.

## tsconfig.json files

Setting up the TypeScript compiler to work correctly in this monorepo setup was very tricky, and took many iterations of
experimenting. The main reason for publishing this starter project was so that you don't have to do this experimenting.

I made all of the `tsconfig.json` files inherit from `tsconfig.base.json` to make maintenence easier. Any new packages
that you add should also inherit from this file. The only things that you need to customize in each package are:
- The source, root and output directories need to be specified in each package because they are relative to the `tsconfig.json` file.
- The references list needs to list the intr-package dependencies.

## Extending shared packages

Packages that are imported into other packages can only export a single source file (`index.js` by convention) and a 
single type definition file (`index.d.ts` by convention). The TypeScript compiler is configured to generate these
files in the `dist` folder by compiling `./src/index.ts`.

This means that all classes that you want to reference in other packages need to be exported from `./src/index.ts`.
The way I recommend that you do this is to place an `index.ts` file in every directory that simply re-exports all of
the classes defined in that directory, then in the parent directory, re-export the `index.ts` files from all sub-directories.

