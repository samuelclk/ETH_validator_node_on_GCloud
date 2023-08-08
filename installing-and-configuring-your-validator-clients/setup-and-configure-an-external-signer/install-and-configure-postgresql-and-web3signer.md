# Install and configure PostgreSQL and Web3signer

#### Installation

Install Java dependencies

```bash
sudo apt install default-jre
```

Download and install latest PostgreSQL. Reference [here](https://www.postgresql.org/download/linux/ubuntu/).

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

If checksum is verified, extract the files.

```sh
tar xvf web3signer-23.6.0.tar.gz
```

#### Creating PostgreSQL database

Create a new user called `postgres` for PostgreSQL to run under and set the password to a default “password” for now.

```bash
sudo useradd --no-create-home postgres
passwd postgres
```

Copy the extracted Web3signer directory into the postgreSQL home directory and change the folder owner to `postgres`.&#x20;

```bash
cd
sudo cp -r web3signer-23.6.0 /var/lib/postgresql
sudo chown -R postgres:postgres /var/lib/postgresql/web3signer-23.6.0
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
\\q
```

#### Load the web3signer database schema onto postgreSQL

Iterate through all schema .sql files (eg. V00001 - V00011) under the `/web3signer-23.3.1/migrations/postgresql/migrations` folder using the following command:

```bash
psql --echo-all --host=localhost --port=5432 --dbname=web3signer --username=postgres -f /Users/me/web3signer-0.2.1-SNAPSHOT/migrations/postgresql/postgresql/V1__initial.sql
```

Then, log out of the postgres user.

```yaml
exit
```

#### Configure the Web3signer YAML service

Create a folder to store the web3signer configuration file and fill with the subsequent YAML contents.

```bash
sudo mkdir /var/lib/postgresql/web3signer_config/
sudo touch /var/lib/postgresql/web3signer_config/config.yaml
sudo chown -R postgres:postgres /var/lib/postgresql/web3signer_config/
sudo nano /var/lib/postgresql/web3signer_config/config.yaml
```

```yaml
type: "file-keystore"
keyType: "BLS"
keystoreFile: "/var/lib/postgresql/validator_signing_keystores/keystore-m_<TIMESTAMP>.json"
keystorePasswordFile: "/var/lib/postgresql/validator_signing_keystores/keystores_password.txt"

---

```

Create a folder to store the validator signing keystore and the keystore password.

```bash
sudo mkdir /var/lib/postgresql/validator_signing_keystores/
sudo touch /var/lib/postgresql/validator_signing_keystores/keystores_password.txt
sudo cp /path/to/keystore_in_usb/keystore-m_<TIMESTAMP>.json /var/lib/postgresql/validator_signing_keystores
sudo chown -R postgres:postgres /var/lib/postgresql/validator_signing_keystores/
```

#### Configure the Web3signer systemd service

Copy the web3signer folder into /usr/local/bin

```bash
sudo cp -r $HOME/web3signer-23.6.0 /usr/local/bin
```

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
ExecStart=/usr/local/bin/web3signer-23.6.0/bin/web3signer \\
  --http-listen-host=10.0.0.10 \\
  --http-listen-port=9000 \\
  --http-host-allowlist=10.0.0.10 \\
  --key-store-path=/var/lib/postgresql/web3signer_config/ \\
  eth2 \\
  --network=mainnet \\
  --slashing-protection-db-url="jdbc:postgresql://localhost/web3signer" \\
  --slashing-protection-db-username=postgres \\
  --slashing-protection-db-password=password \\
  --slashing-protection-pruning-enabled=true
[Install]
WantedBy=multi-user.target
```

Refresh the systemd daemon, start + enable web3signer, and monitor logs.

```yaml
sudo systemctl daemon-reload
sudo systemctl start web3signer.service
sudo systemctl enable web3signer.service
sudo journalctl -fu web3signer -o cat | ccze -A
```

Check that all validator signing keystores are loaded successfully and that web3signer is and ready to handle signing requests on `10.0.0.10:9000` .

Finally, clean up duplicated copies of Web3signer

```
sudo rm -r $HOME/web3signer-23.6.0 /var/lib/postgresql/web3signer-23.6.0
```
