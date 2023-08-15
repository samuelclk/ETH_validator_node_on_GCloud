# Updating Nethermind

### Download the latest version

[Download](https://downloads.nethermind.io/) the latest version of Nethermind and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://nethdev.blob.core.windows.net/builds/nethermind-1.20.1-9f39c0c7-linux-x64.zip
echo "444bf523e0db9c23243b365e717b5b15 nethermind-1.20.1-9f39c0c7-linux-x64.zip" | md5sum --check
```

_**Expected output:** Verify output of the checksum verification_

```
nethermind-1.20.1-9f39c0c7-linux-x64.zip: OK
```

Stop the Nethermind service

```
sudo systemctl stop nethermind.service
```

Extract the files, delete the previous version, and move the new version into the `(/usr/local/bin)` directory. Then, clean up the duplicated copies.

```bash
unzip nethermind-1.20.1-9f39c0c7-linux-x64.zip -d nethermind
sudo rm -r /usr/local/bin/nethermind
sudo cp -a nethermind /usr/local/bin/nethermind
rm -r nethermind-1.20.1-9f39c0c7-linux-x64.zip nethermind
```

### Restart the Nethermind service

Reload the systemd daemon to register the changes made, start Nethermind, and check its status to make sure its running.

```bash
sudo systemctl start nethermind.service
sudo systemctl status nethermind.service
```

**Expected output:** The output should say Nethermind is **“active (running)”.** Press CTRL-C to exit and Nethermind will continue to run. It should take 48 - 72 hours for Nethermind to sync on the Mainnet.

<figure><img src="../../.gitbook/assets/image (7) (1).png" alt=""><figcaption><p>sudo systemctl status nethermind.service</p></figcaption></figure>

Use the following command to check the logs of Nethermind’s syncing process. Watch out for any warnings or errors.

```bash
sudo journalctl -fu nethermind -o cat | ccze -A
```

**Expected output:**

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption><p>sudo journalctl -fu nethermind -o cat | ccze -A</p></figcaption></figure>

Press `CTRL-C` to exit.
