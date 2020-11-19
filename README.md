# LearnStorybook CSS issues

This repo details a couple of issues we're currently facing with Storybook and CRA

## CSS issue

Based on the current behavior described in the tutorial once the reader adds the [tutorial's css](https://github.com/chromaui/learnstorybook-code/blob/master/src/index.css) either in `src/index.css` or as in detailed in this scenario with `Taskbox.css` and 
imports it into `.storybook/preview.js` he/she will be faced with the following error when starting Storybook.


```shell
ERROR in ./src/Taskbox.css (./node_modules/css-loader/dist/cjs.js??ref--9-oneOf-4-1!./node_modules/postcss-loader/src??postcss!./src/Taskbox.css)
Module build failed (from ./node_modules/css-loader/dist/cjs.js):
Error: Can't resolve './icon/percolate.eot?-5w3um4' in '/Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/src'
    at /Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/enhanced-resolve/lib/Resolver.js:209:21
    at /Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/enhanced-resolve/lib/Resolver.js:285:5
    at eval (eval at create (/Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/tapable/lib/HookCodeFactory.js:33:10), <anonymous>:15:1)
    at /Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/enhanced-resolve/lib/UnsafeCachePlugin.js:44:7
    at /Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/enhanced-resolve/lib/Resolver.js:285:5
    at eval (eval at create (/Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/tapable/lib/HookCodeFactory.js:33:10), <anonymous>:15:1)
    at /Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/enhanced-resolve/lib/Resolver.js:285:5
    at eval (eval at create (/Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/tapable/lib/HookCodeFactory.js:33:10), <anonymous>:27:1)
    at /Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/enhanced-resolve/lib/DescriptionFilePlugin.js:67:43
    at /Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/enhanced-resolve/lib/Resolver.js:285:5
    at eval (eval at create (/Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/tapable/lib/HookCodeFactory.js:33:10), <anonymous>:16:1)
    at /Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/enhanced-resolve/lib/Resolver.js:285:5
    at eval (eval at create (/Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/tapable/lib/HookCodeFactory.js:33:10), <anonymous>:27:1)
    at /Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/enhanced-resolve/lib/DescriptionFilePlugin.js:67:43
    at /Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/enhanced-resolve/lib/Resolver.js:285:5
    at eval (eval at create (/Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/tapable/lib/HookCodeFactory.js:33:10), <anonymous>:16:1)
 @ ./src/Taskbox.css 2:26-162 53:4-74:5 56:18-154
 @ ./.storybook/preview.js
 @ ./.storybook/preview.js-generated-config-entry.js
 @ multi ./node_modules/@pmmmwh/react-refresh-webpack-plugin/client/ReactRefreshEntry.js ./node_modules/@storybook/core/dist/server/common/polyfills.js ./node_modules/@storybook/core/dist/server/preview/globals.js ./.storybook/storybook-init-framework-entry.js ./node_modules/@storybook/addon-docs/dist/frameworks/common/config.js-generated-other-entry.js ./node_modules/@storybook/addon-docs/dist/frameworks/react/config.js-generated-other-entry.js ./node_modules/@storybook/addon-links/dist/preset/addDecorator.js-generated-other-entry.js ./node_modules/@storybook/addon-actions/dist/preset/addDecorator.js-generated-other-entry.js ./node_modules/@storybook/addon-actions/dist/preset/addArgs.js-generated-other-entry.js ./node_modules/@storybook/addon-backgrounds/dist/preset/defaultParameters.js-generated-other-entry.js ./.storybook/preview.js-generated-config-entry.js ./.storybook/generated-stories-entry.js (webpack)-hot-middleware/client.js?reload=true&quiet=false&noInfo=undefined ./node_modules/react-scripts/node_modules/react-dev-utils/webpackHotDevClient.js

 ```

Looking at the error it seems that the during the bootup process Storybook/Webpack is parsing the css added and it will break because it's currently looking into the `src` folder, but the fonts and icons are placed in `public`.

### Tests done

The following tests were done to try and solve this:

1. Create a global.css with the styles added in public
2. Create index.css with the styles added in public
3. Create a global.css within public
4. Adjust css @font-face to point at the public folder


All of this failed with different types of errors. Ranging from the error already described to:

```shell
You attempted to import ../public/icon/percolate.ttf which falls outside of the project src/ directory. Relative imports outside of src/ are not supported.
```


One thing that was bothering me was the following. In the past probably there was some sort of rule in place that made this easier with Storybook and CRA to load assets directly in the public folder from `src/index.css`. But as time goes by probably some changes were introduced and broke this rule.



# Solution
The following steps solve the issue:
- Put the compiled css into `src/index.css`.
- Put fonts / icons in `src/assets` folder.
- Adjust css font styling as follows:

```css
@font-face {
  font-family: "percolate";
  src: url("./assets/icon/percolate.eot?-5w3um4");
  src: url("./assets/icon/percolate.eot?#iefix5w3um4") format("embedded-opentype"),
    url("./assets/icon/percolate.woff?5w3um4") format("woff"),
    url("./assets/icon/percolate.ttf?5w3um4") format("truetype"),
    url("./assets/icon/percolate.svg?5w3um4") format("svg");
  font-weight: normal;
  font-style: normal;
}
```

### Extra documentation

For reference I referred to the create react app docs regarding:
- [Using the public folder](https://create-react-app.dev/docs/using-the-public-folder)
- [Adding images, fonts and files](https://create-react-app.dev/docs/adding-images-fonts-and-files/)

It's a bit slim on the documentation side. And doesn't address this case.

## Issue with babel-loader

One other issue that is currently plaguing the tutorial is the inclusion of `babel-loader` during the Storybook project bootstrap process. Currently Storybook adds version `babel-loader` version 8.2.1 as a devDependency.

If the reader is following the tutorial and issues `yarn start`/ `yarn test --watchAll` he/she will run into 

```shell
$ react-scripts start

There might be a problem with the project dependency tree.
It is likely not a bug in Create React App, but something you need to fix locally.

The react-scripts package provided by Create React App requires a dependency:

  "babel-loader": "8.1.0"

Don't try to install it manually: your package manager does it automatically.
However, a different version of babel-loader was detected higher up in the tree:

  /Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/babel-loader (version: 8.2.1) 

Manually installing incompatible versions is known to cause hard-to-debug issues.

If you would prefer to ignore this check, add SKIP_PREFLIGHT_CHECK=true to an .env file in your project.
That will permanently disable this message but you might encounter other issues.

To fix the dependency tree, try following the steps below in the exact order:

  1. Delete package-lock.json (not package.json!) and/or yarn.lock in your project folder.
  2. Delete node_modules in your project folder.
  3. Remove "babel-loader" from dependencies and/or devDependencies in the package.json file in your project folder.
  4. Run npm install or yarn, depending on the package manager you use.

In most cases, this should be enough to fix the problem.
If this has not helped, there are a few other things you can try:

  5. If you used npm, install yarn (http://yarnpkg.com/) and repeat the above steps with it instead.
     This may help because npm has known issues with package hoisting which may get resolved in future versions.

  6. Check if /Users/myuser/myfolder/storybook_fork/storybook-related/throwaways/throwaway-storybook-css/node_modules/babel-loader is outside your project directory.
     For example, you might have accidentally installed something in your home folder.

  7. Try running npm ls babel-loader in your project folder.
     This will tell you which other package (apart from the expected react-scripts) installed babel-loader.

If nothing else helps, add SKIP_PREFLIGHT_CHECK=true to an .env file in your project.
That would permanently disable this preflight check in case you want to proceed anyway.

P.S. We know this message is long but please read the steps above :-) We hope you find them helpful!

error Command failed with exit code 1.
info Visit https://yarnpkg.com/en/docs/cli/run for documentation about this command.
```

This can be easily solved with creating a `.env` file and adding the `SKIP_PREFLIGHT_CHECK=true` variable and value.
