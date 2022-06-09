# Execute Private Action

An experimental GitHub Actions proof of concept where a public composite action loads a private composite action. Existing solutions, [nick-fields/private-action-loader](https://github.com/nick-fields/private-action-loader) and [bagbyte/use-private-action](https://github.com/bagbyte/use-private-action/) have the following limitations:

- They only use PAT (personal access tokens). Instead, a better solution is to use deploy keys, which prevent the workflow from having overreaching access to everything the owner of the token has access to.
- They're limited to only JavaScript actions.
- node_modules must be committed. GitHub recommends using a bundler instead.


The solution in this repo fails for the following reasons:

- The `with` arguments cannot be passed to the private action since yaml isn't a programming language, and there is no bash-level GitHub SDK to execute actions.
- The working-directory cannot be set, which may create problems for actions that don't use the GITHUB_ACTION_PATH environment variable.


This solution succeeds in using deploy keys in place of personal access tokens. However, without converting it to a JavaScript action, it cannot solve the above problems at this time. Instead, the other two solutions can be adapted to solve the security issues as well as running composite actions. However, this would involve writing another implementation of the GitHub Actions yaml. The simplest solution would be to allow one `run` block and then dynamically write all of the bash commands to a shell script and execute with `@actions/exec`. [uraimo/run-on-arch-action](https://github.com/uraimo/run-on-arch-action/), which takes shell commands passed to a `run` block and runs them in cross-architecture Docker containers, has a great example of how to pull shell commands from yaml and execute them in a shell script in this manner.

