# Updating Teku

### Download the latest version

[Download](https://github.com/ConsenSys/teku/releases) the latest version of Teku and run the checksum verification process to ensure that the downloaded file has not been tampered with.

```bash
cd
curl -LO https://artifacts.consensys.net/public/teku/raw/names/teku.tar.gz/versions/23.6.2/teku-23.6.2.tar.gz
echo "c0b021ead22a514fdb8d5e2ce072549dc447dfdeed660eccef0e3a42890e56c2 teku-23.6.2.tar.gz" | sha256sum --check
```

_**Expected output:** Verify output of the checksum verification._

```
teku-23.6.2.tar.gz: OK
```

Stop the Teku beacon node and validator client services.

```bash
sudo systemctl stop tekubeacon.service tekuvalidator.service
```

Extract the files, delete the previous version, and move the new version into the `(/usr/local/bin)` directory. Then, clean up the duplicated copies.

```bash
tar xvf teku-23.6.2.tar.gz
sudo rm -r /usr/local/bin/teku
sudo cp -a teku-23.6.2 /usr/local/bin/teku
rm -r teku-23.6.2.tar.gz teku-23.6.2
```

### Restart the Teku services

```bash
sudo systemctl start tekubeacon.service tekuvalidator.service
sudo systemctl status tekubeacon.service tekuvalidator.service
```

Monitor journal logs using

```bash
sudo journalctl -fu tekubeacon -o cat | ccze -A
sudo journalctl -fu tekuvalidator -o cat | ccze -A
```
