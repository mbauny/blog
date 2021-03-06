[//]: # 'date 2020-10-31'
[//]: # 'tags Yarn, npm, GitHub'

# Using Yarn install in a CI environment

## Motivation

I have recently decided to switch one of my projects to Yarn and the transition from npm went smoothly for the repository itself.

The small twist is that the project is hosted on GitHub where I have set up a workflow to run a test suit against every PR: I wanted to migrate this workflow to Yarn as well.

## How to do it

npm has a [`ci`](https://docs.npmjs.com/cli/v6/commands/npm-ci#description) option dedicated to continuous integration:

> This command is similar to npm install, except it's meant to be used in automated environments such as test platforms, continuous integration, and deployment -- or any situation where you want to make sure you're doing a clean install of your dependencies.

It (not so surprisingly) turns out that Yarn has an similar feature: the more accurately named [`--frozen-lockfile`](https://classic.yarnpkg.com/en/docs/cli/install#toc-yarn-install-frozen-lockfile) option:

> Don’t generate a yarn.lock lockfile and fail if an update is needed.

My workflow was using the former and I only had to switch to the later. Here is what the updated yml file looks like:

```diff
uses: actions/setup-node@v1
with:
  node-version: ${{ matrix.node-version }}
- name: npm run test
+ name: yarn run test
run:
-	npm ci
-	npm test
+	yarn install --frozen-lockfile
+	yarn test
env:
	CI: true
```

## Take away

- `yarn --frozen-lockfile` might be handy in a CI environment
- Yarn is installed on all [GitHub-hosted runners](https://docs.github.com/en/free-pro-team@latest/actions/guides/building-and-testing-nodejs#installing-dependencies)
- GitHub's [documentation about Actions](https://docs.github.com/en/free-pro-team@latest/actions/guides/building-and-testing-nodejs) is pretty complete
