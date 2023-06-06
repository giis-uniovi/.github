# Contributing to giis-uniovi repositories

Unless stated otherwise, these policies and gidelines are applicable to any contribtuion on any repository located at https://github.com/giis-unovi.

## General policies and guidelines

- Pull requests (PR):
  - All contributions from non administrators must be done via a PR against the default branch (`main`)
  - When appropriate, create issues to discuss possible solutions for a change before start coding
  - Keep the PRs focused on a single change. 
    Use separated pull requests for changes related to functionality, formatting or refactoring
  - By default, all PRs are squashed before merged and the source branch deleted. Do not reuse feature branches.
  - If for any reason you need to include more than one commit in a PR, ensure that you have the 
    minimum set of well identified commits and state this clearly in the PR description (Squash commits at your local branch if needed)
  - If a change takes time to develop, consider using feature flags and split it into incremental PRs.
    Rebase frequently onto the default branch.
  - Although part of the documentation could be in Spanish, new documentation should be written in english, 
    as well as the titles of PRs and commits
- Coding:
  - Static analysis:
    - Every build wil run SonarQube at [sonarcloud.io](https://sonarcloud.io/organizations/giis/projects)
    - We follow a zero issue strict policy as reported by SonarQube (including smells) and zero compilation warnings
  - Test coverage:
    - Ensure that new or updated functionality is covered by new or existing tests
    - We do not enforce a minimum percentage of test coverage, but you may be requested to complete your tests before merging into the main branch
  - Coding style:
    - We don't enforce a particular code style, but contributions must follow the usual coding guidelines and common sense
    - Limit the size of lines of code and comments, a limit of 120 is a good compromise
    - Use a single blank line only to separate logical blocks of code
    - Comment your code, but avoid trivial and redundant comments
      that do not provide useful information that can be known from the names of variables, fuctions and classes
    - You can be asked to make changes regarding coding style before merging a PR

## Releases and snapshots (Java)

Releases are published to maven central when default branch is labeled by the administrators.

A snapshot for each branch is created automatically after each push.
Snapshots are published to GitHub packages with the name `<artifact>-<version>-<branch>-SNAPSHOT`

To consume a snapshot from your development environment, ensure you run maven with the `-U` option 
and declare the GitHub repository in the pom.xml, example:
```xml
  <repositories>
    <repository>
      <id>github</id>
      <url>https://maven.pkg.github.com/giis-uniovi/REPO_NAME</url>
      <snapshots>
        <enabled>true</enabled>
      </snapshots>
    </repository>
  </repositories>
```

As GitHub Packages require authentication to access the published packages, 
authentication must be included in a `settings.xml` file in your `~/.m2` folder.
The `id` (`github` in the example) must match the `id` indicated in the pom.xml file.
The GitHub token must have the `read:packages` scope:

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

If consuming the packages from other CI environments like Jenkins, you should protect the token with a credential,
then the pipeline will inject the secret:
```groovy
    withCredentials([string(credentialsId: 'credential_id', variable: 'GITHUB_TOKEN')]) {
      sh 'mvn test -U'
    }
```
