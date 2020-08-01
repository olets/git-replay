# git-renew

Automate the rebasing of Git branches and creation of stage branches (ie branches into which one or more feature branch is merged with a merge commit).

Handy if you follow a linear or merge-commit-free Git process, have several features in progress at once, and find yourself rebasing feature branches and recreating stage branches every time there's a significant change in the trunk.

## Installation

***Homebrew***

Recommended. Download and install `git-renew` and its dependency [yq](https://github.com/mikefarah/yq/releases/latest) with one command:

```shell
brew install olets/tap/git-renew
```

***Manual***

1. Download [the latest binary](https://github.com/olets/git-renew/releases/latest)
1. Put the file `git-renew` in a directory in your `PATH`
1. Install [yq](https://github.com/mikefarah/yq)

## Requirements

- Git ≥ 2.23*
- zsh

## Usage

`git-renew` first runs automated rebases and automated stage branch creation. Both are configured in the `git-renew.yaml` config file. The file is YAML and can have either or both of the top-level objects `rebase` and `stage`. (See [Configuration](#configuration).)

With configuration in place, run

```shell
git renew
```

`git-renew` will log what it's doing, along with all Git output.

### Handling Git conflicts

`git-renew` works best with [`git-rerere`](https://git-scm.com/docs/git-rerere) enabled.

> `git-rerere` has the potential to resolve conflicts in ways you don’t want. Familiarity with rerere is recommended.

To enable `git-rerere`, run `git config rerere.enabled true`

- If you hit a conflict and have enabled `git-rerere`,

  1. resolve it
  1. teach `git-rerere` the resolution by running `git rerere`
  1. abort the in-progress action (run either `git rebase --abort` or `git merge --abort` as appropriate)
  1. run `git renew` again

- If you hit a conflict and have not enabled `git-rerere` either

  1. abort the in-progress action (run either `git rebase --abort` or `git merge --abort` as appropriate)
  1. enable `git-rerere`
  1. run `git renew` again
  1. optionally disable `git-rerere` again

  or

  1. abort the in-progress action (run either `git rebase --abort` or `git merge --abort` as appropriate)
  1. take the conflicted step out of the `git-renew` config
  1. run `git renew` again
  1. do the conflicted step manually
  1. optionally add the conflicted step back into the `git-renew` config

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
# git-renew.yaml
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
# git-renew.yaml
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

Inspired by [git-assembler](https://gitlab.com/wavexx/git-assembler), which is “Like ‘make’, for branches.” It's cool, check it out! The major functional differences:
- `git-assembler` does not help with conflict resolution; `git-renew` can continue past a conflict point if `git-rerere` has resolved all the conflicts.
- `git-assembler` keeps track of how far its run has progressed; if something goes wrong, a conflict for example, it can continue from that point after the problem is resolved. `git-renew` does not and cannot, but because it automatically continues past rerere-resolved conflicts it does not need to; hit a conflict, resolve it, and re-run `git-renew`, and it starts from the top but breezes right through the previous run's stumbling block.

## Contributing

Thanks for your interest. Contributions are welcome!

> Please note that this project is released with a [Contributor Code of Conduct](CODE_OF_CONDUCT.md). By participating in this project you agree to abide by its terms.

Check the [Issues](https://github.com/olets/git-renew/issues) to see if your topic has been discussed before or if it is being worked on.

Please read [CONTRIBUTING.md](CONTRIBUTING.md) before opening a pull request.

## License

This project is licensed under [MIT license](http://opensource.org/licenses/MIT).
For the full text of the license, see the [LICENSE](LICENSE) file.
