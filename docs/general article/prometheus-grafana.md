# Prometheus and Grafana for monitering 

## Install Prometheus

Download prometheus binaries from [Download page](https://github.com/prometheus/prometheus/releases)

#### Setup Prometheus Binaries

```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.44.0/prometheus-2.44.0.linux-amd64.tar.gz
tar -xvf prometheus-2.44.0.linux-amd64.tar.gz
```

```bash
useradd --no-create-home --shell /bin/false prometheus
mkdir /etc/prometheus
mkdir /var/lib/prometheus
```

```bash

cp prometheus-2.44.0.linux-amd64/prometheus prometheus-2.44.0.linux-amd64/promtool /usr/local/bin/
cp -r prometheus-2.44.0.linux-amd64/consoles /etc/prometheus/ 
cp -r prometheus-2.44.0.linux-amd64/console_libraries/ /etc/prometheus/
cp prometheus.yml /etc/prometheus/
```

```bash
chown prometheus:prometheus /etc/prometheus
chown prometheus:prometheus /var/lib/prometheus
```

#### Setup Prometheus Configuration

All the prometheus configurations should be present in /etc/prometheus/prometheus.yml file.

Create the prometheus.yml file.

```bash
vi /etc/prometheus/prometheus.yml
```

Copy the following contents to the prometheus.yml file.

```yaml
global:
  scrape_interval: 10s

scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['localhost:9090']
```

Change the ownership of the file to prometheus user.

```bash
chown prometheus:prometheus /etc/prometheus/prometheus.yml
```

#### Setup Prometheus Service File

Create a prometheus service file

```bash
vi /etc/systemd/system/prometheus.service
```

Copy the following content to the file

```bash
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

Reload the systemd service to register the prometheus service and start the prometheus service

```bash
systemctl daemon-reload
systemctl start prometheus

systemctl status prometheus
```

```bash
firewall-cmd --add-port 9090/udp --permanent
firewall-cmd --add-port 9090/tcp --permanent
firewall-cmd --reload
```

#### Access Prometheus Web UI

Now you will be able to access the prometheus UI on 9090 port of the prometheus server.

```bash
http://<prometheus-ip>:9090/graph
```

## Install Node Exportor

#### Setup Node Exporter Binary

Download the latest node exporter package. [Download page](https://github.com/prometheus/node_exporter/releases/tag/v1.6.0)

```bash
cd /tmp
wget https://github.com/prometheus/node_exporter/releases/download/v1.6.0/node_exporter-1.6.0.linux-amd64.tar.gz
```

Unpack the tarball

```bash
tar -xvf node_exporter-0.18.1.linux-amd64.tar.gz
```

Move the node export binary to /usr/local/bin

```bash
sudo mv node_exporter-0.18.1.linux-amd64/node_exporter /usr/local/bin/
```

#### Create a Custom Node Exporter Service

Create a node_exporter user to run the node exporter service.

```bash
sudo useradd -rs /bin/false node_exporter
```

Create a node_exporter service file under systemd.

```bash
vi /etc/systemd/system/node_exporter.service
```

Add the following service file content to the service file and save it.

```yaml
[Unit]
Description=Node Exporter
After=network.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```

Reload the system daemon and star the node exporter service.

```bash
systemctl daemon-reload
systemctl start node_exporter
```

check the node exporter status to make sure it is running in the active state.

```bash
systemctl status node_exporter
```

Enable the node exporter service to the system startup.

```bash
systemctl enable node_exporter
```

Now, node exporter would be exporting metrics on port 9100. 

You can see all the server metrics by visiting your server URL on /metrics as shown below.

http://<server-IP>:9100/metrics

## Install Grafana

#### Download & install Grafana binary

```bash
yum install -y https://dl.grafana.com/oss/release/grafana-9.5.2-1.x86_64.rpm
```

Run the following to add the above service unit and start grafana.

```bash
systemctl daemon-reload
systemctl start grafana-server
systemctl status grafana-server
systemctl enable grafana-server.service
systemctl status grafana-server
```

#### Grafana access

Let’s open port 3000 to access the UI @
http://<public-ip-address>:3000/login

The default user & password is ‘admin’ & ‘admin’.


## Visualize Prometheus Metrics In Grafana

