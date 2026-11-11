<!-- BEGIN_ANSIBLE_DOCS -->

# Ansible role telekom_mms.maxscale

Installs and configures MariaDB Maxscale

## Supported Operating Systems

- Ubuntu
  - all
- Debian
  - all
- EL
  - all

## Role Variables

- `database_login_host`
  - Default: ``
  - Description: Host used for database user preparation.
  - Type: str
  - Required: no

- `database_login_socket`
  - Default: ``
  - Description: Unix socket used for database user preparation.
  - Type: str
  - Required: no

- `database_login_user_name`
  - Default: ``
  - Description: Username of database user which is used to create maxscale users and used in config for configured montitors.
  - Type: str
  - Required: yes

- `database_login_user_password`
  - Default: ``
  - Description: Password of database user which is used to create maxscale users and used in config for configured montitors.
  - Type: str
  - Required: yes

- `maxscale_admin_host`
  - Default: `127.0.0.1`
  - Description: IP to bind MariaDB MaxScale GUI
  - Type: str
  - Required: no

- `maxscale_admin_port`
  - Default: `8989`
  - Description: Port to bind MariaDB MaxScale GUI
  - Type: int
  - Required: no

- `maxscale_admin_secure_gui`
  - Default: `True`
  - Description: Change SSL usage for MariaDB MaxScale GUI. By default true but no certificates are being created. You will have to create them yourself.
  - Type: bool
  - Required: no

- `maxscale_config_file_path`
  - Default: `/etc/maxscale.cnf`
  - Description: Path and name of maxscale config. This path will also be mounted in docker container and used as main config.
  - Type: str
  - Required: no

- `maxscale_config_filter_list`
  - Default: ``
  - Description: List of filters.
  - Type: list of dicts of 'maxscale_config_filter_list' options
  - Required: no

  - Element options for `maxscale_config_filter_list[]`:

    - `matches`
      - Default: ``
      - Description: Match rules for this filter.
      - Type: list of dicts of 'matches' options
      - Required: yes

    - `module`
      - Default: `namedserverfilter`
      - Description: Filter module.
      - Type: str
      - Required: no

    - `name`
      - Default: ``
      - Description: Name of filter.
      - Type: str
      - Required: yes

  - Element options for `maxscale_config_filter_list[].matches[]`:

    - `number`
      - Default: ``
      - Description: Number of match in format 01, 02, ..., 10, 11. Maximum is 25 per filter.
      - Type: str
      - Required: yes

    - `target`
      - Default: `->master`
      - Description: Server to which matching statements should be routed, for example ->master, ->slave, or a server name.
      - Type: str
      - Required: no

    - `value`
      - Default: ``
      - Description: Pattern to match. Supports regular expressions.
      - Type: str
      - Required: yes

- `maxscale_config_listener_list`
  - Default: ``
  - Description: List of MaxScale listeners.
  - Type: list of dicts of 'maxscale_config_listener_list' options
  - Required: no

  - Element options for `maxscale_config_listener_list[]`:

    - `address`
      - Default: ``
      - Description: Address to bind listener to.
      - Type: str
      - Required: yes

    - `name`
      - Default: ``
      - Description: Name of listener (can be chosen freely).
      - Type: str
      - Required: yes

    - `port`
      - Default: ``
      - Description: Port to use for listener.
      - Type: int
      - Required: yes

    - `protocol`
      - Default: `MariaDBClient`
      - Description: Protocol to use for listener. Possible values MariaDBClient, CDC.
      - Type: str
      - Required: no

    - `service`
      - Default: ``
      - Description: Name of beforehand defined service to use.
      - Type: str
      - Required: yes

- `maxscale_config_monitor_list`
  - Default: ``
  - Description: List of maxscale monitors.
  - Type: list of dicts of 'maxscale_config_monitor_list' options
  - Required: no

  - Element options for `maxscale_config_monitor_list[]`:

    - `module`
      - Default: ``
      - Description: Possible values galeramon, mariadbmon.
      - Type: str
      - Required: yes

    - `monitor_interval`
      - Default: `2000ms`
      - Description: Interval in which the monitor checks the servers.
      - Type: str
      - Required: no

    - `name`
      - Default: ``
      - Description: Name of monitor (can be chosen freely).
      - Type: str
      - Required: yes

    - `servers`
      - Default: ``
      - Description: Comma separated list of servers which maxscale should include in configured monitor.
      - Type: str
      - Required: yes

