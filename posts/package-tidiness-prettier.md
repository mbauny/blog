---
title: "Cleaning your package's root: Prettier"
date: 2020-05-01
tags: [JavaScript, NodeJS, Prettier, VSCode]
---

# Cleaning your package's root: Prettier

## Motivation

As a relatively newcomer to the whole JavaScript ecosystem, there is one thing that particularly bugs me and that is **the number of config files that just lay around, at the root of most packages**.

[React's repository](https://github.com/facebook/react) is a good example: ESLint, Prettier, Babel,... Also note that some configurations rely on an "ignore" file as well. In the end, this all adds up to a ????

## How to do it

### The configuration file

### npm script

If you need Prettier to run as an npm or yarn script, you will now have to specify the path to your configuration file on the command line. This is easily done by using the [`--config` option](https://prettier.io/docs/en/cli.html#--find-config-path-and---config):

```json
"scripts": {
	"fmt": "prettier . --write --config tools/prettier/.prettierrc"
}
```

### Editor integration

If you are a fan of VSCode and Prettier, there is also a fairly good chance that you are using [this extension](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode).

Luckily it provides a setting letting you tell the extension where to find your configuration file:

```json
`"prettier.configPath": "tools/prettier/.prettierrc"`
```

## Take away
- Tidy package roots are possible!
-
