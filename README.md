# Release It! 🚀

🚀 Generic CLI tool to automate versioning and package publishing-related tasks:

<img align="right" src="./docs/assets/release-it.svg?raw=true" height="280">

- Bump version (in e.g. `package.json`)
- [Git commit, tag, push][1]
- Execute any (test or build) commands using [hooks][2]
- [Create release at GitHub][3] or [GitLab][4]
- [Generate changelog][5]
- [Publish to npm][6]
- [Manage pre-releases][7]
- Extend with [plugins][8]
- Release from any [CI/CD environment][9]

Use release-it for version management and publish to anywhere with its versatile configuration, a powerful plugin
system, and hooks to execute any command you need to test, build, and/or publish your project.

[![Action Status][11]][10] [![npm version][13]][12]

## Installation

Although release-it is a **generic** release tool, most projects use it for projects with npm packages. The recommended
way to install release-it uses npm and adds some minimal configuration to get started:

```bash
npm init release-it
```

Alternatively, install it manually, and add the `release` script to `package.json`:

```bash
npm install -D release-it
```

```json
{
  "name": "my-package",
  "version": "1.0.0",
  "scripts": {
    "release": "release-it"
  },
  "devDependencies": {
    "release-it": "^15.10.0"
  }
}
```

## Usage

Run release-it from the root of the project using either `npm run` or `npx`:

```bash
npm run release
npx release-it
```

You will be prompted to select the new version, and more prompts will follow based on your configuration.

## Yarn

Using Yarn? Please see the [npm section on Yarn][14].

## Global Installation

Per-project installation as shown above is recommended, but global installs are supported as well:

- From npm: `npm install -g release-it`
- From Homebrew: `brew install release-it`

## GitHub Actions

Want to use release-it with GitHub Actions to fully automate the whole process? Enjoy this great walk-through: [How to
use GitHub Actions & Release-It to Easily Release Your Code][15] (by [David from Kodaps][16]).

→ See [Continuous Integration environments][9] for more details.

## Configuration

Out of the box, release-it has sane defaults, and [plenty of options][17] to configure it. Most projects use a
`.release-it.json` file in the project root, or a `release-it` property in `package.json`.

Here's a quick example `.release-it.json`:

```json
{
  "git": {
    "commitMessage": "chore: release v${version}"
  },
  "github": {
    "release": true
  }
}
```

→ See [Configuration][18] for more details.

## Interactive vs. CI mode

By default, release-it is **interactive** and allows you to confirm each task before execution:

<img src="./docs/assets/release-it-interactive.gif?raw=true" height="290">

By using the `--ci` option, the process is fully automated without prompts. The configured tasks will be executed as
demonstrated in the first animation above. In a Continuous Integration (CI) environment, this non-interactive mode is
activated automatically.

Use `--only-version` to use a prompt only to determine the version, and automate the rest.

## Latest version

How does release-it determine the latest version?

1.  For projects with a `package.json`, its `version` will be used (see [npm][19] to skip this).
2.  Otherwise, release-it uses the latest Git tag to determine which version should be released.
3.  As a last resort, `0.0.0` will be used as the latest version.

Alternatively, a plugin can be used to override this (e.g. to manage a `VERSION` or `composer.json` file):

- [@release-it/bumper][20] to read from or bump the version in any file
- [@release-it/conventional-changelog][21] to get a recommended bump based on commit messages
- [release-it-calver-plugin][22] to use CalVer (Calendar Versioning)

Add the `--release-version` flag to print the **next** version without releasing anything.

## Git

Git projects are supported well by release-it, automating the tasks to stage, commit, tag and push releases to any Git
remote.

→ See [Git][23] for more details.

## GitHub Releases

GitHub projects can have releases attached to Git tags, containing release notes and assets. There are two ways to add
[GitHub releases][24] in your release-it flow:

1.  Automated (requires a `GITHUB_TOKEN`)
2.  Manual (using the GitHub web interface with pre-populated fields)

→ See [GitHub Releases][25] for more details.

## GitLab Releases

GitLab projects can have releases attached to Git tags, containing release notes and assets. To automate [GitLab
releases][26]:

- Configure `gitlab.release: true`
- Obtain a [personal access token][27] (release-it only needs the "api" scope).
- Make sure the token is [available as an environment variable][28].

→ See [GitLab Releases][29] for more details.

## Changelog

By default, release-it generates a changelog, to show and help select a version for the new release. Additionally, this
changelog serves as the release notes for the GitHub or GitLab release.

The [default command][17] is based on `git log ...`. This setting (`git.changelog`) can be overridden. To further
customize the release notes for the GitHub or GitLab release, there's `github.releaseNotes` or `gitlab.releaseNotes`.
Make sure any of these commands output the changelog to `stdout`. Note that release-it by default is agnostic to commit
message conventions. Plugins are available for:

- GitHub and GitLab Releases
- auto-changelog
- Conventional Changelog
- Keep A Changelog

