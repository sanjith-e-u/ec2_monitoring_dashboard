# System Metrics Monitoring using Prometheus & Grafana

## Description
This project collects and visualizes real-time system metrics from multiple EC2 instances using Node Exporter, Prometheus, and Grafana.
Node Exporter is installed on each EC2 instance to gather system metrics such as CPU usage, memory utilization, disk usage, and running processes.
The collected data is sent to Prometheus, which stores and manages time-series metrics efficiently. Grafana then connects to Prometheus to retrieve the stored metrics and display them in interactive real-time dashboards.
This setup enables continuous system performance monitoring, helping optimize resource utilization and ensure system stability.

## Technologies Used
AWS EC2
Node exporter
Prometheus
Grafana

## Installation Guide

### Install Node Exporter on Each EC2 Instance
wget https://github.com/prometheus/node_exporter/releases/latest/download/node_exporter-linux-amd64.tar.gz
tar xvf node_exporter-linux-amd64.tar.gz
sudo mv node_exporter-*/node_exporter /usr/local/bin/

## Create a Systemd Service for Node Exporter
sudo nano /etc/systemd/system/node_exporter.service
Add the following code:
[Unit]
Description=Node Exporter
After=network.target
[Service]
User=root
ExecStart=/usr/local/bin/node_exporter
[Install]
WantedBy=default.target

### Install & Configure Prometheus
wget https://github.com/prometheus/prometheus/releases/latest/download/prometheus-linux-amd64.tar.gz
tar xvf prometheus-linux-amd64.tar.gz
sudo mv prometheus-*/prometheus /usr/local/bin/

### Create a Configuration File
sudo nano /etc/prometheus/prometheus.yml
Add the following code:
global:
  scrape_interval: 15s 

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]

  - job_name: "node_exporter"
    static_configs:
      - targets: ["INSTANCE-1-IP:9100", "INSTANCE-2-IP:9100"]

### Install & Configure Grafana
sudo apt update && sudo apt install -y grafana
sudo systemctl enable grafana-server
sudo systemctl start grafana-server

### Create dashboards
To create dashboard we must link a data scourcse. In the left we can see the data scource click on that then click on prometheus and add the url.
Then go to dashboards and click new pannel or visualization and add the following queries on different pannel to create different graphs

#### For CPU usage
100 - (avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
#### For Memory usage
(1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)) * 100
#### For Disk usage
100 - ((node_filesystem_avail_bytes{mountpoint="/"} / node_filesystem_size_bytes{mountpoint="/"}) * 100)
#### For Running processes
count(node_scrape_collector_duration_seconds)



