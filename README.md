# SSH Buildkite Plugin

|Linting|Tests|
|---|---|
|[![Build status](https://badge.buildkite.com/d3f6052ddfdb7e2e2000cd12ae3a92ba99b5d87af70aa46ff5.svg?branch=master)](https://buildkite.com/adabay/internal-ssh-buildkite-plugin-linting)|-|

This plugin enables pipelines to execute commands or scripts on remote servers via SSH.

To define the commands or scripts that should be executed via SSH, you can configure the commands in your pipeline as if you wanted to execute them on the agent.
The plugin overrides the command hook, parses the commands from the pipeline file and executes them on the remote server.
The source of a pipeline command can be a command or executable file on the server as well as a command or executable file on the agent.
If a command or executable file is available on the server as well as on the agent, the one on the server will be preferred.
The option to define scripts and commands that are located on the agent to be executed on the server enables even more reuse of scripts defined by library plugins.

The plugin tries its best to detect errors in the configuration before executing any of the defined commands. 
The plugin verifies every command and script for its existence on the server and the agent.
Also, it sends a test command via SSH to verify that the connection properties are valid.

## Example

### Single inline command example

```yml
steps:
  - command: 'echo "Hello World"'
    env:
      SSH_PRIVATE_KEY: "--- PRIVATE KEY ---"
    plugins:
      - adabay/ssh#v0.9.3:
          server_address: "127.0.0.1"
          username: "admin"
          private_key_env_variable: "SSH_PRIVATE_KEY"
```

### Multiple inline commands example

```yml
steps:
  - command:
      - 'echo "Hello World"'
      - 'echo "Hello World 2"'
    env:
      SSH_PRIVATE_KEY: "--- PRIVATE KEY ---"
    plugins:
      - adabay/ssh#v0.9.3:
          server_address: "127.0.0.1"
          username: "admin"
          private_key_env_variable: "SSH_PRIVATE_KEY"
```

### Agent script example

In this example, `.buildkite/scripts/hello-world` is the path to a script on the agent.

```yml
steps:
  - command: '.buildkite/scripts/hello-world'
    env:
      SSH_PRIVATE_KEY: "--- PRIVATE KEY ---"
    plugins:
      - adabay/ssh#v0.9.3:
          server_address: "127.0.0.1"
          username: "admin"
          private_key_env_variable: "SSH_PRIVATE_KEY"
```

### Server script example

In this example, `/opt/scripts/hello-world` is the path to a script on the server.

```yml
steps:
  - command: '/opt/scripts/hello-world'
    env:
      SSH_PRIVATE_KEY: "--- PRIVATE KEY ---"
    plugins:
      - adabay/ssh#v0.9.3:
          server_address: "127.0.0.1"
          username: "admin"
          private_key_env_variable: "SSH_PRIVATE_KEY"
```

### Buildkite library plugin example

In this example, the `clean-directory` command will be resolved to the script path that belongs to the library plugin `adabay/utilities`.

```yml
steps:
  - command: 'clean-directory /var/www/html'
    env:
      SSH_PRIVATE_KEY: "--- PRIVATE KEY ---"
    plugins:
      - adabay/utilities#master: ~
      - adabay/ssh#v0.9.3:
          server_address: "127.0.0.1"
          username: "admin"
          private_key_env_variable: "SSH_PRIVATE_KEY"
```

### Environment variable injection example

In this example, `.buildkite/scripts/hello-world` is the path to a script on the agent, and the script depends on an environment variable called `TEST`.
The environment variable `TEST` from the client is injected into the SSH session, so it's available when the script is executed on the remote server.

```yml
steps:
  - command: '.buildkite/scripts/hello-world'
    env:
      SSH_PRIVATE_KEY: "--- PRIVATE KEY ---"
      TEST: "test"
    plugins:
      - adabay/utilities#master: ~
      - adabay/ssh#v0.9.3:
          server_address: "127.0.0.1"
          username: "admin"
          private_key_env_variable: "SSH_PRIVATE_KEY"
          injected_env_variables:
            - TEST
```

## Configuration

### `server_address` (Required, string)

The ip address or FQDN of the server where the commands should be executed.

### `username` (Required, string)

The username that should be used for the SSH connection.

### `private_key_env_variable` (Required, string)

The name of the environment variable that contains the private key which should be used for authentication with the server.

### `port` (Optional, string)

The server port which should be used for the SSH connection. Defaults to "22".

### `injected_env_variables` (Optional, array)

A list of names of environment variables that should be injected into the SSH session.

## Developing

To run the linter:

```shell
docker run -it --rm -v "${PWD}:/plugin:ro" buildkite/plugin-linter --id adabay/ssh
```
