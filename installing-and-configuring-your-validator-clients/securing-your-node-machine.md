# Securing your node machine

Install SSH server on the server

```bash
sudo apt install openssh-server
```

### Generate your SSH keys

_**If you have a Yubikey:**_

With a Yubikey plugged in on your client machine, run

```bash
eval $(ssh-agent)
ssh-keygen -t ed25519-sk -C <your email address>
```

Add SSH public key to the server device:

```bash
ssh-copy-id -i ~/.ssh/id_ed25519-sk.pub username@node.ip.address
ssh-add ~/.ssh/id_ed25519-sk
```

_**If you don't have a Yubikey:**_

On your client machine, run

```bash
eval $(ssh-agent)
ssh-keygen -t ed25519 -C <your email address>
```

Add SSH public key to the server device:

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub <username>@<node.ip.address>
ssh-add ~/.ssh/id_ed25519
```

Alternatively, create the **.ssh** folder and **authorized\_keys** file on the server and copy the SSH public key into the following file.

```bash
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
```

### Modify the SSH port and disable remote password login

Make it harder for attackers to access your node by switching your SSH port away from the default port 22.

Pick a port number between 1024–49151 and check that it is not already in use by running

```bash
sudo ss -tulpn | grep <Chosen_SSH_Port_Number>
```

If there is no output, it means that your chosen port is free to use.

#### Next, we will change your SSH port number and disable remote password login concurrently

Run the following command to open up the SSH server configuration file.

```bash
sudo nano /etc/ssh/sshd_config
```

1. Find the line `Port 22` in the file. Change it to your chosen port number and then uncomment the line by removing the `#` prefix (if it exists)
2. Uncomment `#AuthorizedKeysFile` if it is commented (by removing the `#` in front of it)
3. Change `KbdInteractiveAuthentication yes` to `KbdInteractiveAuthentication no` and uncomment (by removing the `#` in front of it) - **older versions of SSH call this option `ChallengeResponseAuthentication` instead of `KbdInteractiveAuthentication`**
4. Change `PasswordAuthentication yes` to `PasswordAuthentication no` and uncomment (by removing the `#` in front of it)
5. Change `PermitRootLogin yes` to `PermitRootLogin prohibit-password` unless it's already set to that and has a `#` in front of it

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
