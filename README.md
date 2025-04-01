# Monitoring with Grafana & Prometheus on Raspberry PI

Setup Grafana and Prometheus with Docker on Raspberry PI

## Dependencies

-   [Docker](https://docker.com)
-   [Node Exporter](https://github.com/prometheus/node_exporter)

## Setup
### Pull GH repo/Docker images
```bash
git clone https://github.com/mluksic/grafana-prom-pi.git
```

### Setup

1. SSH to PI

2. Download Node Exporter 
```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.9.0/node_exporter-1.9.0.linux-arm64.tar.gz
```

3. Extract & move to bin folder
```bash
tar xfvz node_exporter-1.9.0.linux-arm64.tar.gz
cd node_exporter-1.9.0.linux-arm64.tar.gz
sudo cp node_exporter /usr/local/bin/
```

4. Create new `node_exporter` user for running systemd service
```bash
sudo useradd --no-create-home --shell /bin/false node_exporter
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
```

5. Create `systemd` service for Node Exporter
```bash
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter
Restart=always
RestartSec=3

[Install]
WantedBy=multi-user.target
```

6. Enable and start `node_exporter.service`
```bash
sudo systemctl enable node_exporter.service`
sudo systemctl start node_exporter.service`
```

7. Adjust `pi5` job target ID in `prometheus.yml` with your server IP

8. Run Docker containers
```bash
cd grafana-prom-pi
docker compose start --build -d
```

9. Visit & login to Grafana
Navigate to Grafana: http://[your pi IP]:3000

10. Check if Prometheus targets are pulling metrics
Navigate to Prometheus: http://[your pi IP]:9090/targets and check if both 'pi5' and 'prometheus' targets are up & running

11. Create Grafana dashboard for checking PI/server metrics (CPU, RAM, etc.)
1. Navigate to Grafana
2. Create `Prometheus`data source
3. Import [Node Exporter Full](https://grafana.com/grafana/dashboards/1860-node-exporter-full/) dashboard by pasting in `1860` as dashboard IP
4. Check newly created dashboard and its metrics

## Authors

👤 **Miha Luksic**
