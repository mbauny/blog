[//]: # 'date 2020-05-01'
[//]: # 'tags JavaScript, NodeJS, Prettier, VSCode'

# Cleaning your package's root: Prettier (The basic case)

## Motivation

As a relatively newcomer to the whole JavaScript ecosystem, there is one thing that particularly bugs me: **the number of config files that just lay around, at the root of most projects**.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Little known fact -- config files in the root of a JS project actually create their own special kind of gravity, which only attracts other config files.</p>&mdash; Jackson Gabbard (@jgbbrd) <a href="https://twitter.com/jgbbrd/status/1261242168133464065?ref_src=twsrc%5Etfw">May 15, 2020</a></blockquote>

[React's repository](https://github.com/facebook/react) is a good example: ESLint, Prettier, Babel,... Also note that some tools rely on an *ignore* file as well. In the end, this all adds up to build a pretty cumbersome wall of files that you cannot avoid seeing, even if there are probably the ones you will open the least often.

As a result, I have decided to try and find ways to avoid these kind of situations. Let's get started with Facebook's [Prettier](https://prettier.io).

## How to do it

### The basic case

Let's imagine your workspace is composed of a single package with a unique Prettier config file at its root. This is the simplest setting, and the easiest to "fix".

**We'll deal with monorepos in a follow-up article.**

The first stage is to move your config file to a sub-folder. I generally use `tools/prettier/prettier.rc` but that is entirely up to you.

Now, on to the next steps...

#### npm script

If you need Prettier to run as an _npm_ or _yarn_ script, you will have to specify the path to your configuration file on the command line. Just use the [`--config` option](https://prettier.io/docs/en/cli.html#--find-config-path-and---config):

```json
"scripts": {
	"fmt": "prettier . --write --config path/to/prettier/config"
}
```

#### VSCode integration

If you are a fan of VSCode and Prettier, then there is a fairly good chance you are using [this extension](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode).

Again, nothing fancy here as it provides a setting letting you tell the extension where to find your configuration file:

```json
"prettier.configPath": "path/to/prettier/config"
```

#### package.json

Both previous tips relied on a configuration file but Prettier is also configurable directly from the package.json file:

```json
prettier: {
	"tabWidth": 4,
	"semi": false,
	"singleQuote": true
}
```

I am usually not a fan is this solution but if you don't mind your package.json getting a bit chunkier or if your config is really simple, you might as well go with it.

## Take away

-   Tidy package roots are possible!
-   Stay tuned for the monorepo case
-   Next: ESLint
