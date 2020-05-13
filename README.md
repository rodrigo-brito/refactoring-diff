# Refactoring-aware Code Reviews

![Tests and Lint](https://github.com/rodrigo-brito/refactoring-aware-review/workflows/Tests%20and%20Lint/badge.svg)
![Chrome Web Store](https://img.shields.io/chrome-web-store/v/bclbegekihgpelanbbleaceefgmekjdd)
[![MIT license](https://img.shields.io/badge/License-MIT-blue.svg)](https://lbesson.mit-license.org/)

Code review is a key practice in modern software development. Currently, this practice is performed using textual diff tools, such as the one provided by GitHub. However, diff results can easily become complex and hard to understand. Refactorings, for example, are not represented at all in diff results. This makes diff understanding more complex because reviewers need to infer themselves that a given group of added and removed lines of code represent, for example, an extract method.

Refactoring-Aware Code Reviews is a tool pipeline that seamlessly enriches GitHub diff results with refactoring information. See the following figure:

<p align="center">
    <img src="https://user-images.githubusercontent.com/7620947/76000414-706c7f80-5ee2-11ea-8f21-06bfb2646b36.png" width= "400px" />
</p>

As presented in this figure, this pipeline has three key components:

-   [RefDiff](https://github.com/aserg-ufmg/RefDiff): a tool to detect refactoring operations in commits. We rely on GitHub Actions to automatically execute RefDiff after each Pull Request.

-   An [external server](https://github.com/rodrigo-brito/refactoring-aware-review/tree/server), that collects the list of refactorings produced by RefDiff.

-   A [Chrome plug-in](https://chrome.google.com/webstore/detail/refactoring-aware-review/bclbegekihgpelanbbleaceefgmekjdd) that seamlessly annotates diff chunks with refactoring data (see the following snapshot).

#### Move Method

![Move Method](https://user-images.githubusercontent.com/7620947/76126244-58c5f180-5fdd-11ea-9212-445c147d31cb.png)

#### Pull Up Method

![Pull Up Method](https://user-images.githubusercontent.com/7620947/76126249-595e8800-5fdd-11ea-8a1f-c066d44adbb8.png)

#### Rename Method

![Rename Method](https://user-images.githubusercontent.com/7620947/76126250-59f71e80-5fdd-11ea-8e8a-e3ba73473007.png)

## Installation

To detect refactorings in your repository, you must first install this [Chrome plug-in](https://chrome.google.com/webstore/detail/refactoring-aware-review/bclbegekihgpelanbbleaceefgmekjdd) and configure RefDiff on [Github Actions](https://help.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow).

<a href="https://chrome.google.com/webstore/detail/refactoring-aware-diff/bclbegekihgpelanbbleaceefgmekjdd">
    <img width="200px" src="https://user-images.githubusercontent.com/7620947/75341916-26e1bc00-5874-11ea-9526-463ddf1e7f82.png" />
</a>

To execute RefDiff as Github Actions, you need to create a workflow file in `.github/workflows` folder with `.yaml` extension. Example of configuration: `.github/workflows/workflow.yml`:

```yaml
on: [pull_request] # Trigger RefDiff after each pull request

jobs:
    main:
        runs-on: ubuntu-latest
        name: Refactoring Detection
        steps:
            - name: Git Checkout
              uses: actions/checkout@v2
              with:
                  fetch-depth: 0

            - name: RefDiff
              env:
                  LANGUAGE: ${{ github.event.repository.language }} # Currently, we only provide support for projects implemented in Java.
                  REV_BEFORE: ${{ github.event.pull_request.base.sha }}
                  REV_AFTER: ${{ github.event.pull_request.head.sha }}
              uses: rodrigo-brito/refactoring-aware-review@action
```

## Simple Example

We created a simple pull request with commits including toy refactorings, to illustrate the usage of our plug-in. See [here](https://github.com/rodrigo-brito/refactoring-aware-review/pull/5/commits) (but first you need to install our plug in).

## License

Distributed under the MIT License. See `LICENSE` for more information.