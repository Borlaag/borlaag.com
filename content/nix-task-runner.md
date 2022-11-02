+++
title = "A Nix-ified Task Runner"
date = 2022-10-30

[taxonomies]
tags = ["nix"]
+++

Nix is my preferred tool for declarative and reproducible development environments for projects. Combined
with `direnv`, all you have to do is change your directory and all depenencies are available.
You still have to remember how to work in that project or read the `README.md` file.

<!-- more -->

Something I appreciate about `nix` is its agnostic approach, but I'd like to go a step further
with convenience. An ideal workflow would be to `cd` into a project directory and run a single
command to get a list of common tasks defined by the developer(s) of the project.
People often use `Makefile`s for this, and I recently learned about [`just`](https://github.com/casey/just).

Instead of introducing a new syntax/DSL for tasks like 'run tests' or 'run integration tests',
what if we defined the tasks inside a `flake.nix` using `nix` (the language)?

```nix
{
  description = "...";

  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";
    nix-tasks.url = "github:Borlaag/nix-tasks";
  };

  outputs = inputs@{ self, nixpkgs, nix-tasks, ... }:
    let
      # an example of a task, the command can be a multi-line string
      # that is effectively just a bash script
      setup = {
        name = "setup";
        helpText = "setup the application and database";
        command = "...";
      };
      test = {
        name = "test";
        helpText = "run all unit tests for the project";
        command = "...";
      };
      testIntegration = {
        name = "test-integration";
        helpText = "run integration tests";
        command = "...";
        # having tasks depend on other tasks can be useful, and
        # something other task runners generally support
        dependsOn = [ "setup" ];
      };
    in
    {
      # mkTasks is the nix side of this story, it creates a file in
      # the nix store where each task is defined in a json file that
      # our task runner binary can parse to display the help text
      # and run the commands.
      tasks = nix-tasks.mkTasks [
        setup
        test
        testIntegration
      ];
    };
}
```

Given the above useage of this not-yet-existent `nix-tasks` library, we could now `cd` into a directory
and be dropped into a pre-configured development environment and simply run `nix-tasks` to get a list
of common tasks.

```
$ nix-tasks
Usage: nix-tasks [task]

Available Tasks:
  setup               setup the application and database
  test                run all unit tests for the project
  test-integration    run integration tests (depends on: setup)
  help                display this help text
```

Starting with something simple we could build things like flags and arguments, for running tasks such as
`nix-tasks deploy --prod` or `nix-tasks deploy --staging` etc. The only advantage here is everything is
defined in the `flake.nix` file, and that there is no additional task runner file in the repository root.

Alternatively, `nix-tasks` can just be a compiler to create a `Justfile` or a `Makefile` and write that
to the nix store. My idea was to write a separate tool that I can take some design liberties with and bake
opinionated defaults into.

## Design

There are two components to this:

- `mkTasks`, the `nix` library
- `nix-tasks`, a binary executable CLI tool

Inside of a `flake.nix` users would include the `nix-tasks` `nix` library flake in inputs,
and also add the `nix-tasks` executable in their devShell list of packages.

`mkTasks` will process the nix definitions into a `JSON` file and write it to the nix store.
This file will be easier to work with from the task runner tool.

`nix-tasks` will read this `JSON` file and create a help text which will be the default entrypoint
to the CLI. Each task has a command which if effectively just a bash script, and our CLI will execute
that script in the current shell.
