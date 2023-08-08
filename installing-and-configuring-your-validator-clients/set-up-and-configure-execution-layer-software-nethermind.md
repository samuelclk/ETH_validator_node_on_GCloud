# Set up and configure execution layer software (Nethermind)

#### Generate the JWT file

We first need to create a JSON Web Token (JWT) that will allow the execution layer software (Geth) and the consensus layer software (Lighthouse) to talk to each other.

Run the following commands one line at a time to create a folder on the server to store the JWT file and generate the JWT file:

```bash
sudo mkdir -p /var/lib/jwtsecret
openssl rand -hex 32 | sudo tee /var/lib/jwtsecret/jwt.hex > /dev/null
```

We will be pointing the configuration files of the Nethermind and Teku software to this JWT file later.

#### Install dependencies - Unzip, Snappy & the GNU C Library

```bash
sudo apt-get update
sudo apt-get install unzip libsnappy-dev libc6-dev libc6 -y
```

#### Install and configure the execution layer software

[Download](https://downloads.nethermind.io/) the latest version of Nethermind and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO <https://nethdev.blob.core.windows.net/builds/nethermind-1.20.1-9f39c0c7-linux-x64.zip>
echo "nethermind-1.20.1-9f39c0c7-linux-x64.zip" | md5sum --check
```

Verify output of the checksum verification:

If checksum is verified, extract the files and move them into the \*\*\*\*`(/usr/local/bin)` directory for neatness and best practice. Then, clean up the duplicated copies.

```bash
unzip nethermind-1.20.1-9f39c0c7-linux-x64.zip -d nethermind
sudo cp -a nethermind /usr/local/bin/nethermind
rm -r nethermind-1.20.1-9f39c0c7-linux-x64.zip nethermind
```

Create an account (`nethermind`) without server access for Nethermind to run as a background service. This restricts potential attackers to only the Nethermind service in the unlikely event that they manage to infiltrate via a compromised client update.

```bash
sudo useradd --no-create-home --shell /bin/false nethermind
```

Create a directory for Nethermind to store the blockchain data of the execution layer. Then set the owner of this directory to `nethermind` so that this user can read and write to the directory.

```bash
sudo mkdir -p /var/lib/nethermind
sudo chown -R nethermind:nethermind /var/lib/nethermind
```

Create a systemd configuration file for the Nethermind service to run in the background.

```bash
sudo nano /etc/systemd/system/nethermind.service
```

Paste the configuration parameters below into the file:

```bash
[Unit]
Description=Nethermind Execution Client (Mainnet)
After=network.target
Wants=network.target

[Service]
User=nethermind
Group=nethermind
Type=simple
Restart=always
RestartSec=5
WorkingDirectory=/var/lib/nethermind
Environment="DOTNET_BUNDLE_EXTRACT_BASE_DIR=/var/lib/nethermind"
ExecStart=/usr/local/bin/nethermind/Nethermind.Runner \\
  --config mainnet \\
  --datadir /var/lib/nethermind \\
  --JsonRpc.JwtSecretFile /var/lib/jwtsecret/jwt.hex \\
  --Sync.SnapSync true \\
  --Sync.AncientBodiesBarrier 11052984 \\
  --Sync.AncientReceiptsBarrier 11052984 \\
  --Metrics.Enabled true \\
  --Metrics.PushGatewayUrl=http://localhost:9091/metrics
  
[Install]
WantedBy=default.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration summary below, and amend if needed.

**Nethermind configuration summary (WIP):**

`--config mainnet --datadir /var/lib/nethermind --JsonRpc.JwtSecretFile /var/lib/jwtsecret/jwt.hex --Sync.SnapSync true --Sync.AncientBodiesBarrier 11052984 --Sync.AncientReceiptsBarrier 11052984`

#### Start Nethermind

Reload the systemd daemon to register the changes made, start Nethermind, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start nethermind.service
sudo systemctl status nethermind.service
```

The output should say Nethermind is **“active (running)”.** Press CTRL-C to exit and Nethermind will continue to run. It should take 48 - 72 hours for Nethermind to sync on the Mainnet.

Use the following command to check the logs of Nethermind’s syncing process. Watch out for any warnings or errors.

```bash
sudo apt install ccze -y
sudo journalctl -fu nethermind -o cat | ccze -A
```

Press `CTRL-C` to exit.

If the Nethermind service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable nethermind.service
```
