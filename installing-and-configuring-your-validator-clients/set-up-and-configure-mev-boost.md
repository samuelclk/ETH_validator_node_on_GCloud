# Set up and configure MEV-boost

Download latest version of MEV-boost [here](https://github.com/flashbots/mev-boost/releases) and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd 
curl -LO <https://github.com/flashbots/mev-boost/releases/download/v1.6/mev-boost_1.6_linux_amd64.tar.gz>
echo "03ac6cfb13d8f8b6d59f9de5de753412b0840024ef354399bcb4aa4ee8fe5c3b mev-boost_1.6_linux_amd64.tar.gz" | sha256sum --check
```

If checksum is verified, extract the files and move them into the \*\*\*\*`(/usr/local/bin)` directory for neatness and best practice. Then, clean up the duplicated copies.

```bash
tar xvf mev-boost_1.6_linux_amd64.tar.gz
sudo cp mev-boost /usr/local/bin
rm mev-boost LICENSE README.md mev-boost_1.6_linux_amd64.tar.gz

```

Create an account (`mevboost`) without server access for MEV Boost to run as a background service. This restricts potential attackers to only the MEV Boost service in the unlikely event that they manage to infiltrate via a compromised client update.

```bash
sudo useradd --no-create-home --shell /bin/false mevboost
```

Create a systemd configuration file for the tekubeacon service to run in the background.

```bash
sudo nano /etc/systemd/system/mevboost.service
```

Paste the configuration parameters below into the file:

```bash
[Unit]
Description=mev-boost (Mainnet)
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=mevboost
Group=mevboost
Restart=always
RestartSec=5
ExecStart=mev-boost \\
    -mainnet \\
    -min-bid 0.05 \\
    -relay-check \\
    -relay <https://example.com> \\
    -relay <https://example.com> \\
    -relay <https://example.com> \\
    -relay <https://example.com> 

[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

**MEV Boost configuration summary (WIP):**

Choose relays here - [https://github.com/eth-educators/ethstaker-guides/blob/main/MEV-relay-list.md](https://github.com/eth-educators/ethstaker-guides/blob/main/MEV-relay-list.md)

#### Start the MEV Boost service

Reload the systemd daemon to register the changes made, start MEV Boost, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start mevboost
sudo systemctl status mevboost
```

The output should say MEV Boost is **“active (running)”.** Press CTRL-C to exit and MEV Boost will continue to run.

Use the following command to check the logs for any warnings or errors:

```bash
sudo journalctl -fu mevboost -o cat | ccze -A
```

Press `CTRL-C` to exit.

If the MEV Boost service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable mevboost.service
```
