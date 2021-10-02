# Thycotic Devops Secret Vault Buildkite Plugin


This plugin is a modified version of https://github.com/adabay/vault-key-value-buildkite-plugin

This plugin enables pipelines to use secrets from a Thycotic Devops Secret Vault as environment variables.

Pipeline steps can define multiple secrets that should be provided as environment variables to the subsequent hooks.
Secrets have to be specified by their path and their key.

To authorize the access to Thycotic DSV:
* Thycotic will need to be pre authenticated in order to use this plugin, the plugin does not do any authentication.

## Example

### Single secret example

```yml
steps:
  - command: 'curl -H "Authorization: Bearer $API_ACCESS_TOKEN" https://api.example.com'
    plugins:
      - rmatulis/thycotic-dsv:
          secret_path: "static/api_access_token"
          secret_key: "token"
          exported_env_variable_name: "API_ACCESS_TOKEN"
```

### Multiple secrets example

```yml
steps:
  - command: 'echo $DOCKER_PASSWORD | docker login --username $DOCKER_USERNAME --password-stdin'
    plugins:
      - rmatulis/thycotic-dsv:
          secrets:
          - secret_path: "static/docker/registry1"
            secret_key: "username"
            exported_env_variable_name: "DOCKER_USERNAME"
          - secret_path: "static/docker/registry1"
            secret_key: "password"
            exported_env_variable_name: "DOCKER_PASSWORD"
```

## Configuration

### `secrets` (array)

If you want to export multiple secrets, you can use this array. Each entry in this array has to have the configuration properties that are listed below.

### `secret_path` (string)

This parameter defines the path of a secret. If you only want to export a single secret, you can specify this parameter at the top-level of the plugin configuration (See single secret example). Otherwise, specify it for each entry in the `secrets` array.

### `secret_key` (string)

Since there can be multiple key/value entries in a single secret, you have to specify the key of the entry that you want to export. This is done using this configuration parameter. If you only want to export a single secret, you can specify this parameter at the top-level of the plugin configuration (See single secret example). Otherwise, specify it for each entry in the `secrets` array.

### `exported_env_variable_name` (string)

With this parameter you can define the name of the environment variable that you want to set to the value of the secret entry. If you only want to export a single secret, you can specify this parameter at the top-level of the plugin configuration (See single secret example). Otherwise, specify it for each entry in the `secrets` array.

## Developing

To run the linter:

```shell
docker run -it --rm -v "${PWD}:/plugin:ro" buildkite/plugin-linter --id rmatulis/thycotic-dsv
```