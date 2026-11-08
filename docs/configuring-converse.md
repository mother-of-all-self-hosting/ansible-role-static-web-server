<!--
SPDX-FileCopyrightText: 2020 Aaron Raimist
SPDX-FileCopyrightText: 2020 Chris van Dijk
SPDX-FileCopyrightText: 2020 Dominik Zajac
SPDX-FileCopyrightText: 2020 Mickaël Cornière
SPDX-FileCopyrightText: 2020-2024 MDAD project contributors
SPDX-FileCopyrightText: 2020-2024 Slavi Pantaleev
SPDX-FileCopyrightText: 2022 François Darveau
SPDX-FileCopyrightText: 2022 Julian Foad
SPDX-FileCopyrightText: 2022 Warren Bailey
SPDX-FileCopyrightText: 2023 Antonis Christofides
SPDX-FileCopyrightText: 2023 Felix Stupp
SPDX-FileCopyrightText: 2023 Pierre 'McFly' Marty
SPDX-FileCopyrightText: 2024-2026 Suguru Hirahara

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Setting up Converse

This is an [Ansible](https://www.ansible.com/) role which installs [Converse](https://conversejs.org/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

Converse is a free and open-source XMPP chat client written in JavaScript which can be tightly integrated into any website.

See the project's [documentation](https://conversejs.org/docs/html/index.html) to learn what Converse does and why it might be useful to you.

>[!NOTE]
> The role is configured to build the Docker image by default, as it is not provided by the upstream project. Before proceeding, make sure that the machine which you are going to run the Ansible commands against has sufficient computing power to build it.

## Prerequisites

To use XMPP via HTTP with the client, it is necessary to use a BOSH connection manager or the WebSocket API. Note that this role does not prepare one for you. When using the service, please check whether your XMPP server supports the WebSocket API and make sure to set up the BOSH connection manager if it does not.

## Adjusting the playbook configuration

To enable Converse with this role, add the following configuration to your `vars.yml` file.

**Note**: the path should be something like `inventory/host_vars/mash.example.com/vars.yml` if you use the [MASH Ansible playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

```yaml
########################################################################
#                                                                      #
# converse                                                             #
#                                                                      #
########################################################################

converse_enabled: true

########################################################################
#                                                                      #
# /converse                                                            #
#                                                                      #
########################################################################
```

### Set the hostname

To enable Converse you need to set the hostname as well. To do so, add the following configuration to your `vars.yml` file. Make sure to replace `example.com` with your own value.

```yaml
converse_hostname: "example.com"
```

After adjusting the hostname, make sure to adjust your DNS records to point the domain to your server.

### Specify BOSH / WebSocket API

To use XMPP via HTTP with the client, it is necessary to use a BOSH connection manager or the WebSocket API.

To use a BOSH connection manager, add the following configuration to your `vars.yml` file:

```yaml
converse_config_initialize_bosh_service_url: YOUR_BOSH_SERVICE_URL_HERE
```

To use the WebSocket API instead, add the following configuration to your `vars.yml` file:

```yaml
converse_config_initialize_websocket_url: YOUR_WEBSOCKET_API_URL_HERE
```

### Enabling OMEMO encryption

By default the role enables OMEMO encryption for the chat which supports it. You can disable it by adding the following configuration to your `vars.yml` file:

```yaml
converse_config_initialize_omemo_default: false
```

### Setting themes (optional)

It is possible to set light and dark themes by adding the following configuration to your `vars.yml` file:

```yaml
converse_config_initialize_theme: LIGHT_THEME_HERE

converse_config_initialize_dark_theme: DARK_THEME_HERE
```

By default, the `classic` theme is specified to the light one, and the `dracula` to the dark one.

### Extending the configuration

There are some additional things you may wish to configure about the service.

Take a look at:

- [`defaults/main.yml`](../defaults/main.yml) for some variables that you can customize via your `vars.yml` file. You can override settings (even those that don't have dedicated playbook variables) using the `converse_config_initialize_custom_enabled` and `converse_config_initialize_custom` variables.

Refer to [this page](https://conversejs.org/docs/html/configuration.html) for available options which can be set to `converse_config_initialize_custom`.

## Installing

After configuring the playbook, run the installation command of your playbook as below:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=setup-all,start
```

If you use the MASH playbook, the shortcut commands with the [`just` program](https://github.com/mother-of-all-self-hosting/mash-playbook/blob/main/docs/just.md) are also available: `just install-all` or `just setup-all`

## Usage

After running the command for installation, Converse becomes available at the specified hostname like `https://example.com`.

## Troubleshooting

Refer to [this dedicated page](https://conversejs.org/docs/html/troubleshooting.html) on the project website for troubleshooting.

### Check the service's logs

You can find the logs in [systemd-journald](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html) by logging in to the server with SSH and running `journalctl -fu converse` (or how you/your playbook named the service, e.g. `mash-converse`).

#### Increase logging verbosity

If you want to increase the verbosity, add the following configuration to your `vars.yml` file:

```yaml
converse_config_initialize_loglevel: debug
```
