[//title]: (mongodb-bi-connector)
[//englishtitle]: (mongodb-bi-connector)
[//category]: (mongodb,tableau)
[//tags]: (mongodb,tableau)
[//createtime]: (20220607)
[//updatetime]: (20220607)

In order to connect mongoDB to tableau or other BI tools, you need install MongoDB BI Connector.

## Official document

https://www.mongodb.com/docs/bi-connector/current/installation/

## Prerequisites

https://www.mongodb.com/docs/bi-connector/current/tutorial/install-bi-connector-rhel/

```bash
rpm -qa | grep -i openssl
```

## Download

https://www.mongodb.com/try/download/bi-connector

## Install

```bash
tar -xvzf mongodb-bi-linux-{arch}-{platform}-{version}.tgz
sudo install -m755 bin/mongo* /usr/bin/
```

## Config

```yml
systemLog:
  logAppend: true
  path: "/data/mongosqld/logs/mongosqld.log"
  verbosity: 2
mongodb:
  net:
    uri: "<mongodb_host>:<mongodb_port>"
net:
  bindIp: 0.0.0.0
  port: 3307
```

## Start

```bash
mongosqld --config /data/mongosqld/mongosqld.conf
```

## Add to systemd (optional)

```text
sudo sh -c 'cat > /usr/lib/systemd/system/mongosqld.service <<EOF
[Unit]
Description=mongosqld service
[Service]
Type=simple
User=ec2-user
WorkingDirectory=/home/ec2-user
ExecStart=/usr/bin/mongosqld /data/mongosqld/mongosqld.conf
Restart=always
RestartSec=1
[Install]
WantedBy=multi-user.target
EOF'
sudo systemctl enable mongosqld
sudo systemctl start mongosqld
```

## Connect

After mongosqld been installed, you can connect it by mysql client, tableau, etc.

You can query mongoDB by sql.

```yaml
bindIp: 0.0.0.0
port: 3307
```
