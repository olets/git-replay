# git-replay ![GitHub release (latest by date)](https://img.shields.io/github/v/release/olets/git-replay)

Automate the rebasing of Git branches and creation of "stage" branches (ie branches into which one or more feature branches are merged with a merge commit).

Handy if you have several features in progress at once, and find yourself rebasing feature branches and/or recreating stage branches every time there's a significant change in the trunk.

- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
  - [Examples](#examples)
  - [Options](#options)
  - [Subcommands](#subcommands)
  - [Handling conflicts](#handling-conflicts)
- [Action configuration](#action-configuration)
- [Option configuration](#option-configuration)
- [Related](#related)
- [Contributing](#contributing)
- [License](#license)

## Requirements

- Git
- [yq](https://github.com/mikefarah/yq) (installed for you if you install `git-replay` with Homebrew, as detailed below)
- zsh (does not need to be your default interactive shell, just needs to be installed)
## Installation

***Homebrew*** is the recommended installation method.

### Homebrew

Recommended. Download and install `git-replay` and its dependency [yq](https://github.com/mikefarah/yq/) with one command:

```shell
brew install olets/tap/git-replay
```

### With a shell plugin manager

1. Install [yq](https://github.com/mikefarah/yq)
1. Install git-replay with a zsh plugin manager. Each has their own way of doing things. See your package manager's documentation or the [zsh plugin manager plugin installation procedures gist](https://gist.github.com/olets/06009589d7887617e061481e22cf5a4a).

    zinit users will need to use the `ice`s `as"program" pick"git-replay"`

    zplug users will need to use the tag `as:command`

    After adding the plugin to the manager, restart zsh:

    ```shell
    exec zsh
    ```

### Manual

1. Install [yq](https://github.com/mikefarah/yq)
1. Download [the latest `git-replay` binary](https://github.com/olets/git-replay/releases/latest)
1. Put the file `git-replay` in a directory in your `PATH`

## Usage

`git-replay` "replays" configured rebases and stage branch creation. Configuration lives in the `git-replay.yaml` config file (customizable with `--file` and `--rev`). The file is YAML and can have any of the top-level objects `rebase`, `rebase-onto`, and `stage`. (See [Configuration](#configuration).)

```
git replay [--file <config file path>] [--rev <revision>]
    [--back-up] [--dry-run]
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

**The basics:**

Run the actions specified in the configuration file, saving a backup.

```shell
git replay --back-up
```

Change your mind? Restore the backup.

```shell
git replay restore-backup
```

**Recommended:**

In most cases it will be desirable to replay the same actions regardless of what branch is checked out when `git replay` is run. Either

- gitignore the configuration file. Now regardless of what commit is checked out you can manage the configuration file and/or run `git replay [...]`

    > For this to work, there must be no revision configured (see [Options](#options)). If you have configured a default revision globally (e.g. with `git config --global replay.rev <revision>`) you will need to nullify it locally (e.g. with `git config replay.rev ''`).

- or always read the configuration from the same revision. In this approach it is possible for collaborators to share a `git-replay` configuration.

    For example to keep the canonical configuration file in `main`:

    ```shell
    git checkout main
    # then add, fill out, and commit the configuration file
    ```

    and when updating the configuration file first check out `main`.

    When running `git-replay`,

    - either run
        ```shell
        git replay --rev main [...]
        ```
    - or configure Git once

        ```shell
        git config replay.rev main
        ```

        and then run

        ```shell
        git replay [...]
        ```

### Options

Option | Effect
---|---
`--back-up` | Create `git-replay/`-prefixed backup branches for every manipulated branch
`--rev <revision>` | The [revision](https://git-scm.com/docs/gitrevisions) from which to read the configuration file (defaults to the checked out commit).<br>You can also configure this as a `git-config` option (see [Option configuration](#option-configuration)).
`--dry-run` | Log commands but do not run them
`--file <config file path>` | The configuration file to use (defaults to `git-replay.yaml`).<br>You can also configure this as a `git-config` option (see [Option configuration](#option-configuration)).
`--no-color` | Do not colorize output
`--quiet` or `-q` | Quiet standard Git output
`--quieter` or `-qq` | Quiet standard Git output and git-replay output

### Subcommands

#### --abort

Aborts the in-progress replay. _Unlike `git rebase --abort`, completed actions are not undone. Use `git replay restore-backup` to go back._

#### --continue

Continues the in-progress replay.

#### --dediverge [--force]

Run [`git replay dediverge`](#dediverge) after completing the replay.

Largely equivalent to following the replay with `git replay dediverge` but with less repetition in the logs.

Add `--force` to skip the prompt.

#### --skip

Skips the current action and continues the in-progress replay.

#### back-up

Create `git-replay/`-prefixed backup branches for every branch that would be manipulated, and do nothing else.

#### dediverge

```shell
git replay dediverge [--force]
```

Identifies all manipulated branches (i.e. `<branch>`es in `rebase`s, and `<branch>`es and `<commit>`s in `stage`s) that have diverged from their push destinations (aka [`@{push}`](https://www.git-scm.com/docs/gitrevisions#Documentation)), and offers to [`git push --force-with-lease`](https://git-scm.com/docs/git-push) each.

Add `--force` to not prompt before force pushing.

#### delete-backup

```shell
git replay delete-backup
```

Delete all backup branches. _Backup branches are defined as those with the prefix `git-replay/`._ If the backed up branch is not found —for example if there is no branch `x` to go with the backup branch `git-replay/x`— a warning will be printed and the backup branch will not be deleted.

#### help

```shell
git replay (help | --help)
```

Show the manpage.

#### rebase

```shell
git replay rebase
```

Replay only the configured `git rebase`s. Does not include configured `git rebase --onto`s.

#### rebase-onto

```shell
git replay rebase-onto
```

Replay only the configured `git rebase --onto`s. Does not include configured non-`--onto` `git rebase`s.

#### restore-backup

```shell
git replay restore-backup
```

Reset every configured branch to its `git-replay/`-prefixed backup, and then delete all backups.

For every backup branch, reset the current branch to the backup and then delete the backup branch. _Backup branches are defined as those with the prefix `git-replay/`._ If the backed up branch is not found —for example if there is no branch `x` to go with the backup branch `git-replay/x`— a warning will be printed and the backup branch will not be deleted.

#### stage

```shell
git replay stage
```

Replay only the configured "stages".

#### version

```shell
git replay (--version | -v)
```

Print the `git-replay` version.

### Handling conflicts

`git-replay` works best with [`git-rerere`](https://git-scm.com/docs/git-rerere) enabled.

> `git-rerere` has the potential to resolve conflicts in ways you don’t want. Familiarity with rerere is recommended.

To enable `git-rerere`, run `git config rerere.enabled true`.

If you hit a conflict while replaying, resolve it and then run `git replay --continue`. `git-replay` will have `git-rerere` record the resolution and then will continue the replay.

## Action configuration

`git-replay`'s configuration is a human-readable YAML file, and a handy reference document for keeping track of WIP and how branches relate.

The configuration file takes top-level objects with keys `rebase`, `rebase-onto`, and `stage`.

> Actions will always be run in the following order:
> 1. All `rebase`s, in the order they appear in the configuration file
> 1. Then all `rebase-onto`s, in the order they appear in the configuration file
> 1. Then all `stage`s, in the order they appear in the configuration file

### Rebases

To automate `rebase`

```shell
git rebase <upstream> <branch>
```

use

```yaml
rebase:
  <upstream>: <branch>
```

To automate `rebase --onto`

```shell
git rebase --onto <newbase> <upstream> <branch>
```

use

```yaml
rebase-onto:
  <newbase>:
    <upstream>: <branch>
```

Configure any number of rebases. List notation and square bracket array notations are supported.

```yaml
rebase:
  main:
    - feature-1
    - feature-2
```

```yaml
rebase:
  main: [feature-1, feature-2]
```

```yaml
rebase:
  main: feature-1
  mybase:
    - myfeature
    - another
```

> Tip: leverage [gitrevisions](https://git-scm.com/docs/gitrevisions#Documentation/gitrevisions.txt) and `rebase-onto` to manage stacked branches.
>
> ```yaml
> rebase:
>   main:
>     - feature-1
> rebase-onto:
>   feature-1:
>     feature-1@{u}:
>       - feature-2
> ```

### Stages

To automate

```shell
git switch -C <branch> <start-point>
        # aka `git checkout -B <branch> <start-point>`
        # aka `git checkout <branch> && git reset --hard <start-point>
git commit --no-ff <commit-1>
```

use the form

```yaml
<start-point>:
  <branch>: <commit-1>
```

Configure any number of stages. List and square bracket array notations are supported.

```yaml
stage:
  main:
    development:
      - feature-1
      - feature-3
    staging: [feature-1, feature-2]
```

```yaml
stage:
  main:
    development: feature-1
    staging: feature-2
  mybranch:
    mystage: myfeature
```

## Option configuration

`git-replay` supports [`git-config`](https://git-scm.com/docs/git-config).

### File

Set `git config [--global] replay.file` to use a custom `file` without passing the `--file` parameter.

For example

```shell
git config --global replay.file ./config/git-replay.yaml`
```

will let you run `git replay` in place of `git replay --file ./config/git-replay.yaml`.

### Rev

Set `git config [--global] replay.rev` to specify a revision without passing the `--rev` parameter.

For example

```shell
git config --global replay.rev main
```

will let you run `git replay` instead of `git replay --rev main`.

## Related

Inspired by [git-assembler](https://gitlab.com/wavexx/git-assembler), which is “Like ‘make’, for branches.”

## Contributing

Thanks for your interest. Contributions are welcome!

> Please note that this project is released with a [Contributor Code of Conduct](CODE_OF_CONDUCT.md). By participating in this project you agree to abide by its terms.

Check the [Issues](https://github.com/olets/git-replay/issues) to see if your topic has been discussed before or if it is being worked on.

Please read [CONTRIBUTING.md](CONTRIBUTING.md) before opening a pull request.

## License

<p xmlns:dct="http://purl.org/dc/terms/" xmlns:cc="http://creativecommons.org/ns#" class="license-text"><a rel="cc:attributionURL" property="dct:title" href="https://www.github.com/olets/git-replay">git-replay</a> by <a rel="cc:attributionURL dct:creator" property="cc:attributionName" href="https://www.github.com/olets">Henry Bley-Vroman</a> is licensed under <a rel="license" href="https://creativecommons.org/licenses/by-nc-sa/4.0">CC BY-NC-SA 4.0</a> with a human rights condition from <a href="https://firstdonoharm.dev/version/2/1/license.html">Hippocratic License 2.1</a>. Persons interested in using or adapting this work for commercial purposes should contact the author.</p>

<img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/cc.svg?ref=chooser-v1" /><img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/by.svg?ref=chooser-v1" /><img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/nc.svg?ref=chooser-v1" /><img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/sa.svg?ref=chooser-v1" />

For the full text of the license, see the [LICENSE](LICENSE) file.
