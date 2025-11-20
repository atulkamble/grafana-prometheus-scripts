# 🚀 **Grafana & Prometheus – Commands & Scripts (Basic → Advanced)**

Well-organized into **Levels**, **Topics**, and **Command Blocks**.

---

# 🎯 **1. PROMETHEUS COMMANDS & SCRIPTS**

---

# ✅ **1.1 VERY BASIC PROMETHEUS COMMANDS**

### **Check Prometheus Version**

```bash
prometheus --version
```

### **Start Prometheus (default config)**

```bash
./prometheus
```

### **Start with custom config**

```bash
./prometheus --config.file=prometheus.yml
```

### **Check syntax of Prometheus config**

```bash
promtool check config prometheus.yml
```

### **Check Prometheus rules**

```bash
promtool check rules rules.yml
```

### **Validate Alert Rules**

```bash
promtool check rules alert-rules.yml
```

---

# 🟦 **1.2 BASIC PROMETHEUS SCRIPTS**

### **Install Prometheus on Linux**

```bash
sudo useradd --no-create-home prometheus
sudo mkdir /etc/prometheus /var/lib/prometheus

tar -xvf prometheus*.tar.gz
sudo cp prometheus promtool /usr/local/bin/
sudo cp -r consoles/ console_libraries/ prometheus.yml /etc/prometheus/
```

---

### **Basic Node Exporter install**

```bash
wget https://github.com/prometheus/node_exporter/releases/latest/download/node_exporter.tar.gz
tar -xvf node_exporter.tar.gz
./node_exporter
```

---

# 🟧 **1.3 INTERMEDIATE PROMETHEUS COMMANDS**

### **Reload Prometheus without restart**

```bash
curl -X POST http://localhost:9090/-/reload
```

### **Check running targets**

```bash
curl http://localhost:9090/api/v1/targets
```

### **Check Prometheus status**

```bash
systemctl status prometheus
```

### **Start / Stop / Enable Prometheus**

```bash
sudo systemctl start prometheus
sudo systemctl stop prometheus
sudo systemctl enable prometheus
```

---

# 🟥 **1.4 ADVANCED PROMETHEUS COMMANDS**

### **Run Prometheus as a service**

```bash
sudo nano /etc/systemd/system/prometheus.service
```

Add:

```ini
[Unit]
Description=Prometheus Monitoring
After=network.target

[Service]
ExecStart=/usr/local/bin/prometheus --config.file=/etc/prometheus/prometheus.yml --storage.tsdb.path=/var/lib/prometheus

[Install]
WantedBy=multi-user.target
```

---

### **View Active Alerting Rules**

```bash
curl http://localhost:9090/api/v1/rules
```

### **View Alertmanager Active Alerts**

```bash
curl http://localhost:9093/api/v2/alerts
```

---

### **PromQL Query Examples (Advanced)**

CPU Usage %

```promql
100 - avg(irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100
```

Memory Usage %

```promql
(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100
```

Disk Free %

```promql
(node_filesystem_avail_bytes / node_filesystem_size_bytes) * 100
```

---

# 🟪 **1.5 ADVANCED PROMETHEUS SCRIPTS**

### **Automate Prometheus Backup (Retention)**

```bash
#!/bin/bash
DATA_DIR="/var/lib/prometheus"
BACKUP_DIR="/backup/prometheus-$(date +%F)"

mkdir -p $BACKUP_DIR
cp -r $DATA_DIR $BACKUP_DIR
echo "Prometheus Backup Completed: $BACKUP_DIR"
```

---

### **Script to restart Prometheus safely**

```bash
#!/bin/bash
echo "Reloading Prometheus Config..."
curl -X POST http://localhost:9090/-/reload

if [ $? -ne 0 ]; then
    echo "Reload failed. Restarting service..."
    sudo systemctl restart prometheus
fi
```

---

---

# 🎯 **2. GRAFANA COMMANDS & SCRIPTS**

---

# 🟩 **2.1 VERY BASIC GRAFANA COMMANDS**

### **Check Grafana version**

```bash
grafana-server -v
```

### **Start Grafana**

```bash
sudo systemctl start grafana-server
```

### **Enable on startup**

```bash
sudo systemctl enable grafana-server
```

### **Check service status**

```bash
sudo systemctl status grafana-server
```

---

# 🟦 **2.2 BASIC GRAFANA SCRIPTS**

### Install Grafana (Linux)

```bash
sudo wget https://dl.grafana.com/oss/release/grafana_11.0.0_amd64.deb
sudo dpkg -i grafana_11.0.0_amd64.deb
sudo systemctl start grafana-server
```

---

### Reset Grafana admin password

```bash
grafana-cli admin reset-admin-password atul123
```

---

# 🟧 **2.3 INTERMEDIATE GRAFANA COMMANDS**

### Restart Grafana server

```bash
sudo systemctl restart grafana-server
```

### View logs

```bash
sudo journalctl -u grafana-server -f
```

### Install Grafana plugin

```bash
grafana-cli plugins install grafana-clock-panel
sudo systemctl restart grafana-server
```

---

# 🟥 **2.4 ADVANCED GRAFANA COMMANDS**

### Import Dashboard via CLI

```bash
curl -X POST -H "Content-Type: application/json" \
    -d @dashboard.json \
    http://admin:admin@localhost:3000/api/dashboards/db
```

---

### Add Data Source using API

```bash
curl -X POST http://admin:admin@localhost:3000/api/datasources \
-H "Content-Type: application/json" \
-d '{
  "name":"Prometheus",
  "type":"prometheus",
  "url":"http://localhost:9090",
  "access":"proxy",
  "basicAuth":false
}'
```

---

# 🟪 **2.5 ADVANCED GRAFANA SCRIPTS**

### Backup Grafana dashboards & datasources

```bash
#!/bin/bash
DATE=$(date +%F)
BACKUP_DIR="/backup/grafana/$DATE"
mkdir -p $BACKUP_DIR

curl -s http://admin:admin@localhost:3000/api/search | jq -c '.[]' |
while read dashboard; do
    uid=$(echo $dashboard | jq -r '.uid')
    curl -s http://admin:admin@localhost:3000/api/dashboards/uid/$uid > "$BACKUP_DIR/$uid.json"
done

echo "Grafana Backup Completed: $BACKUP_DIR"
```

---

### Auto-provision dashboards script

```bash
cp -r dashboards/ /var/lib/grafana/dashboards/
systemctl restart grafana-server
```

---

# 🔥 3. DOCKER COMMANDS FOR PROMETHEUS + GRAFANA

---

### Run Prometheus in Docker

```bash
docker run -d -p 9090:9090 \
 -v $PWD/prometheus.yml:/etc/prometheus/prometheus.yml \
 prom/prometheus
```

### Run Grafana in Docker

```bash
docker run -d -p 3000:3000 \
 --name=grafana grafana/grafana
```

### Run Node Exporter

```bash
docker run -d -p 9100:9100 prom/node-exporter
```

---