- `maxscale_config_server_list`
  - Default: ``
  - Description: List of servers which maxscale should proxy to.
  - Type: list of dicts of 'maxscale_config_server_list' options
  - Required: no

  - Element options for `maxscale_config_server_list[]`:

    - `address`
      - Default: ``
      - Description: Address or FQDN of the database server.
      - Type: str
      - Required: yes

    - `name`
      - Default: ``
      - Description: Name of the database server (can be chosen freely).
      - Type: str
      - Required: yes

    - `port`
      - Default: ``
      - Description: Port of the database server.
      - Type: int
      - Required: yes

    - `protocol`
      - Default: `MariaDBBackend`
      - Description: Protocol to use for server connection. Currently no other protocols are supported.
      - Type: str
      - Required: no

- `maxscale_config_service_list`
  - Default: ``
  - Description: List of maxscale services.
  - Type: list of dicts of 'maxscale_config_service_list' options
  - Required: no

  - Element options for `maxscale_config_service_list[]`:

    - `name`
      - Default: ``
      - Description: Name of service (can be chosen freely).
      - Type: str
      - Required: yes

    - `router`
      - Default: ``
      - Description: Possible values readwritesplit, readconnroute.
      - Type: str
      - Required: yes

    - `router_options`
      - Default: ``
      - Description: Options for router. Values master, slave.
      - Type: str
      - Required: no

    - `servers`
      - Default: ``
      - Description: Comma separated list of servers which maxscale should include in configured service.
      - Type: str
      - Required: yes

- `maxscale_config_use_sql_variables_in`
  - Default: `all`
  - Description: Queries which read session variables will be routed to. Possible values are master, all.
  - Type: str
  - Required: no

- `maxscale_database_monitor_user_host`
  - Default: `%`
  - Description: Host of maxscale monitor user
  - Type: str
  - Required: no

- `maxscale_database_monitor_user_name`
  - Default: `maxscale_monitor_user`
  - Description: Name of maxscale monitor user
  - Type: str
  - Required: no

- `maxscale_database_monitor_user_password`
  - Default: ``
  - Description: Password of maxscale monitor user
  - Type: str
  - Required: yes

- `maxscale_database_user_host`
  - Default: `%`
  - Description: Host for maxscale user
  - Type: str
  - Required: no

- `maxscale_database_user_name`
  - Default: `maxscale`
  - Description: Name of maxscale user
  - Type: str
  - Required: no

- `maxscale_database_user_password`
  - Default: ``
  - Description: Password for maxscale user
  - Type: str
  - Required: yes

- `maxscale_docker_pull_image`
  - Default: `False`
  - Description: Set policy for image pulling when image is already present. True for always, false for never.
  - Type: bool
  - Required: no

- `maxscale_docker_recreate`
  - Default: `False`
  - Description: Force recreation of existing container.
  - Type: bool
  - Required: no

- `maxscale_docker_restart`
  - Default: `False`
  - Description: Restart container even if no changes happened.
  - Type: bool
  - Required: no

- `maxscale_docker_state`
  - Default: `started`
  - Description: Set state of container (started, stopped, absent, present).
  - Type: str
  - Required: no

- `maxscale_docker_version`
  - Default: `latest`
  - Description: Set container version to use.
  - Type: str
  - Required: no

- `maxscale_install_repo_script`
  - Default: `True`
  - Description: Use installscript provided by MariaDB to install repository if not using docker. Set to false if you want to manage it by yourself.
  - Type: bool
  - Required: no

- `maxscale_use_docker`
  - Default: `True`
  - Description: Install docker, download latest maxscale container and start it with your configuration.
  - Type: bool
  - Required: no

- `maxscale_use_mariadb`
  - Default: `False`
  - Description: Use MariaDB modules to prepare the MaxScale database users.
  - Type: bool
  - Required: no


## Dependencies

None.

## Requirements

The role requires the following Ansible collections:

```yaml
---
collections:
  - name: ansible.mariadb
  - name: ansible.mysql
  - name: ansible.posix

```

```bash
ansible-galaxy collection install -r requirements.yml
```

## Example Playbook

```
- hosts: all
  roles:
    - telekom_mms.maxscale
```

## Usage

```
---
maxscale_use_docker: false
maxscale_use_mariadb: true
maxscale_database_user_password: your_password_here
maxscale_database_monitor_user_password: your_monitor_password_here
maxscale_config_server_list:
  - name: db01
    address: 0.0.0.0
    port: 3306
maxscale_config_monitor_list:
  - name: Monitor
    module: mariadbmon
    servers: db01
maxscale_config_service_list:
  - name: Splitter-Service
    router: readwritesplit
    servers: db01
maxscale_config_listener_list:
  - name: Splitter-Listener
    service: Splitter-Service
    protocol: MariaDBClient
    address: 0.0.0.0
    port: 3307
database_login_host: db01
database_login_user_name: root
database_login_user_password:
database_login_socket: /run/mysqld/mysqld.sock
```

## Author Information

- Andreas Hering

## License

GPL-3.0-only

<!-- END_ANSIBLE_DOCS -->
