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

# Setting up Static Web Server

This is an [Ansible](https://www.ansible.com/) role which installs [Static Web Server](https://static-web-server.net/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

Static Web Server is a web server for static file-serving.

See the project's [documentation](https://static-web-server.net/v3/) to learn what Static Web Server does and why it might be useful to you.

>[!NOTE]
> This role is configured to install [v3](https://static-web-server.net/v3/) by default.

## Adjusting the playbook configuration

To enable Static Web Server with this role, add the following configuration to your `vars.yml` file.

**Note**: the path should be something like `inventory/host_vars/mash.example.com/vars.yml` if you use the [MASH Ansible playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

```yaml
########################################################################
#                                                                      #
# static_web_server                                                    #
#                                                                      #
########################################################################

static_web_server_enabled: true

########################################################################
#                                                                      #
# /static_web_server                                                   #
#                                                                      #
########################################################################
```

### Set the hostname

To enable Static Web Server you need to set the hostname as well. To do so, add the following configuration to your `vars.yml` file. Make sure to replace `example.com` with your own value.

```yaml
static_web_server_hostname: "example.com"
```

After adjusting the hostname, make sure to adjust your DNS records to point the domain to your server.

### Mounting the root directory

To mount the root directory to overwrite the default one, add the following configuration to your `vars.yml` file (adapt to your needs):

```yaml
static_web_server_container_additional_volumes_custom:
  - type: bind
    src: /path/on/the/host
    dst: "{{ static_web_server_environment_variables_server_root }}"
    options: readonly
```

>[!NOTE]
> The directory should be mounted with `readonly` to prevent accidental modification.

### Integrating with Prometheus (optional)

Static Web Server can natively expose metrics to Prometheus. If you are looking for an integration, you can check out the MASH Ansible playbook. Please refer to the service's documentation over there for more information.

### Extending the configuration

There are some additional things you may wish to configure about the service.

Take a look at:

- [`defaults/main.yml`](../defaults/main.yml) for some variables that you can customize via your `vars.yml` file. You can override settings (even those that don't have dedicated playbook variables) using the `static_web_server_environment_variables_additional_variables` variable.

Refer to [this page](https://static-web-server.net/v3/configuration/env) for available options which can be set to `static_web_server_environment_variables_additional_variables`.

## Installing

After configuring the playbook, run the installation command of your playbook as below:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=setup-all,start
```

If you use the MASH playbook, the shortcut commands with the [`just` program](https://github.com/mother-of-all-self-hosting/mash-playbook/blob/main/docs/just.md) are also available: `just install-all` or `just setup-all`

## Usage

After running the command for installation, Static Web Server becomes available at the specified hostname like `https://example.com`.

## Troubleshooting

### Check the service's logs

You can find the logs in [systemd-journald](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html) by logging in to the server with SSH and running `journalctl -fu static-web-server` (or how you/your playbook named the service, e.g. `mash-static-web-server`).

#### Increase logging verbosity

If you want to increase the verbosity, add the following configuration to your `vars.yml` file:

```yaml
static_web_server_environment_variables_server_log_level: debug
```
