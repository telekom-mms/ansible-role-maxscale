ansible-role-maxscale
=========

This role installs, configures and starts Maxscale at one or more servers. It is possible to have it started as a docker container (default) or directly run on specified servers.

Please note that currently no logic for clustering maxscale is implemented. You can read more here if you want to do that: https://mariadb.com/resources/blog/mariadb-maxscale-2-5-cooperative-monitoring/
This means that it is possible that multiple maxscale hosts could define different masters when using it in conjunction with Galera Cluster.

It is also heavily based on configurations for MariaDB.

Requirements
------------

- ansible [community.mysql](https://docs.ansible.com/ansible/latest/collections/community/mysql/index.html#plugins-in-community-mysql) module
- The default "deployment" behaviour is using Docker. Installation is not done in this role.

If you want external access to the admin REST GUI you need to overwrite the default bind address and configure your firewall if active.

Role Variables
--------------

| Name | Required | Default | Description |
|---|---|---|---|
| **general config parameters** |  |  |  |
| database_login_user_name | yes |  | Username of database user which is used to create maxscale users and used in config for configured montitors. |
| database_login_user_password | yes |  | Password of database user which is used to create maxscale users and used in config for configured montitors. |
| database_login_host |  |  |  |
| database_login_socket |  |  |  |
| **maxscale config** |  |  |  |
| maxscale_database_user_name | no | maxscale | Name of maxscale user |
| maxscale_database_user_password | yes |  | Password for maxscale user |
| maxscale_database_user_host | no | % | Host for maxscale user |
| maxscale_database_monitor_user_name | no | maxscale_monitor_user | Name of maxscale monitor user |
| maxscale_database_monitor_user_password | yes |  | Password of maxscale monitor user |
| maxscale_database_monitor_user_host | no | % | Host of maxscale monitor user |
| maxscale_use_docker | no | true | Install docker, download latest maxscale container and start it with your configuration. |
| maxscale_docker_version | no | latest | Set container version to use.
| maxscale_docker_state | no | started | Set state of container: (started, stopped, absent, present).
| maxscale_docker_restart | no | false | Restart container even if no changes happened.
| maxscale_docker_pull_image | no | false | Set policy for image pulling when image is already present. True for always, false for never.
| maxscale_docker_recreate | no | false | Force recreation of existing container.

| maxscale_install_repo_script | no | true | Use installscript provided by MariaDB to install repository if not using docker. Set to false if you want to manage it by yourself. |
| maxscale_config_file_path | no | /etc/maxscale.cnf | Path and name of maxscale config. This path will also be mounted in docker container and used as main config. |
| maxscale_admin_host | no | 127.0.0.1 | IP to bind MariaDB MaxScale GUI |
| maxscale_admin_port | no | 8989 | Port to bind MariaDB MaxScale GUI |
| maxscale_admin_secure_gui | no | true | Change SSL usage for MariaDB MaxScale GUI. By default true but no certificates are beeing created. You will have to [do it yourself](https://mariadb.com/resources/blog/getting-started-with-the-mariadb-maxscale-gui/) (Search for "Create Self Signed Certificate for MaxScale Rest API") |
| *maxscale_config_server_list* |  |  | List of servers which maxscale should proxy to |
| &ensp;name | yes |  | Name of the database server (can be chosen freely) |
| &ensp;address | yes |  | Adress or FQDN of the database server |
| &ensp;port | yes |  | Port of the database server |
| &ensp;protocol | no | MariaDBBackend | Protocol to use for server connection. [**Currently no other protocols are supported**](https://mariadb.com/kb/en/mariadb-maxscale-22-mariadb-maxscale-configuration-usage-scenarios/#protocol). |
| *maxscale_config_monitor_list* |  |  | List of maxscale monitors |
| &ensp;name | yes |  | Name of monitor (can be chosen freely) |
| &ensp;module | yes |  | Possible values *galeramon*, *mariadbmon*. |
| &ensp;servers | yes |  | Comma seperated list of servers which maxscale should include in configured monitor. |
| &ensp;monitor_interval | no | 2000ms | Interval in which the monitor checks the servers |
| *maxscale_config_service_list* |  |  | List of maxscale services |
| &ensp;name | yes |  | Name of service (can be chosen freely) |
| &ensp;router | yes |  | Possible values: *readwritesplit*, *readconnroute*. In connection with router_options it is possible to create 2 services which take care of a read and write split if readwritesplit router is not used. |
| &ensp;router_options | no |  | Options for router. Values: *master*, *slave* |
| &ensp;servers | yes |  | Comma seperated list of servers which maxscale should include in configured service. |
| &ensp;use_sql_variables_in | no | all | Queries, which read session variables will be routed to. Possible values are *master*, *all*. [More Information](https://mariadb.com/kb/en/mariadb-maxscale-21-readwritesplit/#use_sql_variables_in) |
| *maxscale_config_listener_list* |  |  |  |
| &ensp;name | yes |  | Name of listener (can be chosen freely) |
| &ensp;service | yes |  | Name of beforehand defined service to use for listener. (eg. My-Splitter-Service, My-Read-Service) |
| &ensp;port | yes |  | Port to use for listener |
| &ensp;address | yes |  | Address to bind listener to |
| &ensp;protocol | no | MariaDBClient | Protocol to use for listener. Possible values: [*MariaDBClient*](https://mariadb.com/kb/en/mariadb-maxscale-6-mariadb-maxscale-configuration-guide/#mariadbclient), [*CDC*](https://mariadb.com/kb/en/mariadb-maxscale-6-change-data-capture-cdc-protocol/) |


Dependencies
------------

- https://galaxy.ansible.com/community/mysql

Example Playbook
----------------

    ---
    - hosts:
        - maxscale-host1
        - maxscale-host2
      roles:
        - ansible-role-maxscale
      vars:
        # used for user creation
        database_login_user_name: my_privileged_db_user
        database_login_user_password: "super_secure_and_vaulted_password"
        database_login_host: db_server1
        database_login_socket: "/var/lib/mysql/system/mysql.sock"
        # actual maxscale stuff
        maxscale_database_user_password: "super_secure_and_vaulted_password"
        maxscale_database_monitor_user_password: "super_secure_and_vaulted_password"
        maxscale_database_user_name: my_maxscale_user
        maxscale_database_user_host: "127.0.0.1"
        maxscale_database_monitor_user_name: my_maxscale_monitor_user
        maxscale_database_monitor_user_host: "127.0.0.1"
        maxscale_admin_host: 172.25.2.3

        # list of servers which maxscale should proxy to
        maxscale_config_server_list:
          - name: db-server1
            address: 172.25.2.40
            port: 3306
          - name: db-server2
            address: 172.25.2.41
            port: 3306
          - name: db-server3
            address: 172.25.2.42
            port: 3306

        # monitor list for maxscale
        maxscale_config_monitor_list:
          - name: Galera-Monitor
            module: galeramon
            servers: "db-server1, db-server2, db-server3"

        # service list for maxscale
        maxscale_config_service_list:
          - name: My-Splitter-Service
            router: readwritesplit
            servers: "db-server1, db-server2, db-server3"

        # listener list for maxscale
        maxscale_config_listener_list:
          - name: My-Splitter-Listener
            service: Splitter-Service
            port: 3306
            address: 172.25.2.3

License
-------

GPLv3

Author Information
------------------

- Andreas Hering
