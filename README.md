# Project - Visualization
Description : This project is for monitoring the metrics from the server created on GCP and compare the metrics when two same programs in different languages are running on the server.
- Monitoring helps us to understand the dependencies of whole system in a deep and easy way.
Requirements: 
Server :
  - e2-small (2 vCPUs, 2 GB memory) instance in GCP
  - CentOS 
Database - InfluxDB
Visualization tool : Grafana
Server Agent : Telegraf

Step 1:
Installing InfluxDB on the CentOS server
- InfluxDB uses 8086 TCP port by default.
- InfluDB can be installed on CentOS using yum package manager
- InfluDB repository can be added to yum configuration by the following commands
    cat <<EOF | sudo tee /etc/yum.repos.d/influxdb.repo
    [influxdb]
    name = InfluxDB Repository - RHEL \$releasever
    baseurl = https://repos.influxdata.com/rhel/\$releasever/\$basearch/stable
    enabled = 1
    gpgcheck = 1
    gpgkey = https://repos.influxdata.com/influxdb.key
    EOF
- Now install and start InfluxDB service by the following commands
    sudo yum install influxdb
    sudo service influxdb start    (or if OS using systemd) sudo systemctl start influxdb
- launch InfluxDB with config file (Set the environment variable INFLUXDB_CONFIG_PATH to the path of   your configuration file and start the process.)by the following commands
    echo $INFLUXDB_CONFIG_PATH
    /etc/influxdb/influxdb.conf
- check if influxDB is started
    systemctl start influxDB
    systemctl status influxDB
- see if the ports are open
    sudo netstat -naptu | grep LISTEN | grep influxDB
- uncomment the settings in the configuration files depending on the needs
    vim /etc/influxdb/influxdb.conf
- start influx client and create admin user and a guest user
    influx
    CREATE USER "..."  WITH PASSWORD '.....' WITH ALL PRIVILAGES
- restart influxdb and start influx client and authenticate the user and password to show users and     create a databade
    service influxdb restart
    influx
    auth <user> <password>
    show users
    show database
    create database <database_name>
    exit
 Step 2:
 - we use this documentation for installing grafana on to the gcp instance                                 -  https://grafana.com/docs/grafana/latest/installation/rpm/
- go to the grafana portal using the external ip of the server and port 3000(default grafana port)
- create a datasource influxdb using the user authentication details we set previously during step 1
- creating a dashboard (prerequiste - telegraf agent from influx framework for collecting metrics       from the server and sending them to grafana)
Step 3:
 - First we install the telegraf on the windows OS and see how it works
 - documentation for installing telgraf (windows 64 bit)
    https://portal.influxdata.com/downloads/
 - download in windows and copy it to program files 
 - install the service - C:\"Program Files"\Telegraf\telegraf.exe --service install
 - ->Computer management -> services and applications -> services ->Telegraf ( start the service )
 - Edit the config file according to use
 - restart the service.
 - now go to garfana add new dashboard and a new panel and selelect the from as host and tag the        windows and observe the metrics of windows through grafana  .
 - Now we will do the same process with the CentOS ec2 instance instead of windows
 - Run same programs in java and C languages and record the metrics and compare the metrics and by      visualizing in grafana.

INFLUXDB : InfluxDB is an open-source time series database (TSDB) developed by InfluxData. It is written in Go and optimized for fast, high-availability storage and retrieval of time series data in fields such as operations monitoring, application metrics, Internet of Things sensor data, and real-time analytics.
Grafana : Grafana is a multi-platform open source analytics and interactive visualization web application. It provides charts, graphs, and alerts for the web when connected to supported data sources.
Telegraf : 
Telegraf is a plugin-driven server agent for collecting & reporting metrics, and is the first piece of the TICK stack. Telegraf has plugins to source a variety of metrics directly from the system it’s running on, pull metrics from third party APIs, or even listen for metrics via a statsd and Kafka consumer services. It also has output plugins to send metrics to a variety of other datastores, services, and message queues, including InfluxDB, Graphite, OpenTSDB, Datadog, Librato, Kafka, MQTT, NSQ, and many others.
