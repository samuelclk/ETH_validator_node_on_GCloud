# Install and configure PostgreSQL and Web3signer

### Installation

Install Java dependencies

```bash
sudo apt install default-jre
```

Download and install latest version of PostgreSQL. Reference [here](https://www.postgresql.org/download/linux/ubuntu/).

```bash
sudo sh -c 'echo "deb <http://apt.postgresql.org/pub/repos/apt> $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
wget --quiet -O - <https://www.postgresql.org/media/keys/ACCC4CF8.asc> | sudo apt-key add -
sudo apt-get update
sudo apt-get -y install postgresql
```

Download and unzip latest Web3signer version [here](https://github.com/ConsenSys/web3signer/releases).

<pre class="language-bash"><code class="lang-bash">cd
curl -LO &#x3C;https://artifacts.consensys.net/public/web3signer/raw/names/web3signer.tar.gz/versions/23.6.0/web3signer-23.6.0.tar.gz>
<strong>echo "250c91e7fa18ae9d4962b083a95a7018775a6b99991f1423ce99ffef0366d4a5 web3signer-23.6.0.tar.gz" | sha256sum --check 
</strong></code></pre>

_**Expected output:** Verify output of the checksum verification_

```
web3signer-23.6.0.tar.gz: OK
```

If checksum is verified, extract the files into the `/usr/local/bin`directory and delete the zipped file.

```sh
tar xvf web3signer-23.6.0.tar.gz -C /var/local/bin
sudo rm web3signer-23.6.0.tar.gz
```

### Creating PostgreSQL database

Create a new user called `postgres` for PostgreSQL to run under and set the password for `postgres`

```bash
sudo useradd --no-create-home postgres
passwd postgres
```

Set the owner of the Web3signer directory to `postgres`.&#x20;

```bash
sudo chown -R postgres:postgres /usr/local/bin/web3signer-23.6.0
```

Log in into the “postgres” user and start the postgreSQL server

```bash
sudo -i -u postgres
psql
```

Create the web3signer database.

```bash
CREATE DATABASE web3signer;
ALTER USER postgres WITH ENCRYPTED PASSWORD 'password';
GRANT ALL PRIVILEGES ON DATABASE web3signer TO postgres;
```

Exit the postgreSQL server.

```bash
\q
```

#### Load the web3signer database schema onto postgreSQL

Iterate through all schema .sql files (eg. V00001 - V00011) under the `web3signer-23.6.0/migrations/postgresql` folder using the following command:

```bash
psql --echo-all --host=localhost --port=5432 --dbname=web3signer --username=postgres -f /usr/local/bin/web3signer-23.6.0/migrations/postgresql/V00001__initial.sql
```

Then, log out of the postgres user.

```yaml
exit
```

### Configure the Web3signer YAML service

Create a directory to store the web3signer configuration file and fill with the subsequent YAML contents.

```bash
sudo mkdir /var/lib/postgresql/web3signer_config/
sudo touch /var/lib/postgresql/web3signer_config/config.yaml
sudo nano /var/lib/postgresql/web3signer_config/config.yaml
```

```yaml
type: "file-keystore"
keyType: "BLS"
keystoreFile: "/var/lib/postgresql/validator_signing_keystores/keystore-m_<TIMESTAMP_01>.json"
keystorePasswordFile: "/var/lib/postgresql/validator_signing_keystores/keystores_password.txt"

---
type: "file-keystore"
keyType: "BLS"
keystoreFile: "/var/lib/postgresql/validator_signing_keystores/keystore-m_<TIMESTAMP_02>.json"
keystorePasswordFile: "/var/lib/postgresql/validator_signing_keystores/keystores_password.txt"

---
```

**Web3signer YAML configuration summary:**

1. keystoreFile: File path to your actual validator signing keystores
2. keystorePasswordFile: File path to your actual validator signing keystore password stored in plain text format

Set the owner of the directory containing the web3signer configuration file to `postgres`

```bash
sudo chown -R postgres:postgres /var/lib/postgresql/web3signer_config/
```

Create a folder to store the validator signing keystore and the keystore password.

```bash
sudo mkdir /var/lib/postgresql/validator_signing_keystores/
sudo touch /var/lib/postgresql/validator_signing_keystores/keystores_password.txt
sudo cp /path/to/keystore_in_usb/keystore-m_<TIMESTAMP>.json /var/lib/postgresql/validator_signing_keystores
```

Set the owner of the directory containing the validator signing keystore and the keystore password to `postgres`

```bash
sudo chown -R postgres:postgres /var/lib/postgresql/validator_signing_keystores/
```

### Configure the Web3signer systemd service

Create a systemd configuration file for web3signer and paste the subsequent configuration contents in it.

```bash
sudo nano /etc/systemd/system/web3signer.service
```

```bash
[Unit]
Description=Web3signer
After=network.target

[Service]
User=postgres
Group=postgres
Type=simple
Restart=always
RestartSec=5
ExecStart=/usr/local/bin/web3signer-23.6.0/bin/web3signer \
  --http-listen-host=<Signer_internal_IP> \
  --http-listen-port=9000 \
  --http-host-allowlist=<Signer_internal_IP> \
  --key-store-path=/var/lib/postgresql/web3signer_config/ \
  eth2 \
  --network=mainnet \
  --slashing-protection-db-url="jdbc:postgresql://localhost/web3signer" \
  --slashing-protection-db-username=postgres \
  --slashing-protection-db-password=<postgres_user_password> \
  --slashing-protection-pruning-enabled=true
[Install]
WantedBy=multi-user.target
```

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`. Understand and review your configuration. Amend if needed.

**Web3signer key `systemd` configuration flags:**

1. \--key-store-path: File path to the directory containing the YAML configuration file and not the actual signing keystore file&#x20;
2. \--slashing-protection-db-url: Default syntax connecting to the PostgresSQL database we created earlier&#x20;
3. \--slashing-protection-pruning-enabled: Prunes the slashing protection database over time

Reload systemd to register the changes made, start the Web3signer service, and check its status to make sure its running.

```bash
sudo systemctl daemon-reload
sudo systemctl start web3signer.service
sudo systemctl status web3signer.service

```

**Expected output:** The output should say that Web3signer is **“active (running)”.**

<figure><img src="../../../../.gitbook/assets/image (12).png" alt=""><figcaption><p>sudo systemctl status web3signer.service</p></figcaption></figure>

Press `Ctrl+C` to exit monitoring.

Use the following command to check the logs of the Web3signer service. Watch out for any warnings or errors.

```bash
sudo journalctl -fu web3signer -o cat | ccze -A
```

**Expected output:** Check that all validator signing keystores are loaded successfully and that web3signer is and ready to handle signing requests on `<Signer_internal_IP>:9000` .

<figure><img src="../../../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

Press `Ctrl+C` to exit monitoring.

If the Web3signer service is running smoothly, we can now enable it to fire up automatically when rebooting the system.

```bash
sudo systemctl enable web3signer.service
```
