# git-replay ![GitHub release (latest by date)](https://img.shields.io/github/v/release/olets/git-replay)

Automate the rebasing of Git branches and creation of "stage" branches (ie branches into which one or more feature branches are merged with a merge commit).

Handy if you have several features in progress at once, and find yourself rebasing feature branches and recreating stage branches every time there's a significant change in the trunk.

- [Installation](#installation)
- [Requirements](#requirements)
- [Usage](#usage)
  - [Options](#options)
  - [Subcommands](#subcommands)
  - [Handling conflicts](#handling-conflicts)
- [Configuration](#configuration)
- [Related](#related)
- [Contributing](#contributing)
- [License](#license)

## Installation

***Homebrew*** is the recommended installation method.

### Homebrew

Recommended. Download and install `git-replay` and its dependency [yq](https://github.com/mikefarah/yq/releases/latest) with one command:

```shell
brew install olets/tap/git-replay
```

### With a shell plugin manager

1. Install [yq](https://github.com/mikefarah/yq)
1. Add `git-replay` to your shell plugin manager. Tested on zinit but these snippets should all work — if one doesn't, open an issue!
    ```shell
    # antibody: add this to your plugins file
    olets/git-replay

    # antigen: add this to ~/.zshrc
    antigen bundle olets/git-replay

    # omz
    git clone https://github.com/olets/git-replay.git $ZSH_CUSTOM/plugins/git-replay
    # and then add git-replay to the plugins list in ~/.zshrc

    # zgen: add this to the zgen load function in your ~/.zshrc
    zgen load olets/git-replay

    # zplug: add this to ~/.zshrc
    zplug "olets/git-replay", as:command

    # zinit: add this to ~/.zshrc
    zplugin ice as"program" pick"git-replay"
    zplugin light olets/git-replay
    ```

### Manual

1. Install [yq](https://github.com/mikefarah/yq)
1. Download [the latest binary](https://github.com/olets/git-replay/releases/latest)
1. Put the file `git-replay` in a directory in your `PATH`

## Requirements

- Git
- [yq](https://github.com/mikefarah/yq)
- zsh (does not need to be your default interactive shell, just needs to be installed)

## Usage

`git-replay` "replays" configured rebases and stage branch creation. Configuration lives in the `git-replay.yaml` config file (customizable with `--file`). The file is YAML and can have any of the top-level objects `rebase`, `rebase-onto`, and `stage`. (See [Configuration](#configuration).)

```
git replay [--file <config file path>] [--back-up] [--dry-run]
          [(--quiet | -q) | (--quieter | -qq)] [--no-color]
          [((--continue | --skip | --continue) | <subcommand>)]
```

```
git replay (--help | help)
```

```
git replay (--version | -v)
```

### Examples

Run the rebases and/or stage creations specified in the configuration file, saving a backup.

```shell
git replay --back-up
```

Something went wrong? Restore the backup.

```shell
git replay backup-restore
```

### Options

Option | Effect
---|---
`--back-up` | Create a `git-replay/`-prefixed backup branches for every manipulated branch
`--dry-run` | Log commands but do not run them
`--file <config file path>` | The configuration file to use (defaults to `git-replay.yaml`)
`--no-color` | Do not colorize output
`--quiet` or `-q` | Quiet standard Git output
`--quieter` or `-qq` | Quiet standard Git output and git-replay output

### Subcommands

**--abort**

Aborts the in-progress replay. _Unlike `git rebase --abort`, completed actions are not undone._

If `--file <config file path>` is used, it must come before `--abort`. No other options are supported.

**--continue**

Continues the in-progress replay.

**--skip**

Skips the current action and continues the in-progress replay.

**add** ⚠️

> Editing the config file interactively is temporarily unsupported. See [#2](https://github.com/olets/git-replay/issues/2)

```shell
git replay add rebase <upstream> <branch>
git replay add rebase-onto <newbase> <upstream> <branch>
git replay add stage <start-point> <upstream> <branch>
```

`git-replay` uses a YAML config file, so editing the file directly is a good experience. But there is a CLI too.

`git replay add rebase <upstream> <branch>` adds to the configuration file the configuration for `git rebase <upstream> <branch>`.

`git replay add rebase-onto <newbase> <upstream> <branch>` adds to the configuration file the configuration for `git rebase --onto <newbase> <upstream> <branch>`.

`git replay add stage <start point> <upstream> <branch>` adds to the configuration file the configuration for `git switch -C <upstream> <start point> && git merge --no-ff <branch>`.

Any options (e.g. `--quiet`) must be specified ahead of this command.

**backup-delete**

```shell
git replay backup-delete
```

Delete all backup branches. _Backup branches are defined as those with the prefix `git-replay/`._ If the backed up branch is not found —for example if there is no branch `x` to go with the backup branch `git-replay/x`— a warning will be printed and the backup branch will not be deleted.

Any options (e.g. `--quiet`) must be specified ahead of this command.

**backup-restore**

```shell
git replay backup-restore
```

Reset every configured branch to its `git-replay/`-prefixed backup, and then delete all backups.

For every backup branch, reset the current branch to the backup and then delete the backup branch. _Backup branches are defined as those with the prefix `git-replay/`._ If the backed up branch is not found —for example if there is no branch `x` to go with the backup branch `git-replay/x`— a warning will be printed and the backup branch will not be deleted.

Any options (e.g. `--quiet`) must be specified ahead of this command.

**delete** ⚠️

> Editing the config file interactively is temporarily unsupported. See [#2](https://github.com/olets/git-replay/issues/2)

```shell
git replay delete rebase <upstream> [<branch>]
git replay delete rebase-onto <newbase> [<upstream> [<branch>]]
git replay delete stage <start-point> [<upstream> [<branch>]]
```

`git-replay` uses a YAML config file, so editing the file directly is a good experience. But there is a CLI too.

`git replay delete rebase <upstream>` deletes from the configuration file all configured `rebase`s off `<upstream>`.

`git replay delete rebase <upstream> <branch>` deletes from the configuration file the configuration for `git rebase <upstream> <branch>`.

`git replay delete rebase-onto <newbase>` deletes from the configuration file all configured `rebase --onto`s with the `newbase` `<newbase>`.

`git replay delete rebase-onto <newbase> <upstream>` deletes from the configuration file all configured `rebase --onto`s with the `newbase` `<newbase>` and `upstream` `<upstream>`.

`git replay delete rebase-onto <newbase> <upstream> <branch>` deletes from the configuration file the configuration for `git rebase --onto <newbase> <upstream> <branch>`.

`git replay delete stage <start point>` deletes from the configuration file all configured "stages" with the "start point" `<start point>`.

`git replay delete stage <start point> <upstream>` deletes from the configuration file all "stages" with the "start point" `<start point>` and `upstream` `<upstream>`.

`git replay delete stage <start point> <upstream> <branch>` deletes the configuration for `git checkout -B <upstream> <start point> && git merge --no-ff <branch>`.

Any options (e.g. `--quiet`) must be specified ahead of this command.

**help**

```shell
git replay (help | --help)
```

Show the manpage.

**rebase**

```shell
git replay rebase
```

Replay only the configured `git rebase`s. Does not include configured `git rebase --onto`s.

**rebase-onto**

```shell
git replay rebase-onto
```

Replay only the configured `git rebase --onto`s. Does not include configured non-`--onto` `git rebase`s.

**stage**

```shell
git replay stage
```

Replay only the configured "stages".

**version**

```shell
git replay (--version | -v)
```

Print the `git-replay` version.

### Handling conflicts

`git-replay` works best with [`git-rerere`](https://git-scm.com/docs/git-rerere) enabled.

> `git-rerere` has the potential to resolve conflicts in ways you don’t want. Familiarity with rerere is recommended.

To enable `git-rerere`, run `git config rerere.enabled true`.

If you hit a conflict while replaying, resolve it and then run `git replay --continue`. `git-replay` will have `git-rerere` record the resolution and then will continue the replay.

## Configuration

`git-replay`'s configuration is a human-readable YAML file, and a handy reference document for keeping track of WIP and how branches relate.

You can manage the file from the command line with `git replay add` and `git replay delete`. Editing the file directly may be an even better experience.

The configuration file takes top-level objects with keys `rebase`, `rebase-onto`, and `stage`. There can be multiple of each and they can be in any order, but _actions will always be run in the following order_:

1. All `rebase`s, in the order they appear in the configuration file
1. All `rebase-onto`s, in the order they appear in the configuration file
1. All `stage`s, in the order they appear in the configuration file

### Rebases

To automate

```shell
git rebase <upstream> <branch>
```

use the form

```yaml
rebase:
  <upstream>:
    - <branch>
```

or

```yaml
rebase:
  <upstream>: <branch>
```

To automate

```shell
git rebase <upstream> <branch1>
git rebase <upstream> <branch2>
```

use the form

```yaml
rebase:
  <upstream>:
    - <branch1>
    - <branch2>
```

To automate

```shell
git rebase --onto <newbase> <upstream> <branch>
```

use the form

```yaml
rebase-onto:
  <newbase>:
    <upstream>:
      - <branch>
```

or, if there's only one <branch>

```yaml
rebase-onto:
  <newbase>:
    <upstream>: <branch>
```

To automate

```shell
git rebase --onto <newbase> <upstream> <branch1>
git rebase --onto <newbase> <upstream> <branch2>
```

use the form

```yaml
rebase-onto:
  <newbase>:
    <upstream>:
      - <branch1>
      - <branch2>
```

For example, to automate

```shell
git rebase main feature-1
git rebase main feature-2
git rebase feature-2 feature-4
git rebase main next
git rebase --onto feature-2 feature-2@{u} feature-2b
git rebase --onto next next@{u} feature-3
```

use

```yaml
# git-replay.yaml
rebase:
  main:
    - feature-1
    - feature-2
    - next
  feature-2:
    - feature-4
rebase-onto:
  feature-2:
    feature-2@{u}:
      - feature-2b
  next:
    next@{u}:
      - feature-3
```

(Note that array notation `a: [b, c]` is not supported.)

### Stages

To automate

```shell
git switch -C <branch> <start-point>
				# aka `git checkout -B <branch> <start-point>`
        # aka `git checkout <branch> && git reset --hard <start-point>
git commit --no-ff <commit-1>
git commit --no-ff <commit-2>
```

use the form

```yaml
<start-point>:
  <branch>:
    - <commit-1>
    - <commit-2>
```

For example, to automate

```shell
git switch -C development main
				# aka `git checkout -B development main`
        # aka `git checkout development && git reset --hard main`
git commit --no-ff feature-1
git commit --no-ff feature-2
git switch -C staging main
				# aka `git checkout -B staging main`
        # aka `git checkout staging && git reset --hard main`
git commit --no-ff feature-1
git commit --no-ff feature-3
```

use

```yaml
# git-replay.yaml
stage:
  main:
    development:
      - feature-1
      - feature-2
    staging:
      - feature-1
      - feature-3
```

(Note that array notation `a: [b, c]` is not supported.)

## Related

Inspired by [git-assembler](https://gitlab.com/wavexx/git-assembler), which is “Like ‘make’, for branches.” It's cool, check it out! It also has the flexibility to do more than rebase and create stage branches.

## Contributing

Thanks for your interest. Contributions are welcome!

> Please note that this project is released with a [Contributor Code of Conduct](CODE_OF_CONDUCT.md). By participating in this project you agree to abide by its terms.

Check the [Issues](https://github.com/olets/git-replay/issues) to see if your topic has been discussed before or if it is being worked on.

Please read [CONTRIBUTING.md](CONTRIBUTING.md) before opening a pull request.

## License

<p xmlns:dct="http://purl.org/dc/terms/" xmlns:cc="http://creativecommons.org/ns#" class="license-text"><a rel="cc:attributionURL" property="dct:title" href="https://www.github.com/olets/git-replay">git-replay</a> by <a rel="cc:attributionURL dct:creator" property="cc:attributionName" href="https://www.github.com/olets">Henry Bley-Vroman</a> is licensed under <a rel="license" href="https://creativecommons.org/licenses/by-nc-sa/4.0">CC BY-NC-SA 4.0</a> with a human rights condition from <a href="https://firstdonoharm.dev/version/2/1/license.html">Hippocratic License 2.1</a>. Persons interested in using or adapting this work for commercial purposes should contact the author.</p>

<img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/cc.svg?ref=chooser-v1" /><img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/by.svg?ref=chooser-v1" /><img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/nc.svg?ref=chooser-v1" /><img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/sa.svg?ref=chooser-v1" />

For the full text of the license, see the [LICENSE](LICENSE) file.
