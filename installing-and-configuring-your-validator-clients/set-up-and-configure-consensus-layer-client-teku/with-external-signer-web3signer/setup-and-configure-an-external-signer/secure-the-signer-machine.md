# Secure the Signer machine

### Section 6 - Secure device

#### Configure the firewall

The basic rules we will implement are as follows:

1. Deny all incoming traffic by default
2. Allow all outgoing traffic by default
3. Allow incoming traffic via port 22 for SSH access
4. Allow incoming traffic via port 9000 for the node device to communicate with this device

```bash
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp
sudo ufw allow from 10.0.0.11 to 10.0.0.10 port 9000
```

With these configurations set up, you will have blocked off all but 2 openings for attackers to enter from. Port 9000 will be occupied by web3signer so there’s no “space” for attackers to use. Port 22 is secured by your SSH private key so its virtually impossible to brute force the access.

#### Turn on the firewall

Now lets turn the firewall on and check our configurations before moving on.

```bash
sudo ufw enable
sudo ufw status numbered
```

#### Set up brute force protection

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
port = 22
filter = sshd
logpath = %(sshd_log)s
maxretry = 5
```

Once you're done, save and exit with `Ctrl+O`and `Enter`, then `Ctrl+X`.

_**Finally, restart the service:**_

```bash
sudo systemctl restart fail2ban
```

#### Enable automatic security updates

```bash
sudo apt update
sudo apt install -y unattended-upgrades update-notifier-common
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
```

```bash
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
APT::Periodic::AutocleanInterval "7";
Unattended-Upgrade::Remove-Unused-Dependencies "true";
Unattended-Upgrade::Remove-New-Unused-Dependencies "true";
Unattended-Upgrade::Automatic-Reboot "true";
Unattended-Upgrade::Automatic-Reboot-Time "02:00";
```

```bash
sudo systemctl restart unattended-upgrades
```

#### Change login password

```bash
sudo passwd <username>
```

#### Install SSH server on the device

```bash
sudo apt install openssh-server
```

#### Generate the SSH keys

On a client device, run

```bash
eval `ssh-agent`
ssh-keygen -t ed25519-sk -C <your email address>
```

Add SSH public key to the server device:

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub username@node.ip.address
ssh-add ~/.ssh/id_ed25519
```

Alternatively, create the **.ssh** folder and **authorized\_keys** file on the server and copy the SSH public key into the following file.

```bash
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
```

#### Disable remote password login

Run the following command to open up the SSH server configuration file.

<pre class="language-bash"><code class="lang-bash"><strong>sudo nano /etc/ssh/sshd_config
</strong></code></pre>

1. Uncomment `#AuthorizedKeysFile` if it is commented (by removing the `#` in front of it)
2. Change `KbdInteractiveAuthentication yes` to `KbdInteractiveAuthentication no` and uncomment (by removing the `#` in front of it) - **note that older versions of SSH call this option `ChallengeResponseAuthentication` instead of `KbdInteractiveAuthentication`**
3. Change `PasswordAuthentication yes` to `PasswordAuthentication no` and uncomment (by removing the `#` in front of it)
4. Change `PermitRootLogin yes` to `PermitRootLogin prohibit-password` unless it's already set to that and has a `#` in front of it

Once you're done, save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`.

Now we restart the SSH server so it registers the new settings:

```bash
sudo systemctl restart sshd
```
