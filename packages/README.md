# Managing Packages

This repository uses [lerna] to manage Human Made modules and publish them as packages to [npm]. This enforces certain steps in the workflow which are described below.

## Managing Dependencies

There are two types of dependencies that you might want to add to one of the existing Human Made packages.

### Production Dependencies

Production dependencies are stored in the `dependencies` section of the package’s `package.json` file.

#### Adding New Dependencies

The simplest way to add a production dependency to one of the packages is to run a very convenient [lerna add](https://github.com/lerna/lerna/tree/master/commands/add#readme) command from the root of the project.

_Example:_

```shell
lerna add stylelint-csstree-validator packages/stylelint-config
```

This command adds the latest version of `stylelint-csstree-validator` as a dependency to the `@humanmade/stylelint-config` package, which is located in `packages/stylelint-config` folder.

#### Removing Existing Dependencies

Removing a dependency from one of the Human Made packages requires some manual work. You need to remove the line in the corresponding `dependencies` section of the `package.json` file.

_Example:_
```diff
+++ b/packages/eslint-config-humanmade/package.json
@@ -33,7 +33,6 @@
 		"chalk": "^2.4.1",
 		"eslint": "^5.10.0",
 		"eslint-config-react-app": "^3.0.5",
-		"eslint-plugin-flowtype": "^3.2.0",
 		"eslint-plugin-import": "^2.14.0",
 		"eslint-plugin-jsx-a11y": "^6.1.2",
 		"eslint-plugin-react": "^7.11.1"
```

Next, you need to run `npm install` in the root of the project to ensure that `package-lock.json` file gets properly regenerated.

#### Updating Existing Dependencies

This is the most confusing part of working with [lerna] which causes a lot of hassles for contributors. The most successful strategy so far is to do the following:
 1. First, remove the existing dependency as described in the previous section.
 2. Next, add the same dependency back as described in the first section of this chapter. This time it wil get the latest version applied unless you enforce a different version explicitly.

### Development Dependencies

In contrast to production dependencies, development dependencies shouldn't be stored in individual Human Made packages. Instead they should be installed in the project's `package.json` file using the usual `npm install` command. In effect, all development tools are configured to work with every package at the same time to ensure they share the same characteristics and integrate correctly with each other.

_Example:_

```shell
npm install glob --save-dev
```

This commands adds the latest version of `glob` as a development dependency to the `package.json` file. It has to be executed from the root of the project.

## Maintaining Changelogs

In maintaining npm packages it can be tough to keep track of changes. To simplify the release process the project root contains a `CHANGELOG.md` file which details all published releases and the unreleased ("Master") changes, if any exist.

For each pull request, you should always include relevant changes in a "Master" heading at the top of the file. You should add the heading if it doesn't already exist.

_Example:_

```md
## Unreleased (1.2.3)

### Added:
 - Added stylelint `stylelint-csstree-validator` plugin to `@humanmade/stylelint-config`

### Removed
 - Removed ESLint `eslint-plugin-flowtype` config from `eslint-plugin-humanmade`
```

There are a number of common release subsections you can follow. Each is intended to align to a specific meaning in the context of the [Semantic Versioning (`semver`) specification](https://semver.org/) the project adheres to. It is important that you describe your changes accurately, since this is used in the packages release process to help determine the version of the next release.

- "Breaking Change" - A backwards-incompatible change which requires specific attention of the impacted developers to reconcile (requires a major version bump).
- "New Feature" - The addition of a new backwards-compatible function or feature to the existing public API (requires a minor verison bump).
- "Enhancement" - Backwards-compatible improvements to existing functionality (requires a minor version bump).
- "Bug Fix" - Resolutions to existing buggy behavior (requires a patch version bump).
- "Internal" - Changes which do not have an impact on the public interface or behavior of the module (requires a patch version bump).

While other section naming can be used when appropriate, it's important that the subsections are expressed clearly to avoid confusion for both the packages releaser and third-party consumers.

When in doubt, refer to [Semantic Versioning specification](https://semver.org/).

## Releasing Packages

Lerna automatically releases all outdated packages. To check which packages are outdated and will be released, type `npm run publish:check`.

If you have the ability to publish packages, you _must_ have [2FA enabled](https://docs.npmjs.com/getting-started/using-two-factor-authentication) on your [npm account][npm].

### Before Releasing

Confirm that you're logged in to [npm], by running `npm whoami`. If you're not logged in, run `npm adduser` to login.

If you're publishing a new package, ensure that its `package.json` file contains the correct `publishConfig` settings:

```json
{
	"publishConfig": {
		"access": "public"
	}
}
```

You can check your package configs by running `npm run lint-pkg-json`.

### Development Release

Run the following command to release a dev version of the outdated packages.

```shell
npm run publish:dev
```

Lerna will ask you which version number you want to choose for each package. For a `dev` release, you'll more likely want to choose the "prerelease" option. Repeat the same for all the outdated packages and confirm your version updates.

Lerna will then publish to [npm], commit the `package.json` changes and create the git tags.

### Production Release

To release a production version for the outdated packages, run the following command:

```shell
npm run publish:prod
```

Choose the correct version based on `CHANGELOG.md` files, confirm your choices and let Lerna do its magic.

## Credits

Thanks to the WordPress and Gutenberg contributors to which this [document is based on](https://github.com/WordPress/gutenberg/blob/master/packages/README.md).

[lerna]: https://lerna.js.org/
[npm]: https://www.npmjs.com/