To print the changelog without releasing anything, add the `--changelog` flag.

→ See [Changelog][30] for more details.

## Publish to npm

With a `package.json` in the current directory, release-it will let `npm` bump the version in `package.json` (and
`package-lock.json` if present), and publish to the npm registry.

→ See [Publish to npm][19] for more details.

## Manage pre-releases

With release-it, it's easy to create pre-releases: a version of your software that you want to make available, while
it's not in the stable semver range yet. Often "alpha", "beta", and "rc" (release candidate) are used as identifiers for
pre-releases. An example pre-release version is `2.0.0-beta.0`.

→ See [Manage pre-releases][31] for more details.

## Update or re-run existing releases

Use `--no-increment` to not increment the last version, but update the last existing tag/version.

This may be helpful in cases where the version was already incremented. Here are a few example scenarios:

- To update or publish a (draft) GitHub Release for an existing Git tag.
- Publishing to npm succeeded, but pushing the Git tag to the remote failed. Then use
  `release-it --no-increment --no-npm` to skip the `npm publish` and try pushing the same Git tag again.

## Hooks

Use script hooks to run shell commands at any moment during the release process (such as `before:init` or
`after:release`).

The format is `[prefix]:[hook]` or `[prefix]:[plugin]:[hook]`:

| part   | value                                       |
| ------ | ------------------------------------------- |
| prefix | `before` or `after`                         |
| plugin | `version`, `git`, `npm`, `github`, `gitlab` |
| hook   | `init`, `bump`, `release`                   |

Use the optional `:plugin` part in the middle to hook into a life cycle method exactly before or after any plugin.

The core plugins include `version`, `git`, `npm`, `github`, `gitlab`.

Note that hooks like `after:git:release` will not run when either the `git push` failed, or when it is configured not to
be executed (e.g. `git.push: false`). See [execution order][32] for more details on execution order of plugin lifecycle
methods.

All commands can use configuration variables (like template strings). An array of commands can also be provided, they
will run one after another. Some example release-it configuration:

```json
{
  "hooks": {
    "before:init": ["npm run lint", "npm test"],
    "after:my-plugin:bump": "./bin/my-script.sh",
    "after:bump": "npm run build",
    "after:git:release": "echo After git push, before github release",
    "after:release": "echo Successfully released ${name} v${version} to ${repo.repository}."
  }
}
```

The variables can be found in the [default configuration][17]. Additionally, the following variables are exposed:

```text
version
latestVersion
changelog
name
repo.remote, repo.protocol, repo.host, repo.owner, repo.repository, repo.project
branchName
```

All variables are available in all hooks. The only exception is that the additional variables listed above are not yet
available in the `init` hook.

Use `--verbose` to log the output of the commands.

For the sake of verbosity, the full list of hooks is actually: `init`, `beforeBump`, `bump`, `beforeRelease`, `release`
or `afterRelease`. However, hooks like `before:beforeRelease` look weird and are usually not useful in practice.

Note that arguments need to be quoted properly when used from the command line:

```bash
release-it --'hooks.after:release="echo Successfully released ${name} v${version} to ${repo.repository}."'
```

Using Inquirer.js inside custom hook scripts might cause issues (since release-it also uses this itself).

## Dry Runs

Use `--dry-run` to show the interactivity and the commands it _would_ execute.

→ See [Dry Runs][33] for more details.

## Troubleshooting & debugging

- With `release-it --verbose` (or `-V`), release-it prints the output of every user-defined [hook][2].
- With `release-it -VV`, release-it also prints the output of every internal command.
- Use `NODE_DEBUG=release-it:* release-it [...]` to print configuration and more error details.

Use `verbose: 2` in a configuration file to have the equivalent of `-VV` on the command line.

## Plugins

Since v11, release-it can be extended in many, many ways. Here are some plugins:

| Plugin                                    | Description                                                                   |
| ----------------------------------------- | ----------------------------------------------------------------------------- |
| [@release-it/bumper][20]                  | Read & write the version from/to any file                                     |
| [@release-it/conventional-changelog][21]  | Provides recommended bump, conventional-changelog, and updates `CHANGELOG.md` |
| [@release-it/keep-a-changelog][34]        | Maintain CHANGELOG.md using the Keep a Changelog standards                    |
| [@release-it-plugins/lerna-changelog][35] | Integrates lerna-changelog into the release-it pipeline                       |
| [@release-it-plugins/workspaces][36]      | Releases each of your projects configured workspaces                          |
| [release-it-calver-plugin][22]            | Enables Calendar Versioning (calver) with release-it                          |
| [@grupoboticario/news-fragments][37]      | An easy way to generate your changelog file                                   |
| [@j-ulrich/release-it-regex-bumper][38]   | Regular expression based version read/write plugin for release-it             |

Internally, release-it uses its own plugin architecture (for Git, GitHub, GitLab, npm).

→ See all [release-it plugins on npm][39].

