+++
title = 'Upgrading to Storybook 7'
date = 2023-01-24T16:59:50Z
draft = true
tags = ['Storybook', 'Notes']
+++

Useful guide for this can be found [here](https://www.notion.so/Storybook-7-migration-guide-dbf41fa347304eb2a5e9c69b34503937?pvs=21)

Using the provided upgrade guide seemed to work well for the most part and detected the project settings easily. There were a couple of minor issues that came up.

### Story is deprecated

Some of my older stories wouldn’t work anymore, this is due to the Story object being deprecated. However it is recommended to switch over to using MDX 2 type stories for longer form stories.

### Webpack 5 module parsing issues

There were some errors appearing where it looked like the TypeScript files were being parsed incorrectly, almost as if they were being treated as JavaScript files. There were a couple of steps needed to remedy this.

- `npx sb@next babelrc` This will add a .babelrc.json config (I added the Typescript and React presets.
- add `babelModeV7: true` to the main.js **features** options

This configures babel properly for React & Typescript.

Really good article on stories - https://storybook.js.org/blog/improved-type-safety-in-storybook-7/

One thing to note during the upgrade, the stories glob on main.js will need updating, there is an upgrade command for it which is this:

`npx storybook migrate upgrade-deprecated-types --glob="**/*.stories.@(js|jsx|ts|tsx)"`

You effectively need this in your main.js

`stories: ["../src//*.mdx", "../src//*.stories.@(js|jsx|ts|tsx)"],`