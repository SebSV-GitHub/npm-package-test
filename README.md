# NPM Package Test

This is a collection of learning from following the steps in this [guide](https://docs.github.com/en/packages/quickstart).

Instead of adding directly the file from step 7, you should add the action "Publish Node.js Package to GitHub Packages"

In the step 8 when you are creating the `.npmrc` file:

## Scope

### Registry scope

```yaml
@YOUR-USERNAME:registry=https://npm.pkg.github.com/
```

> Add the final `/` at the end of the domain

It important when you replace your username `@YOUR-USERNAME`:

- Keep the `@` since this means that this is the scope of the packages.
- Use only lowercase letter to indicate you username.

> Don't forget to create the `package-lock.json` (`npm install`) because this will increase the speed of the build when running the `npm ci` command in the job.

This will run the workflow whenever you create a release

```yaml
on:
  release:
    types: [created]
```

Change that part of the code to modify what triggers the workflow.

### `package.json` package name scop

The same rule applies to the name of the package in `package.json` `name`.

```json
"name": "@sebsv-github/npm-package-test",
```

## Tests

This workflow will run the test, so make sure the dependencies and the tests are ready to run.

## `npm publish` [bug](https://stackoverflow.com/questions/65945592/npm-publish-and-npm-install-fail-depending-on-npmrc-syntax-used)

The version of `npm` used in the workflow has a known bug where it will try to push the code directly to the official npm registry, and fail because it does not have auth credentials.

In order to solve that on of the workaround is to replace the publish command at line 34:

```yaml
run: npm publish
```

To this which adds the GitHub Packages registry directly embedded into the command.

```yaml
run: npm publish --registry=https://npm.pkg.github.com/
```

## Installing the package

### Add a personal access token

Create a new [PAT](https://github.com/settings/tokens) with the minimum capability of read packages to download and install private packages from your user.

Export that token in your environment variables, for security reasons and avoid exposing your token.

```sh
export GITHUB_PACKAGES_NPM_TOKEN = yourToken
```

### Add a `.npmrc` file configure to point to your package registry and having the PAT

Fill that file like this

```yaml
//npm.pkg.github.com/:_authToken=${GITHUB_PACKAGES_NPM_TOKEN}
@YOUR-USERNAME:registry=https://npm.pkg.github.com
```

`YOUR-USERNAME` should be the same as the one in the package your trying to install.

### Add the package to your `package.json`

Add the package to your dependencies, you can copy and paste the command available in the package main page.

After that you would be to install the dependency with `npm install`
