[//]: # (date 2020-01-22)
[//]: # (tags JavaScript, Jest, VSCode)

# Debugging Jest tests on Windows using VSCode

## Motivation

Here is a quick post about an issue I ran into while working with a buddy of mine on a project using [Jest](https://jestjs.io/).

We have a Visual Studio Code [launch configuration](https://code.visualstudio.com/Docs/editor/debugging#_launch-configurations) that makes Jest run the current file, allowing us to step into our tests' code ([very common stuff](https://duckduckgo.com/?q=%22vscode%22+debug+jest+test&norw=1&t=ffab&atb=v167-1&ia=images)). It was inspired from examples you can find all around the Internet and used to look like this:

```json
{
	"version": "0.2.0",
	"configurations": [
		{
			"type": "node",
			"request": "launch",
			"name": "Debug current Jest test",
			"program": "${workspaceFolder}/node_modules/jest/bin/jest",
			"args": ["${relativeFile}"] // The current file
		}
	]
}
```

Unfortunately, **it was not portable** :scream:

:heavy_check_mark: On **MacOS**, everything worked fine: hit <kbd>F5</kbd> and the current test would run, stopping at each breakpoint.

:x: On **Windows**, we could not get a single test running and kept getting the following output:

```Shell
Debugger attached.
No tests found, exiting with code 1
Run with `--passWithNoTests` to exit with code 0
In dopeProject
171 files checked.
testMatch: **/__tests__/**/*.[jt]s?(x), **/?(*.)+(spec|test).[tj]s?(x) - 42 matches
testPathIgnorePatterns: \\node_modules\\ - 171 matches
testRegex:  - 0 matches
Pattern: sources\tests\dopeTests.test.js - 0 matches
Waiting for the debugger to disconnect...
```

What's up with all this regexp stuff?

## How to do it

### The problem

By default, and as the official documentation points it out, Jest interprets its input as a `regexForTestFiles` [regular expression](https://jestjs.io/docs/en/cli.html#jest-regexfortestfiles) (emphasis mine):

> jest \<regexForTestFiles\>
>
> When you run jest with an argument, that argument is treated as a regular expression to match against files in your project. [...]
>
> **On Windows, you will need to use / as a path separator or escape \ as \\\\**.

What this really means is that **you cannot use the same input on Windows and on MacOS**. :unamused:

:warning: On Windows, VSCode resolves `${relativeFile}` as something like _sources\\...\\\*.js_, which is no good for Jest.

### The solution

I am sure they are means of transforming the Windows input somehow but we are only passing a single full file path, so I see no point in using this regexp mechanism. It turns out Jest has [another way of receiving paths](https://jestjs.io/docs/en/cli#--runtestsbypath) that does not involve any regexp shenanigans (again, emphasis mine):

> --runTestsByPath
>
> **Run only the tests that were specified with their exact paths.**
> Note: The default regex matching works fine on small runs, but becomes slow if provided with multiple patterns and/or against a lot of tests. This option replaces the regex matching logic and by that optimizes the time it takes Jest to filter specific test files

This is exactly what we needed: here is the updated configuration that works on **both plaforms**:

```json
{
	"version": "0.2.0",
	"configurations": [
		{
			"type": "node",
			"request": "launch",
			"name": "Debug current Jest test",
			"program": "${workspaceFolder}/node_modules/jest/bin/jest",
			"args": [
				"--runTestsByPath", // <--- HERE!
				"${relativeFile}"
			]
		}
	]
}
```

## Take away

-   Beware what Jest mode you are using
-   When in doubt, refer to the documentation of the tool you are using
-   Pay attention to log messages
-   Expect multi-plaform friction
