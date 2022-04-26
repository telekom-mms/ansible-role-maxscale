# maxscale

## Variables

| Name                         | Required   | Default   | Description   |   |
|------------------------------|------------|-----------|---------------|---|
| maxscale_database_user_name |no  | maxscale | Username of maxscale user |
| maxscale_database_user_password | yes | | Password for maxscale user |
| maxscale_database_user_host | no | % | Host for maxscale user |
| maxscale_database_monitor_user_name | no | maxscale_monitor_user | Username of maxscale monitor user |
| maxscale_database_monitor_user_password | yes | | Password of maxscale monitor user |
| maxscale_database_monitor_user_host | no | % | Host of maxscale monitor user |
| database_login_user_name | yes | | Username of database user which is used to create maxscale users |
| database_login_user_password | yes | | Password of database user which is used to create maxscale users |
| maxscale_install_repo_script | no | true | Use installscript provided by MariaDB to install repository. Set to false if you want to manage it by yourself. |
