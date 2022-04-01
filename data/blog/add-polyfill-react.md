---
title: add Polyfill to previous portfolio
date: '2022-03-31'
tags: ['movie-web', 'portfolio', 'refactor']
draft: false
summary: 'How can I make My portfolio website work on IE Browser?'
---

Most sentences in this post are pasted from official development documents. I just add some comments and organize how I work.

&nbsp;

CRA official document says [like this](https://create-react-app.dev/docs/supported-browsers-features/) below.

> By default, the generated project supports all modern browsers. but Support for Internet Explorer 9, 10, and 11 requires polyfills. For a set of polyfills to support older browsers, use `react-app-polyfill`.

&nbsp;

My previous portfolio website didn't work on Internet Explorer. so for my first refactoring step, I want to solve this problem.

If you just follow the step on the official [react-app-polyfill github repo](https://github.com/facebook/create-react-app/tree/main/packages/react-app-polyfill), you can find your website made by CRA work well on IE.

> `react-app-polyfill` package includes polyfills for various browsers. It includes minimum requirements and commonly used language features used by CRA projects.

&nbsp;

1. just install the package

```
npm i react-app-polyfill
```

2. include proper modules to `src/index.js` to inform this project is gonna use react polyfill.

```javascript
// These must be the first lines in src/index.js
import 'react-app-polyfill/ie9'
import 'react-app-polyfill/stable'
```

`stable` module is ...

> You can also polyfill <u>stable language features</u> not available in your target browsers. If you're using this in CRA, it will automatically use the `browserslist` you've defined to only include polyfills <u>needed by your target browsers</u> when importing the stable polyfill.

&nbsp;

## What error occurs during refactor?

### Browser list update

```bash
$ npm run build

> 2021-portfolio-bookshelf@0.1.0 build
> react-scripts build

Creating an optimized production build...
Browserslist: caniuse-lite is outdated. Please run:
  npx browserslist@latest --update-db
  Why you should do it regularly: https://github.com/browserslist/browserslist#browsers-data-updating
```

`npx browserslist@latest --update-db` updates `caniuse-lite` version in your npm, yarn or pnpm lock file. This update will bring data about new browsers to polyfills tools

You need to do it regularly for 3 reasons

1. To use the latest browser's versions and statistics in queries like `last 2 versions` or `>1%`. For example, if you created your project 2 years ago, and did not update your dependencies, `last 1 version` will return 2 year old browsers.
2. Actual browsers data will lead to using less polyfills. It will reduce size of JS and CSS files and improve website performance.
3. `caniuse-lite` deduplication: to synchronize version in different tools.

All of the above descriptions are from [Browserlist github repo](https://github.com/browserslist/browserslist#browsers-data-updating): from warning message last line.

&nbsp;

so after running this `npx browserslist@latest --update-db`, I can find `caniuse-lite` version updated (written in package.json) and then after few seconds, it automatically vanished in package.json.

In terminal, there are some logs about Target browser changes:

```bash
Target browser changes:
- edge 92
+ edge 98
+ edge 97
- firefox 91
- firefox 90
+ firefox 97
+ firefox 96
- ios_saf 14.5-14.7
+ ios_saf 15.2-15.3
....
```

&nbsp;

### change Browser list query

After change **browserslist** in package.json like below, I got this error

```bash
  "browserslist": {
    ...
    "development": [
      "ie9",
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  },
```

```bash
Failed to compile.

./src/components/common/common.scss (./node_modules/css-loader/dist/cjs.js??ref--5-oneOf-6-1!
./node_modules/postcss-loader/src??postcss!
./node_modules/resolve-url-loader??ref--5-oneOf-6-3!
./node_modules/sass-loader/dist/cjs.js??ref--5-oneOf-6-4!
./src/components/common/common.scss)
BrowserslistError: Unknown browser query `ie9`. Maybe you are using old Browserslist or made typo in query.
    at Array.reduce (<anonymous>)
```

so I change this query from `ie9` to `not ie <= 8` following [this browserslist repo](https://github.com/browserslist/browserslist#full-list).
and then I got this error.

```bash
Failed to compile.

./src/components/common/common.scss (./node_modules/css-loader/dist/cjs.js??ref--5-oneOf-6-1!
./node_modules/postcss-loader/src??postcss!
./node_modules/resolve-url-loader??ref--5-oneOf-6-3!
./node_modules/sass-loader/dist/cjs.js??ref--5-oneOf-6-4!
./src/components/common/common.scss)
BrowserslistError: Write any browsers query (for instance, `defaults`) before `not ie <=8`
    at Array.reduce (<anonymous>)
```

so I add `defaults` on top of browerslist development array.

```bash
    ....
    "development": [
      "defaults",
      "not ie <=8",
      "last 1 chrome version",
      ....
    ]
  },
```

&nbsp;

### delete cache

and then react-app-polyfill didn't work. so I was goggling again. and remove `node_modules/.cache`. then all works. (I didn't downgrade react-scripts to 3.2.0 version)

- from https://stackoverflow.com/questions/59834690/react-app-polyfill-doesnt-work-in-ie11
- from eyerean in https://github.com/facebook/create-react-app/issues/8197

&nbsp;

### Firebase deploy again

To refactor previous movie-web-app project, at first, I made a new brach `refactor`. and After finishing refactoring first step, I have to re-deploy firebase hosting with this new branch.

firebase hosting process is simple.

1. test your project hosting in your local server

```bash
$ firebase serve --only hosting

Error: Failed to get Firebase project jaranda-pentagon-v2.
Please make sure the project exists and your account has permission to access it.
```

and then I got this error.

I guessed It was about new branch(?), and searching the error message. It's just about firebase login/auth again. so I ran `firebase login --reauth` command.

```bash
$ firebase login --reauth

Visit this URL on this device to log in:
xxxxx2123fadfxxxxx

Waiting for authentication...

+  Success! Logged in as sfsdsxxxx@xxxxx.com

   ╭─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╮
   │                                                                                                                     │
   │                                          Update available 9.18.0 → 10.5.0                                           │
   │                    To update to the latest version using npm, run npm install -g firebase-tools                     │
   │   For other CLI management options, visit the CLI documentation (https://firebase.google.com/docs/cli#update-cli)   │
   │                                                                                                                     │
   ╰─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
```

This command pop up the new window for login process. After just clicking accept button on new window, all tasks will be done.

2. finally run command `firebase deploy`. then all should work on firebase hosting server.

```bash
$ firebase deploy
```
