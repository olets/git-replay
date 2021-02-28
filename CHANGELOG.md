# [2.3.0](https://github.com/olets/git-replay/compare/v2.2.1...v2.3.0) (2021-02-28)


### Features

* **backup:** warn if branch was not backed up ([bd2e392](https://github.com/olets/git-replay/commit/bd2e392fe81bbd351a1686d71b573fa79c3cdf8b))
* **dediverge:** support new feature ([fed9df4](https://github.com/olets/git-replay/commit/fed9df4321727285871ec48ea2dbdfa93455d006)), ([8afa79f](https://github.com/olets/git-replay/commit/8afa79fd5c480cd8d1f168c996238edb2b7d4a55)), ([da3f0f1](https://github.com/olets/git-replay/commit/da3f0f1ebcfbcf89d0b5421afec6c31ef57dd73e)), ([f7567e1](https://github.com/olets/git-replay/commit/f7567e170644dda600f361457a4ed5b99d8670ce))
* **quiet:** use verbose commands when expected ([01ec3bf](https://github.com/olets/git-replay/commit/01ec3bf09288d3080ace1fb4fd2214929305ffb0))



# [2.2.1](https://github.com/olets/git-replay/compare/v2.2.0...v2.2.1) (2020-11-22)


### Bug Fixes

* **skip:** do not re-queue rebase-ontos ([2af9079](https://github.com/olets/git-replay/commit/2af9079250772ace719e859ed8cbbb979a639e34))


### Features

* **no-color:** support new option ([da7c305](https://github.com/olets/git-replay/commit/da7c3057f0b5db83b03457d663ddb9c2931b7171))
* **rebase, rebase-onto:** do not allow bad configuration ([46a0256](https://github.com/olets/git-replay/commit/46a025605037ff3f7697799dbd0d2a2d0283da8e))
* **stage:** friendlier handling of bad start points and commits ([2fd9082](https://github.com/olets/git-replay/commit/2fd9082e03462bfea56d8e4559d2f6424b8dbf0a))



# [2.2.0](https://github.com/olets/git-replay/compare/v2.1.0...v2.2.0) (2020-11-22)


### Bug Fixes

* **backups:** follow 2.x rebranding ([ea3d274](https://github.com/olets/git-replay/commit/ea3d27433aed4b9cd8c48d9a8725f72e12d7b8a6))
* **man:** use name 'git-replay' ([ddf89f2](https://github.com/olets/git-replay/commit/ddf89f204aa2741a5b840d4f00f8d4d260c0a558))


### Features

* **git:** support older versions by not using 'switch' ([8d280ec](https://github.com/olets/git-replay/commit/8d280ec1474d7fa4f618c38b9cb2762be5a81837))
* **help:** add subcommand ([8c795a0](https://github.com/olets/git-replay/commit/8c795a05b22bd20eea7b87824f293c6d68ffe9e6))
* **restore:** support rebase-onto and stage branches ([86c1720](https://github.com/olets/git-replay/commit/86c1720a8c70f60ba2cf0ddd45ac38443f7f81e8))
* **run:** error if todos exist and --continue not passed ([6934ef3](https://github.com/olets/git-replay/commit/6934ef34950ac17290fbfba8886b4e7229ef2e3b))
* **skip:** add support for --skip ([c5f2cb6](https://github.com/olets/git-replay/commit/c5f2cb6af4ba754248ec80748286c1506bd8b637))
* **version:** add subcommand ([58645fa](https://github.com/olets/git-replay/commit/58645fab96e33a7ad8c1c42c4eaf85f88fe24908))



# [2.1.0](https://github.com/olets/git-replay/compare/v2.0.1...v2.1.0) (2020-09-06)


### Bug Fixes

* **continue:** correct values + log divergence ([fffff5a](https://github.com/olets/git-replay/commit/fffff5a066d0cb59079d3c0c8e98ac6d265d3365))
* **quieter:** implies quiet ([e6da6d3](https://github.com/olets/git-replay/commit/e6da6d32c5180c57323751715eb3a3e1b815fff5))


### Features

* **abort:** support new option ([e49f744](https://github.com/olets/git-replay/commit/e49f744259621748a054f01b25b4076e3eb0f325))
* **add:** support new subcommand ([017fcb4](https://github.com/olets/git-replay/commit/017fcb4672689c55769388f099253e92be6f416a))
* **backup-delete, backup-restore:** rename functions ([32f86ae](https://github.com/olets/git-replay/commit/32f86aeed968995df8ea33a1ccb388a454d6566b))
* **continue:** add support ([98be38b](https://github.com/olets/git-replay/commit/98be38b647be36212551186fa50ec98b139972ef))
* **continue,merge,rebase:** complete conflicted merge/rebase ([90b8c91](https://github.com/olets/git-replay/commit/90b8c9196fb2996b8cdb9732ae1b9fb6bb430013))
* **delete:** support new subcommand ([3b96e0a](https://github.com/olets/git-replay/commit/3b96e0a7bcc01abac347132d6cbf0ed68302aa7c))
* **diverged:** warnings persist across continuations ([934e888](https://github.com/olets/git-replay/commit/934e88833932f1a124dac33247fc28a7b6d458ee))
* **rebase:** continuation does not invoke editor ([1becb02](https://github.com/olets/git-replay/commit/1becb02c2a67a1fb0a10a9fa4dd716096d8401a8))
* **rebase-onto:** supportrebase --onto ([e623dd2](https://github.com/olets/git-replay/commit/e623dd2f6027ca0e1fb9d03d1935a9741f6cfacd))
* **warnings:** only list at end ([ab6e32e](https://github.com/olets/git-replay/commit/ab6e32e39594d061fb92c121a3dc220ce28abeea))



## [2.0.1](https://github.com/olets/git-replay/compare/v2.0.0...v2.0.1) (2020-09-06)


### Bug Fixes

* **merge,rebase:** error logs + resolution hints ([6d850d8](https://github.com/olets/git-replay/commit/6d850d82a787a1762186c6a84965d5cce23983cc))


### Features

* **print_error:** exit after printing ([d0c6d5d](https://github.com/olets/git-replay/commit/d0c6d5d06d736d0621c21960c55e2ae9beb1024d))



# [2.0.0](https://github.com/olets/git-replay/compare/v1.0.0-b2...v2.0.0) (2020-09-06)


### Features

* **name:** now known as git-replay ([0500649](https://github.com/olets/git-replay/commit/05006499fec5c5a304564d48ff75f213d18a9f13))
* **replay:** complete rewrite ([4466466](https://github.com/olets/git-replay/commit/44664666beedc06eac6ff3b24a352a63fa6184b1))



# [1.0.0-b2](https://github.com/olets/git-replay/compare/v1.0.0-b1...v1.0.0-b2) (2020-08-04)


### Bug Fixes

* **rebase:** exit if Git has a fatal error (e.g. no such branch) ([85400f1](https://github.com/olets/git-replay/commit/85400f16846fc021c39668d6a8051869ae852632))
* **rebase, stage:** stop if not able to continue through conflicts ([557ae2b](https://github.com/olets/git-replay/commit/557ae2b2d849019add320dc410e9c81756b60b04))
* **resolve_conflict:** respect quiet option ([dc34f24](https://github.com/olets/git-replay/commit/dc34f245b1fde8672261d78f8bf080a5f77e13b0))
* **restore:** handle missing backups ([8748506](https://github.com/olets/git-replay/commit/87485069d1fee3691400be1ee3a2c5fa456fe534))
* **restore:** use correct variable ([6f65bf4](https://github.com/olets/git-replay/commit/6f65bf4d6a63e691d371ed6b5f66dd52ae493826))


### Features

* **backups:** can save, restore from, and delete backups ([77ce7b9](https://github.com/olets/git-replay/commit/77ce7b95af2738d0286b1fefecbe88d9e2f26f95))
* **dry run:** add support ([b84486b](https://github.com/olets/git-replay/commit/b84486becc7cc45b902e45f13d4e8fdacbeb6be7))
* **help:** support option ([89fbbcb](https://github.com/olets/git-replay/commit/89fbbcbedc72afbc0c95d3e8293846db82952a1d))
* **manpage:** support colorization plugins ([099f225](https://github.com/olets/git-replay/commit/099f22510063b35ebdaa77d9ca0026ccbaaf46ea))
* **quiet/er:** support logging options ([a8e6cfa](https://github.com/olets/git-replay/commit/a8e6cfa2ebe1a9bdfc7daeea56f01ddf90173384))
* **stage, rebase:** only log starting message if changes will be made ([eaf3276](https://github.com/olets/git-replay/commit/eaf3276ab84ebb45968542685c34f436063c765c))
* **version:** add option ([34ca4e5](https://github.com/olets/git-replay/commit/34ca4e57af4e35637b7853834a7cd0364ba47006))



# [1.0.0-b1](https://github.com/olets/git-replay/compare/f99cfee94e80ade03e097fff8171cae8a29fb818...v1.0.0-b1) (2020-08-01)


### Bug Fixes

* **rebase, stage:** no false positive exits ([0aba09c](https://github.com/olets/git-replay/commit/0aba09cf275460d6cbda8d944fdf3b253de63bcf))


### Features

* **app:** working standalone script ([f99cfee](https://github.com/olets/git-replay/commit/f99cfee94e80ade03e097fff8171cae8a29fb818))
* **config:** can specify the config file ([55d73a5](https://github.com/olets/git-replay/commit/55d73a5c05fe22a1a790738d5b74af49b0d74abd))
* **config:** uses a yaml file ([6e75819](https://github.com/olets/git-replay/commit/6e75819e0185d72f7fe4b3094aa28f9a2fe87eeb))
* **project:** rename to "git-renew"... ([f740463](https://github.com/olets/git-replay/commit/f74046320059eddc03caa74e82e1c9a325a04e27))



