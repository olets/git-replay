# git-replay

Automate the rebasing of Git branches and creation of stage branches (ie branches into which one or more feature branch is merged with a merge commit).

Handy if you follow a linear or merge-commit-free Git process, have several features in progress at once, and find yourself rebasing feature branches and recreating stage branches every time there's a significant change in the trunk.

## Installation

***Homebrew***

Recommended. Download and install `git-replay` and its dependency [yq](https://github.com/mikefarah/yq/releases/latest) with one command:

```shell
brew install olets/tap/git-replay
```

***Manual***

1. Download [the latest binary](https://github.com/olets/git-replay/releases/latest)
1. Put the file `git-replay` in a directory in your `PATH`
1. Install [yq](https://github.com/mikefarah/yq)

## Requirements

- Git ≥ 2.23*
- zsh

## Usage

`git-replay` first runs automated rebases and automated stage branch creation. Both are configured in the `git-replay.yaml` config file. The file is YAML and can have either or both of the top-level objects `rebase` and `stage`. (See [Configuration](#configuration).)

With configuration in place, run

```shell
git replay [<file.(yaml|yml)>] [--back-up] [--dry-run] [(--quiet | -q) | (--quieter | -qq)]
```

`git-replay` will rebase all configured branches with their configured upstreams, then reset the configured stage branches to their configured starting points and merge in the configured commits. Progress is logged. If something goes wrong everything is stopped.

Option | Effect
---|---
`--back-up` | Create a `git-replay/`-prefixed backup branches for every manipulated branch
`-dry-run` | Log commands but do not run them
`--quiet` or `-q` | Quiet standard Git output
`--quieter` or `-qq` | Quiet standard Git output and git-replay output

### Handling Git conflicts

`git-replay` works best with [`git-rerere`](https://git-scm.com/docs/git-rerere) enabled.

> `git-rerere` has the potential to resolve conflicts in ways you don’t want. Familiarity with rerere is recommended.

To enable `git-rerere`, run `git config rerere.enabled true`

- If you hit a conflict and have enabled `git-rerere`,

  1. resolve it
  1. teach `git-rerere` the resolution by running `git rerere`
  1. abort the in-progress action (run either `git rebase --abort` or `git merge --abort` as appropriate)
  1. run `git replay` again

- If you hit a conflict and have not enabled `git-rerere` either

  1. abort the in-progress action (run either `git rebase --abort` or `git merge --abort` as appropriate)
  1. enable `git-rerere`
  1. run `git replay` again
  1. optionally disable `git-rerere` again

  or

  1. abort the in-progress action (run either `git rebase --abort` or `git merge --abort` as appropriate)
  1. take the conflicted step out of the `git-replay` config
  1. run `git replay` again
  1. do the conflicted step manually
  1. optionally add the conflicted step back into the `git-replay` config

### Additional commands

**clean**

```shell
git replay clean [(--quiet | -q) | (--quieter | -qq)] [--dry-run]
```

Delete all branches with the prefix `git-replay/`.

**help**

```shell
git replay help
```

Show the manpage.

**restore**

```shell
git replay restore [(--quiet | -q) | (--quieter | -qq)] [--dry-run]
```

Reset every rebased branch and stage branch to its `git-replay/`-prefixed backup.

## Configuration

### Rebase automation

Configure automated rebases.

The value of each child is a branch name or an array of branch names. The value of each child is the upstream branch the branches in the value will be rebased with.

For example, to automate

```shell
git rebase main feature-1
git rebase main feature-2
git rebase production feature-3
```

use

```yaml
# git-replay.yaml
rebase:
  main:
    - feature-1
    - feature-2
  production: feature-3
```

or

```yaml
...
  production:
    - feature-3
```

(Note that array notation `a: [b, c]` is not supported.)

### Stage branch automation

Automate

```shell
git switch -C <branch> <start-point>
git commit --no-ff <commit-1>
git commit --no-ff <commit-2>
```

with objects in the form

```yaml
<start-point>:
  <branch>:
    - <commit-1>
    - <commit-2>
```

For example, to automate

```shell
git switch -C development main
git commit --no-ff feature-1
git commit --no-ff feature-2
git switch -C staging main
git commit --no-ff feature-1
git commit --no-ff feature-3
git switch -C next production
git commit --no-ff feature-2
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
  production:
    next: feature-2
```

or

```yaml
...
  production:
    next:
      - feature-2
```

(Note that array notation `a: [b, c]` is not supported.)

## Related

Inspired by [git-assembler](https://gitlab.com/wavexx/git-assembler), which is “Like ‘make’, for branches.” It's cool, check it out! Where git-replay relies on git-rerere in combination with repeated runs to get past conflicts, git-assembler is able resume the latest run. It also has the flexibility to do more than rebase and create stage branches.

## Contributing

Thanks for your interest. Contributions are welcome!

> Please note that this project is released with a [Contributor Code of Conduct](CODE_OF_CONDUCT.md). By participating in this project you agree to abide by its terms.

Check the [Issues](https://github.com/olets/git-replay/issues) to see if your topic has been discussed before or if it is being worked on.

Please read [CONTRIBUTING.md](CONTRIBUTING.md) before opening a pull request.

## License

This project is licensed under [MIT license](http://opensource.org/licenses/MIT).
For the full text of the license, see the [LICENSE](LICENSE) file.
