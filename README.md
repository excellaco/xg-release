# xg (Excella Generator)

**A tool to facilitate rapid and problem-free development of solutions**

It is available for download here as a go binary.

![Image](.data/demo.gif)

## Features
* Generate code by cloning a configured remote Git repository
* Render Go templates from the source repository against supplied variables
* Generate completion scripts for Bash or Zsh

## Installation
Head over to the [Releases Page](https://github.com/excellaco/xg/releases) to download the appropriate binary for your platform.

### Windows
```shell
$ unzip xg_0.2.1_Windows_x86_64.zip xg.exe
```

### Mac OS
```shell
$ tar zxf xg_0.2.1_Darwin_x86_64.tar.gz -C /usr/local/bin xg && chmod +x /usr/local/bin/xg
```

### Linux
```bash
$ tar zxf xg_0.2.1_Linux_x86_64.tar.gz -C /usr/local/bin xg && chmod +x /usr/local/bin/xg
```

## Getting Started

### Configuration
The generator reads instructions from a configuration file in YAML format. You may either use the default name `xg.yaml` or pass your filename on the command line. The format is as follows:

```yaml
projectName: [project directory which will be created]
templateRepo:
  remote: [URL from which to clone repository]
  refName: [optional, branch to clone if not master]
  
variables:
  Key1: Value1
  Key2: Value2
  Key3: Value3
  ...
```

### Templatizing Your Repository
Any file with the extension `.xg` will be treated as a Go template and rendered against variables supplied in the configuration file. You may create these files in any way you would like, but the following workflow may be particularly helpful if you are starting with a repository of working code.

* Create a branch that will keep the "templatized" code:
  ```shell
  $ git co -b template
  ```
* Within `template`, rename any file to be converted:
  ```shell
  $ git mv Foo.java Foo.java.xg
  ```
* Within `template`, add the Go template bits to your `.xg` file
* Within `master`, continue to develop and test your normal code
* Keep the branches in sync as follows (`master` -> `template`):
  ```shell
  $ git co template
  $ git merge master
  ```

## Generating Shell Completion
Generator can generate it's own shell completion scripts. Here is how to apply those.

* Bash
  ```shell
  $ echo "source <(xg completion bash)" >>~/.bashrc
  ```
* Zsh
  ```shell
  $ xg completion zsh >/usr/local/share/zsh/site-functions/_xg
  ```

## Why Go?
* Builds a small dependency-free native binary
* Cross-compiles to Windows, Mac, and Linux
* Mature CLI libraries available

## Usage

```shell-output
Usage:
  xg [command]

Available Commands:
  completion  Generate shell completion script
  help        Help about any command
  make        Generate code from templates
  version     Show version
```

#### xg make

```shell-output
Generate code from templates

Usage:
  xg make [CONFIG_FILE]

Aliases:
  make, m
```

#### xg completion

```shell-output
Generate shell completion script

Usage:
  xg completion [command]

Aliases:
  completion, c

Available Commands:
  bash        BASH completion script
  zsh         ZSH completion script
```

## Publish

When creating a new repository from a template we want to allow the user to easily add this repository to their organization or personal github accounts, as well as associate teams within the organization to the repository. This command can both tie in to the initial `Make` command as well as be run as a standalone process after the initial make has been run.

### Publish within Make

In order to publish a repository within the `Make` command, you need to provide some additional configuration values, such as github username, access token, repo, and optionally the organization, team slug, and api url. This allows for the most flexibility as a product. Below is a snippet of the `Publish` configuration.

```yml
publish:
  github:
    username: alexm118
    accessToken: *********
    repoName: alex-test
    organization: excellaco
    teamSlug: xg
```

The yml also has support for an `apiUrl` in case you are using Github Enterprise.

### Publish as a Standalone Command

In order to make publish as flexible as possible, we opted to expose the functionality as its own command. This command supports command line arguments rather than a yaml configuration file. We can expose a yaml configuration file or a pre run hook to prompt users as an additional way of ensuring the variables necessary are provided.

Below is a snippet from `xg help publish`

```shell-output
Create and Publish a local git repository

Usage:
  xg publish [flags]

Flags:
  -a, --accesstoken string    Access Token For Github API
  -l, --apiurl string         Url of Github API
  -h, --help                  help for publish
  -o, --organization string   Organization to create repository under
  -r, --reponame string       Name of new repository
  -t, --teamslug string       Team slug to associate with repo
  -u, --username string       Github Username
```

Of the possible command line arguments only username, accesstoken and reponame are required. Below is an example execution:

`xg publish -u alexm118 -a ****** -r alex-test`
