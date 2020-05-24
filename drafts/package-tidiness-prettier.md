[//]: # (date 2020-05-01)
[//]: # (tags JavaScript, NodeJS, Prettier, VSCode)

# Cleaning your package's root: Prettier

## Motivation

As a relatively newcomer to the whole JavaScript ecosystem, there is one thing that particularly bugs me: **the number of config files that just lay around, at the root of most projects**.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Little known fact -- config files in the root of a JS project actually create their own special kind of gravity, which only attracts other config files.</p>&mdash; Jackson Gabbard (@jgbbrd) <a href="https://twitter.com/jgbbrd/status/1261242168133464065?ref_src=twsrc%5Etfw">May 15, 2020</a></blockquote>

[React's repository](https://github.com/facebook/react) is a good example: ESLint, Prettier, Babel,... Also note that some configurations rely on an "ignore" file as well. In the end, this all adds up to a ????

As a result, I have decided to go on a quest to unclutter package roots of the world. Let's get started with Facebook's [Prettier](https://prettier.io).

## How to do it

### The basic case

Let's imagine your workspace is **NOT a monorepo** and that your single package has a unique Prettier config file at its root. This is the simplest setting, and the easiest to "fix".

### VSCode integration

If you are a fan of VSCode and Prettier, then there is also a fairly good chance that you are using [this extension](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode).

Nothing complicated here as it provides a setting letting you tell the extension where to find your configuration file:

```json
"prettier.configPath": "tools/prettier/.prettierrc"
```

### npm script

If you need Prettier to run as an npm or yarn script, you will now have to specify the path to your configuration file on the command line. Again, fancy here: just use by the [`--config` option](https://prettier.io/docs/en/cli.html#--find-config-path-and---config) in your script:

```json
"scripts": {
	"fmt": "prettier . --write --config tools/prettier/.prettierrc"
}
```

### The monorepo

- A single shared config
  - External
  - Internal
- Multiple config


## Take away
- Tidy package roots are possible!
- Stay tuned for more
