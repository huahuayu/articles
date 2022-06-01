[//title]: (tableau-server-installation-guide)
[//englishtitle]: (tableau-server-installation-guide)
[//category]: (tableau,snippet)
[//tags]: (tableau,snippet)
[//createtime]: (20220511)
[//updatetime]: (20220516)

## Official guide

https://help.tableau.com/current/server-linux/en-us/install.htm

## Before install

Make sure your hardware meets the [requirement](https://help.tableau.com/current/server-linux/en-us/requ.htm).

## Install tableau server and TSM

I wrote this script for quick install.

You need to specific few params:

- VERSION: The tableau server version. You can find the release list [here](https://www.tableau.com/support/releases/server)
- ARCH: Your linux server architecture
- DATA_DIR: Path to store tablue server data

```bash
#!/usr/bin/env bash
set -o errexit
set -o nounset
set -o pipefail

VERSION=${VERSION:-2022.1.1}
ARCH=${ARCH:-x86_64}
DATA_DIR=${DATA_DIR:-/data/tableau}
TABLEAU=tableau-server-`echo "$VERSION" | sed -r 's/\./-/g'`.$ARCH.rpm

cd ~
sudo yum update -y
if [[ ! -f $TABLEAU ]]; then
    wget https://downloads.tableau.com/esdalt/$VERSION/$TABLEAU
fi
sudo yum install $TABLEAU -y
cd /opt/tableau/tableau_server/packages/scripts.*
sudo ./initialize-tsm --accepteula --activation-service -d $DATA_DIR
```

Output:

```text
Creating 'tsmadmin' group for TSM admin authorization
Creating 'tableau' unprivileged user account
Creating environment file...
Creating directories and setting permissions...
Using '/data/tableau' as the data directory.
Adding user 'ec2-user' to group 'tableau'...
Adding user 'ec2-user' to group 'tsmadmin'...
Added. Note: These group membership changes do not take effect in shells already open. For these to take effect, log out of the shell and log back in.
Starting TSM services...
Updating repository version in Tableau Server Coordination Service.
TSM services started successfully
Use the 'tsm' command to continue setting up Tableau Server.
>> Tableau binary directory will be added to PATH for new shells. To get the
>> updated path, either start a new session, or for bash users run:
>> source /etc/profile.d/tableau_server.sh
The TSM administrative web interface (and REST API) is now available at
https://<your_host_name>:8850
You can continue the configuration and initialization of Tableau server using either the TSM command line interface,
or the web interface.
You will be prompted to authenticate, or can log in using the username 'ec2-user', with the same password you used to log into this session. You could also use any username, with its password, from the administrative group in the domain.
Done.
```

## Activate & initialize tableau

Visit `https://<your_server_ip>:8850`, ignore the https certificate warning.

Use your linux user's name and password to login TSM.

Get your tableau server key at https://customer-portal.tableau.com/s/my-keys.

Activate tableau.

![](https://cdn.liushiming.cn/img/20220511141415.png)

## Create tableau server admin account

After avtivate, visit `http://<your_server_ip>` your will get a note:

```text
Tableau Server is not completely set up.
An administrator must connect to the server via “localhost” to complete setup.
```

Execute this command to create admin user:

```bash
tabcmd initialuser --server 'localhost:80' --username '<admin-user-name>' --password '<admin-password>'
```

Output:

```text
===== redirecting to http://localhost/auth
===== Signed out
===== Creating new session
=====     Server:   http://localhost:80
=====     Username: admin
===== Connecting to the server...
===== Signing in...
===== Succeeded
```

All done, now you can visit `http://<your_server_ip>`, login to server by admin user.

![](https://cdn.liushiming.cn/img/20220511145401.png)

## Data source driver

You can find most of drivers at https://www.tableau.com/support/drivers

But some database may not in the list, e.g. clickhouse.

## Install clickhouse JDBC driver for tableau server

tutorial: https://clickhouse.com/learn/lessons/connect-tableau-to-clickhouse/

connector repo: https://github.com/analytikaplus/clickhouse-tableau-connector-jdbc

I transalated the steps into bash script:

```bash
#!/usr/bin/env bash
set -o errexit
set -o nounset
set -o pipefail

DRIVER_VERSION=0.3.2-patch7
DATA_DIR=/data/tableau
PACKAGED_CONNECTOR_VERSION=0.2.2

#step1
sudo mkdir -p /opt/tableau/tableau_driver/jdbc
cd /opt/tableau/tableau_driver/jdbc
wget https://github.com/ClickHouse/clickhouse-jdbc/releases/download/v$DRIVER_VERSION/clickhouse-jdbc-$DRIVER_VERSION-shaded.jar
sudo chmod 755 /opt/tableau/tableau_driver/jdbc/*

#step2
wget https://github.com/analytikaplus/clickhouse-tableau-connector-jdbc/releases/download/v$PACKAGED_CONNECTOR_VERSION/clickhouse_jdbc_$PACKAGED_CONNECTOR_VERSION.taco -P /tmp
cp /tmp/clickhouse_jdbc*.taco $DATA_DIR/data/tabsvc/vizqlserver/Connectors/
cp /tmp/clickhouse_jdbc*.taco $DATA_DIR/data/tabsvc/flowprocessor/Connectors/
if [[ -d $DATA_DIR/data/tabsvc/flowqueryservice/Connectors ]]; then
    cp /tmp/clickhouse_jdbc*.taco $DATA_DIR/data/tabsvc/flowqueryservice/Connectors/
fi
if [[ -d $DATA_DIR/data/tabsvc/flowminerva/Connectors ]]; then
    cp /tmp/clickhouse_jdbc*.taco $DATA_DIR/data/tabsvc/flowminerva/Connectors/
fi
sudo chown -R tableau:tableau $DATA_DIR/data/tabsvc

#step3
tsm restart
```

After restart, you can see the newly added clickhouse connector:

![](https://cdn.liushiming.cn/img/20220516093202.png)

## Uninstall tableau server

```bash
tsm licenses deactivate -k <license-key>
sudo /opt/tableau/tableau_server/packages/scripts.*/tableau-server-obliterate -a -y -y -y -l
```

## Install Clickhouse driver for tableau desktop

download driver: https://github.com/ClickHouse/clickhouse-jdbc/releases/download/v0.3.2-patch7/clickhouse-jdbc-0.3.2-patch7-shaded.jar

driver path: ~/Library/Tableau/Drivers

download connector: https://github.com/analytikaplus/clickhouse-tableau-connector-jdbc/releases

connector path: ~/Documents/My Tableau Repository/Connectors

## Install MongoDB driver for tableau desktop

For connect MongoDB to tableau, you need install `MongoDB Connector for BI` which is available as part of the MongoDB Enterprise Advanced subscription.

I haven't tried it.

## Install trino driver for tableau desktop

`Trino` is renamed from `Presto`, but tableau still use the name `Presto`, same thing.

driver: https://trino.io/docs/current/installation/jdbc.html#installing

driver path: ~/Library/Tableau/Drivers
