# Installing & configuring Grafana

### Download and install Grafana

Install Grafana using the APT package manager - Download the Grafana GPG key, add Grafana to the APT sources, refresh the apt cache, and check that Grafana has been added to the APT repository.

```bash
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo add-apt-repository "deb <https://packages.grafana.com/oss/deb> stable main"
sudo apt update
apt-cache policy grafana
```

**Expected output:** Ensure the top-most version matches with latest version here - [https://grafana.com/grafana/download](https://grafana.com/grafana/download)

```bash
grafana:
  Installed: (none)
  Candidate: 10.0.3
  Version table:
     10.0.3 500
        500 <https://packages.grafana.com/oss/deb> stable/main amd64 Packages
     10.0.2 500
        500 <https://packages.grafana.com/oss/deb> stable/main amd64 Packages
     10.0.1 500
        500 <https://packages.grafana.com/oss/deb> stable/main amd64 Packages
```

Run the installation command.

```bash
sudo apt install -y grafana
```

### Start the Grafana server.

```bash
sudo systemctl start grafana-server
sudo systemctl status grafana-server
```

The output should say Prometheus is **“active (running)”.** Press CTRL-C to exit and Grafana will continue to run.

Use the following command to check the logs for any warnings or errors:

```bash
sudo journalctl -fu grafana-server
```

Press `CTRL-C` to exit.

If the Prometheus service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable grafana-server
```

### Configure the Grafana Dashboard

1. go to `http://<yourserverip>:3000/`
2. Enter `admin` for both username and password
3. Select `Data Sources` and click on `Add data source` , then choose **Prometheus** and enter [**http://localhost:9090**](http://localhost:9090) for the URL
4. Setup the execution client (Nethermind) dashboard - On the left menu bar, click on **Dashboards >> Import**
   * On the Import screen, paste the JSON text from [here](https://raw.githubusercontent.com/NethermindEth/metrics-infrastructure/master/grafana/dashboards/nethermind.json)
5. Setup the consensus client (Teku) dashboard - On the left menu bar, click on **Dashboards >> Import**
   * On the Import screen, paste the JSON text from [here](https://raw.githubusercontent.com/sigp/lighthouse-metrics/master/dashboards/Summary.json)