→ See [plugins][40] for documentation to write plugins.

## Use release-it programmatically

While mostly used as a CLI tool, release-it can be used as a dependency to integrate in your own scripts. See [use
release-it programmatically][41] for example code.

## Example projects using release-it

- [axios/axios][42]
- [blockchain/blockchain-wallet-v4-frontend][43]
- [callstack/react-native-paper][44]
- [ember-cli/ember-cli][45]
- [js-cookie/js-cookie][46]
- [metalsmith/metalsmith][47]
- [mozilla/readability][48]
- [pahen/madge][49]
- [redis/node-redis][50]
- [reduxjs/redux][51]
- [saleor/saleor][52]
- [Semantic-Org/Semantic-UI-React][53]
- [shipshapecode/shepherd][54]
- [StevenBlack/hosts][55]
- [swagger-api/swagger-ui][56] + [swagger-editor][57]
- [tabler/tabler][58] + [tabler-icons][59]
- [youzan/vant][60]
- [Repositories that depend on release-it][61]
- GitHub search for [path:\*\*/.release-it.json][62]

## Legacy Node.js

The latest major version is v15, supporting Node.js 14 and up (as Node.js v12 is EOL). Use release-it v14 for
environments running Node.js v10 and v12. Also see [CHANGELOG.md][63].

## Links

- See [CHANGELOG.md][63] for major/breaking updates, and [releases][64] for a detailed version history.
- To **contribute**, please read [CONTRIBUTING.md][65] first.
- Please [open an issue][66] if anything is missing or unclear in this documentation.

## License

[MIT][67]

[1]: #git
[2]: #hooks
[3]: #github-releases
[4]: #gitlab-releases
[5]: #changelog
[6]: #publish-to-npm
[7]: #manage-pre-releases
[8]: #plugins
[9]: ./docs/ci.md
[10]: https://github.com/release-it/release-it/actions
[11]: https://github.com/release-it/release-it/workflows/Cross-OS%20Tests/badge.svg
[12]: https://www.npmjs.com/package/release-it
[13]: https://badge.fury.io/js/release-it.svg
[14]: ./docs/npm.md#yarn
[15]: https://www.youtube.com/watch?v=7pBcuT7j_A0
[16]: https://twitter.com/KodapsAcademy
[17]: ./config/release-it.json
[18]: ./docs/configuration.md
[19]: ./docs/npm.md
[20]: https://github.com/release-it/bumper
[21]: https://github.com/release-it/conventional-changelog
[22]: https://github.com/casmith/release-it-calver-plugin
[23]: ./docs/git.md
[24]: https://docs.github.com/en/repositories/releasing-projects-on-github/about-releases
[25]: ./docs/github-releases.md
[26]: https://docs.gitlab.com/ce/user/project/releases/
[27]: https://gitlab.com/profile/personal_access_tokens
[28]: ./docs/environment-variables.md
[29]: ./docs/gitlab-releases.md
[30]: ./docs/changelog.md
[31]: ./docs/pre-releases.md
[32]: ./docs/plugins.md#execution-order
[33]: ./docs/dry-runs.md
[34]: https://github.com/release-it/keep-a-changelog
[35]: https://github.com/release-it-plugins/lerna-changelog
[36]: https://github.com/release-it-plugins/workspaces
[37]: https://github.com/grupoboticario/news-fragments
[38]: https://github.com/j-ulrich/release-it-regex-bumper
[39]: https://www.npmjs.com/search?q=keywords:release-it-plugin
[40]: ./docs/plugins.md
[41]: ./docs/recipes/programmatic.md
[42]: https://github.com/axios/axios
[43]: https://github.com/blockchain/blockchain-wallet-v4-frontend
[44]: https://github.com/callstack/react-native-paper
[45]: https://github.com/ember-cli/ember-cli
[46]: https://github.com/js-cookie/js-cookie
[47]: https://github.com/metalsmith/metalsmith
[48]: https://github.com/mozilla/readability
[49]: https://github.com/pahen/madge
[50]: https://github.com/redis/node-redis
[51]: https://github.com/reduxjs/redux
[52]: https://github.com/saleor/saleor
[53]: https://github.com/Semantic-Org/Semantic-UI-React
[54]: https://github.com/shipshapecode/shepherd
[55]: https://github.com/StevenBlack/hosts
[56]: https://github.com/swagger-api/swagger-ui
[57]: https://github.com/swagger-api/swagger-editor
[58]: https://github.com/tabler/tabler
[59]: https://github.com/tabler/tabler-icons
[60]: https://github.com/youzan/vant
[61]: https://github.com/release-it/release-it/network/dependents
[62]: https://github.com/search?q=path%3A**%2F.release-it.json&type=code
[63]: ./CHANGELOG.md
[64]: https://github.com/release-it/release-it/releases
[65]: ./.github/CONTRIBUTING.md
[66]: https://github.com/release-it/release-it/issues/new
[67]: ./LICENSE
