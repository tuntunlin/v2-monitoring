# အဆင့် ၁ - Prometheus ကို တပ်ဆင်ခြင်း (Install Prometheus)

```bash
# ၁။ Prometheus အတွက် System User သီးသန့် ဖန်တီးပါ
sudo useradd --no-create-home --shell /bin/false prometheus

# ၂။ လိုအပ်သော Directories များကို ဖန်တီးပြီး Permissions ပေးပါ:
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
sudo chown prometheus:prometheus /var/lib/prometheus

၃။ Prometheus Latest Version ကို Download ဆွဲပြီး ဖြည်ပါ:
cd /tmp
wget https://github.com/prometheus/prometheus/releases/download/v2.51.0/prometheus-2.51.0.linux-amd64.tar.gz
tar xvf prometheus-2.51.0.linux-amd64.tar.gz
cd prometheus-2.51.0.linux-amd64

၄။ Binary ဖိုင်များကို သင့်တော်ရာ လမ်းကြောင်းသို့ ကူးယူပါ:
sudo cp prometheus promtool /usr/local/bin/
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool

၅။ Configuration ဖိုင်များကို Copy ကူးပါ:
sudo cp -r consoles console_libraries /etc/prometheus/
sudo cp prometheus.yml /etc/prometheus/prometheus.yml
sudo chown -R prometheus:prometheus /etc/prometheus/

၆။ Systemd Service ဖိုင် ဖန်တီးပါ (/etc/systemd/system/prometheus.service):
sudo nano /etc/systemd/system/prometheus.service

အောက်ပါ Content ကို ထည့်သွင်းပါ -

[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus/ \
  --web.listen-address=0.0.0.0:9090

[Install]
WantedBy=multi-user.target

၇။ Service ကို Start လုပ်ပြီး Enable ပေးပါ:
sudo systemctl daemon-reload
sudo systemctl start prometheus
sudo systemctl enable prometheus

```
# အဆင့် ၂ - Grafana ကို တပ်ဆင်ခြင်း (Install Grafana)

```bash
# ၁။ လိုအပ်သော Packages များကို ထည့်သွင်းပါ:
sudo apt-get update
sudo apt-get install -y apt-transport-https software-properties-common wget

# ၂။ Grafana GPG Key နှင့် Repository ကို ထည့်ပါ:
wget -q -O - https://packages.grafana.com/gpg.key | sudo gpg --dearmor -o /usr/share/keyrings/grafana.gpg
echo "deb [signed-by=/usr/share/keyrings/grafana.gpg] https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list

# ၃။ Grafana ကို Install လုပ်ပါ:
sudo apt-get update
sudo apt-get install -y grafana

# ၄။ Grafana Service ကို Start လုပ်ပြီး Enable ပေးပါ:
sudo systemctl daemon-reload
sudo systemctl start grafana-server
sudo systemctl enable grafana-server

စစ်ဆေးရန်: Browser ကနေ http://<monitoring-server-ip>:3000 ကို ဝင်ကြည့်ပါ။

Default Username: admin

Default Password: admin (ပထမအကြိမ် ဝင်တဲ့အခါ Password အသစ်ပြောင်းခိုင်းပါမယ်)

```

