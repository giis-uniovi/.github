# Contributing to giis-uniovi repositories

Unless otherwise stated, these policies and guidelines are applicable to any contribution on any repository located at https://github.com/giis-uniovi.

## General policies and guidelines

- Pull requests (PR):
  - All contributions from non administrators must be done via a PR against the default branch (`main`).
  - By default, clearly and concisely written issues should be created to discuss potential changes before coding begins
  - Always keep the PRs focused on a single change. 
    Use separated pull requests for changes related to functionality, formatting or refactoring.
  - Do not force-push the feature branch during revision to make easier to track the changes.
    In general, limit to the minimum posible the number of force-pushes that are made on a branch that has an associated pull request.
  - By default, all PRs are squashed before merged and the source branch deleted to keep a linear history. Do not reuse feature branches.
  - If for any reason you need to include more than one commit in a PR,
    ensure the PR includes a small set of well identified commits (Squash commits at your local branch if needed).
    State clearly in the PR description if you do not want your commits be squashed.
  - If a change takes time to develop, consider using feature flags and split it into incremental PRs.
    Rebase frequently onto the default branch.
  - Although part of the documentation could be in Spanish, new documentation should be written in English, 
    as well as the titles of PRs and commits.
  - Note that when running from a fork, the publish and sonarqube jobs may fail or be skipped.
    An administrator will run sonarqube job before merging your PR.
- Multiple pull requests for dependent repositories (e.g. Given a set of changes that require creating PRs in two repositories A and B, where A depends on B)
  - Use branches with the same name in each repository.
  - Set A to reference the branch snapshot of B.
  - When finish, you can submit A and B PRs at the same time without wainting for a merge or release of B.
  - Clearly indicate in the PR that A is still referencing a branch snapshot of B.
  - After the review, the administrator will merge (and release if required) B, update A to reference the main snapshot (or the release) and finally merge A.
- Coding:
  - Static analysis:
    - Every build will be checked by SonarQube at [sonarcloud.io](https://sonarcloud.io/organizations/giis/projects).
    - We enforce a zero issue strict policy as reported by SonarQube (including smells) and zero compilation warnings.
  - Test coverage:
    - Ensure that new or updated functionality is covered by new or existing tests.
    - We do not enforce a minimum percentage of test coverage, but you may be requested to complete your tests before merging into the main branch.
  - Coding style:
    - We do not enforce a particular code style, but contributions must follow the usual coding guidelines and common sense.
      You can be asked to make changes regarding coding style before merging a PR.
    - Use clear and concise comments in your code. Avoid trivial and redundant comments
      that do not provide additional useful information.
    - Limit the size of lines of code and comments, a limit of 120 characters is a good compromise.
    - Use a single blank line only to separate logical blocks of code.
- New repositories:
  - Ask the organization adminstrator(s) to create a new repository
    (usually created from [samples-giis-template](https://github.com/giis-uniovi/samples-giis-template).
  - Next you will be requested to customize the template and populate your repo.

## Releases and snapshots (Java)

Releases are published to maven central when the default branch is labeled with the version by the repository administrator(s).

A snapshot for each branch is created automatically after each push.
Snapshots are published to GitHub packages with the name `<artifact>-<version>-<branch>-SNAPSHOT`

Remember to use the maven `-U` option 
to update snapshots
and declare the GitHub repository in the pom.xml, e.g.
```xml
  <repositories>
    <repository>
      <id>github</id>
      <url>https://maven.pkg.github.com/giis-uniovi/*</url>
      <snapshots>
        <enabled>true</enabled>
      </snapshots>
    </repository>
  </repositories>
```

As GitHub Packages require authentication to access the published packages, 
you must use a GitHub token with the `read:packages` scope as indicated below.

### Consume a snapshot from your development environment

The GitHub token used for
authentication must be included in a `settings.xml` file in your `~/.m2` folder.
The `id` (`github` in the example) must match the `id` indicated in the pom.xml file.

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                      http://maven.apache.org/xsd/settings-1.0.0.xsd">
  <servers>
    <server>
      <id>github</id>
      <username>USERNAME</username>
      <password>TOKEN</password>
    </server>
  </servers>
</settings>
```

### Consume a snapshot from a CI environment

On Jenkins, you should protect the token with a credential,
then the pipeline will inject the secret:
```groovy
    withCredentials([string(credentialsId: 'credential_id', variable: 'GITHUB_TOKEN')]) {
      sh 'mvn test -U'
    }
```

On GitHub Actions, the github token must be passed explicity to the step or job:
```
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
