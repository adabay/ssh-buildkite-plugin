# SSH Buildkite Plugin

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

### Inline command example

```yml
steps:
  - command: ls
    env:
      SSH_PRIVATE_KEY: "--- PRIVATE KEY ---"
    plugins:
      - maierj/ssh#v0.9.1:
          server_address: "127.0.0.1"
          username: "admin"
          private_key_env_variable: "SSH_PRIVATE_KEY"
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

## Developing

To run the linter:

```shell
docker run -it --rm -v "${PWD}:/plugin:ro" buildkite/plugin-linter --id maierj/ssh
```