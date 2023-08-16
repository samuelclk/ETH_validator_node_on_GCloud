# Beaconcha.in App API

Go to beaconcha.in on your browser and sign up for an account.

Download the beaconcha.in app on your mobile phone.

Once you are logged in, click on your User icon on the top right corner and select _**"Settings".**_

<figure><img src="../../.gitbook/assets/Screenshot 2023-08-16 at 2.52.42 PM.png" alt=""><figcaption></figcaption></figure>

Click on the _**"Mobile App"**_ tab and select _**"Desktop"**_ as the Architecture option.

<figure><img src="../../.gitbook/assets/Screenshot 2023-08-16 at 2.54.21 PM.png" alt=""><figcaption></figcaption></figure>

Select your consensus layer client from the list and copy the resulting flag with your own unique API key. As you can see, I have redacted my API key below and you should also make sure not to reveal yours.

<figure><img src="../../.gitbook/assets/Screenshot 2023-08-16 at 2.57.08 PM.png" alt=""><figcaption></figcaption></figure>

Next, you will SSH into your validator node and add this flag into your Teku (or other CL) client.

Once you are logged in to your validator node, run the following command to open the configuration file of your Teku Beacon Node:

```bash
sudo nano /etc/systemd/system/tekubeacon.service
```

Add in the flag you copied earlier into the configuration file.

```bash
[Unit]
Description=Teku Beacon Node (Mainnet)
Wants=network-online.target
After=network-online.target
[Service]
User=teku
Group=teku
Type=simple
Restart=always
RestartSec=5
Environment="JAVA_OPTS=-Xmx6g"
Environment="TEKU_OPTS=-XX:-HeapDumpOnOutOfMemoryError"
ExecStart=/usr/local/bin/teku/bin/teku \
  --network=mainnet \
  --data-path=/var/lib/teku \
  --ee-endpoint=http://127.0.0.1:8551 \
  --ee-jwt-secret-file=/var/lib/jwtsecret/jwt.hex \
  --initial-state=https://beaconstate.ethstaker.cc \
  --metrics-enabled=true \
  --rest-api-enabled=true \
  --builder-endpoint=http://127.0.0.1:18550 \
  --validators-builder-registration-default-enabled=true \
  --metrics-publish-endpoint 'https://beaconcha.in/api/v1/client/metrics?apikey=<your_API_key>
  
[Install]
WantedBy=multi-user.target
```

Press `CRTL + O`, `ENTER`, then `CTRL + X` to save and exit.

Next, do the same for the Teku validator client.

```bash
sudo nano /etc/systemd/system/tekuvalidator.service
```

Add in the same flag you copied earlier into the configuration file.

