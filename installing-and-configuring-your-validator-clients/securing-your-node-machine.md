# Securing your node machine

Install SSH server on the server

```bash
sudo apt install openssh-server
```

### Generate your SSH keys

_**If you have a Yubikey:**_

With a Yubikey plugged in on your client machine (e.g. working laptop), run

```bash
eval $(ssh-agent)
ssh-keygen -t ed25519-sk -C <your email address>
cat .ssh/id_ed25519-sk.pub #<-- this prints your SSH public key
```

_**If you don't have a Yubikey:**_

On your client machine, run

```bash
eval $(ssh-agent)
ssh-keygen -t ed25519 -C <your email address>
cat .ssh/id_ed25519.pub #<-- this prints your SSH public key
```

Click into your VM on the Google Cloud Console, click **"Edit"**, then scroll down to the **"SSH keys"** sub-section, click **"Add Item"**, and add your SSH public key into a new entry. Then save your changes.

<figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

Alternatively, create the **.ssh** folder and **authorized\_keys** file on the server and copy the SSH public key into the following file.

```bash
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
```

### Change the SSH port and disable remote password login

Make it harder for attackers to access your node by changing your SSH port away from the default port 22.

Pick a port number between 1024–49151 and check that it is not already in use by running

```bash
sudo ss -tulpn | grep <Chosen_SSH_Port_Number>
```

**Expected output:** None. _i.e. If there is no output, it means that your chosen port is free to use._

#### Next, we will change your SSH port number and disable remote password login concurrently

Run the following command to open up the SSH server configuration file.

```bash
sudo nano /etc/ssh/sshd_config
```

1. Find the line `Port 22` in the file. Change it to your chosen port number and then uncomment the line by removing the `#` prefix (if it exists)
2. Uncomment `#AuthorizedKeysFile` if it is commented (by removing the `#` in front of it)
3. Change `KbdInteractiveAuthentication yes` to `KbdInteractiveAuthentication no` and uncomment (by removing the `#` in front of it) - **older versions of SSH call this option `ChallengeResponseAuthentication` instead of `KbdInteractiveAuthentication`**
4. Change `PasswordAuthentication yes` to `PasswordAuthentication no` and uncomment (by removing the `#` in front of it)
5. Change `#PermitRootLogin prohibit-password` to `PermitRootLogin no` , removing the `#` prefix

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`.

Now we restart the SSH server so it registers the new settings:

```bash
sudo systemctl restart sshd
```

### Configure the firewall rules

The basic rules we will implement are as follows:

1. Deny all incoming traffic by default
2. Allow all outgoing traffic by default
3. Allow incoming traffic via port \<your\_chosen\_SSH\_port> for SSH access
4. Allow incoming traffic via port 30303 for Nethermind to connect with other nodes
5. Allow incoming traffic via port 9000 for Teku to connect with other nodes
6. Allow incoming traffic via port 3000 for Grafana to display monitoring dashboards for your node

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow <your_chosen_SSH_port>/tcp
sudo ufw allow 30303
sudo ufw allow 9000
sudo ufw allow 3000
```

With these configurations set up, you will have blocked off all but 4 possible openings for potential attackers to enter from.&#x20;

* Ports 30303 and 9000 will be occupied by Nethermind and Teku so there’s no “space” for attackers to use
* Port 3000 is only accessible within your local area network (i.e. not exposed to the public internet
* Your chosen SSH port is secured by your SSH private key so its virtually impossible to brute force the access.

#### Turn on the firewall

Now lets turn the firewall on and check our configurations before moving on.

```bash
sudo ufw enable
sudo ufw status numbered
```

You should see something similar to the screenshot below:

<figure><img src="../.gitbook/assets/Screenshot 2023-08-09 at 3.31.44 PM.png" alt=""><figcaption></figcaption></figure>

Now we have to amend the firewall rules on our Google Cloud VPC to align with what we configured here. Head over to your Google Cloud Console, go into the **"VPC networks"** settings, and select your dedicated VPC network.

### VPC settings: SSH Port

Under **"Firewalls",** click into the **"xx-allow-ssh"** rule.

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 5.40.20 PM.png" alt=""><figcaption></figcaption></figure>

Then, click on **"Edit"**, change the **"TCP"** port to your amended SSH port, and hit **"Save"**.

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 5.42.02 PM.png" alt=""><figcaption></figcaption></figure>

After hitting "Save", your existing connection might be broken. If it is, you can either,&#x20;

* Reconnect using your own SSH key from your working laptop by running:

```bash
ssh <username>@<VM_external_IP> -p <your_chosen_SSH_port> -i <path_to_SSH_private_key> -v
```

* Reconnect using the Google Cloud Console using the custom port option

<figure><img src="../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

### VPC settings: Ports 30303, 9000, & 3000

Under **"Firewalls",** click **"ADD FIREWALL RULE"**.

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 5.40.20 PM.png" alt=""><figcaption></figcaption></figure>

Configure the following firewall settings for **port 30303.**

1. Name: \*any
2. Network: your dedicated VPC created earlier
3. Priority: 65534
4. Direction of traffic: Ingress
5. Allow on match: Allow
6. Targets: Specified target tags
7. Target tags: tcp-udp (or whatever the name your chose for your network tag for your validator node VM earlier)
8. Source IPv4 ranges: 0.0.0.0/0
9. TCP: 30303
10. UDP: 30303

<div>

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

 

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

</div>

Then, hit **"CREATE"** and repeat the same steps for **ports 9000 and 3000.**

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 5.42.02 PM.png" alt=""><figcaption></figcaption></figure>

### **Set up brute force protection**

Even though having our SSH key access implemented means an attacker will need 25 million years to try all combinations, lets go ahead and make it even harder for them - by limiting the number of attempts per IP address to 5 tries and blocking them after.

_**Install the software:**_

```bash
sudo apt install -y fail2ban
```

_**Open the configuration file:**_

```bash
sudo nano /etc/fail2ban/jail.d/ssh.local
```

_**Add the following contents to the configuration file:**_

```bash
[sshd]
enabled = true
banaction = ufw
port = <your_chosen_SSH_port>
filter = sshd
logpath = %(sshd_log)s
maxretry = 5
```

Once you're done, save and exit with `Ctrl+O`and `Enter`, then `Ctrl+X`.

_**Finally, restart the service:**_

```bash
sudo systemctl restart fail2ban
```

### Enable automatic security updates

```bash
sudo apt update
sudo apt install -y unattended-upgrades update-notifier-common
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
```

_**Add the following contents to the configuration file:**_

```
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
APT::Periodic::AutocleanInterval "7";
Unattended-Upgrade::Remove-Unused-Dependencies "true";
Unattended-Upgrade::Remove-New-Unused-Dependencies "true";
Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-Time "02:00";
```

Once you're done, save and exit with `Ctrl+O`and `Enter`, then `Ctrl+X`.

_**Finally, restart the service:**_

```bash
sudo systemctl restart unattended-upgrades
```
