## Install Erlang
```bash
sudo wget http://packages.erlang-solutions.com/erlang-solutions_1.0_all.deb
sudo dpkg -i erlang-solutions_1.0_all.deb
sudo apt-get update
sudo apt-get -y install erlang-nox
```

## Install RabittMQ
```bash
sudo wget http://www.rabbitmq.com/releases/rabbitmq-server/v3.6.0/rabbitmq-server_3.6.0-1_all.deb
sudo dpkg -i rabbitmq-server_3.6.0-1_all.deb
```

## Configure RabittMQ for Sensu
```bash
sudo rabbitmqctl add_vhost /sensu
sudo rabbitmqctl add_user sensu secret
sudo rabbitmqctl set_permissions -p /sensu sensu ".*" ".*" ".*"
```
## Install Redis
```bash
sudo apt-get update
sudo apt-get -y install redis-server
```

## Install Sensu
```bash
sudo su
wget http://repos.sensuapp.org/apt/pubkey.gpg -O- | sudo apt-key add -
echo "deb     http://repositories.sensuapp.org/apt sensu main" | sudo tee /etc/apt/sources.list.d/sensu.list
CTRL + D

sudo apt-get update && sudo apt-get -y upgrade
sudo apt-get install -y sensu 
sudo apt-get install -y uchiwa
```

## Configure Sensu
```bash
sudo wget -O /etc/sensu/config.json http://sensuapp.org/docs/0.21/files/config.json
sudo wget -O /etc/sensu/conf.d/check_disk.json http://sensuapp.org/docs/0.21/files/check_disk.json
sudo wget -O /etc/sensu/conf.d/default_handler.json http://sensuapp.org/docs/0.21/files/default_handler.json

sudo vim /etc/sensu/conf.d/check_disk.json
```
```bash
{
  "checks": {
    "disk": {
      "command": "echo 0",
      "interval": 10,
      "subscribers": [
        "test"
      ]
    }
  }
}
```
```bash
sudo chown -R sensu:sensu /etc/sensu
```
## Config Sensu Client
```bash
sudo wget -O /etc/sensu/conf.d/client.json http://sensuapp.org/docs/0.21/files/client.json
sudo chown -R sensu:sensu /etc/sensu
```


## Config Uchiwa
```bash
sudo vim /etc/sensu/uchiwa.json
```
```bash
{
  "sensu": [
    {
      "name": "test",
      "host": "127.0.0.1",
      "port": 4567,
      "timeout": 5
    }
  ],
  "uchiwa": {
    "host": "0.0.0.0",
    "port": 3000,
    "interval": 5
  }
}
```

## Start all Services

```bash
sudo service rabbitmq-server start
sudo service sensu-server start
sudo service sensu-client start
sudo service sensu-api start
sudo service uchiwa start
```

goto: http://127.0.0.1:3000

## Check if is all Good
```bash
sudo tail -f /var/log/sensu/sensu-server.log
sudo tail -f /var/log/sensu/sensu-api.log
sudo tail -f /var/log/sensu/sensu-client.log
```
