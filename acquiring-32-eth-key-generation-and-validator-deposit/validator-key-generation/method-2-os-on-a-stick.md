# Method 2: OS-on-a-stick

## Flash and install OS

1\) Download latest Ubuntu OS [here](https://ubuntu.com/download) or TailsOS [here](https://tails.net/install/download/) and follow the respective instructions to verify the checksums of the downloaded file.

2\) Download an ISO flasher (e.g. [BalenaEtcher](https://etcher.balena.io/)) and flash your USB drive with your preferred OS. You may refer to a guide [here](https://ubuntu.com/tutorials/install-ubuntu-desktop#3-create-a-bootable-usb-stick) if needed.

3\) Once your USB drive is flashed with your preferred OS, plug it into your working device and reboot the device to go into the boot menu. Depending on your system, you might need to hold `F2`, `F10`, `F12`, or `ESC` during the rebooting process to bring up the boot menu.

4\) Once you see the boot menu, select the option to boot up from your USB drive instead of your usual storage volume and you should see the following screen.

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

5\) Select `*Try or Install Ubuntu` and then `Try Ubuntu` when you get to the next screen

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

## Building the validator key generation tool from source

Connect your OS-on-a-stick to a trusted WiFi network and fire up the Linux terminal using `Ctrl + Alt + T` .

Install system dependencies - `pip3` & `virtualenv`. _**Note:** Remove the `sudo` prefix for each line if your system returns an error._

```bash
sudo apt-get update -y && sudo apt-get upgrade -y
sudo apt install python3-venv python3-pip
sudo apt install python3-virtualenv
sudo apt-get install git
```

Create a python virtual environment to run the tool under:

```bash
virtualenv venv
source venv/bin/activate
```

Download the git repository of the tool by running. Reference and verify the URL of the git repository [here](https://github.com/ethereum/staking-deposit-cli).

```bash
git clone -b master --single-branch https://github.com/ethereum/staking-deposit-cli.git
```

Install the dependency packages for running the tool:

```bash
python3 setup.py install
pip3 install -r requirements.txt
```

**Expected output:** You should see some progress bars after a few seconds.

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

### \*BEFORE PROCEEDING TO THE NEXT STEP

1. #### TURN OFF YOUR ETHERNET, WIFI, AND BLUETOOTH ACCESS&#x20;
2. #### PHYSICALLY COVER ALL CAMERA DEVICES - e.g. PHONES, WEBCAMS, LAPTOP CAMERAS, PEOPLE STANDING BEHIND YOU

## Generate your validator signing keys

Run the following command to generate your validator keys. Replace `<number>` with the number of validators you want to set up and `<YourWithdrawalAaddress>` with the actual withdrawal address

```bash
python3 ./staking_deposit/deposit.py new-mnemonic --num_validators <number> --chain mainnet --eth1_withdrawal_address <YourWithdrawalAaddress>
```

You will be prompted to key in the following. Select accordingly.

1. Choose your language (for the session)
2. Confirm your execution address (your withdrawal address)
3. Choose the language of your mnemonic word list (seed phrase)
4. Create a password to encrypt your validator signing keystores
5. Confirm password created in step 4

**Expected output:**

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

Next, your mnemonic word list will be generated. Write it down on a piece of paper or notebook  -\***Never store this online or on any device that is connected to the internet**.

&#x20;**Expected output:**

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

Press any key once you have written your mnemonic down and the tool will prompt you to key in your mnemonic in the same order to verify that you have recorded it correctly.

If you typed in your mnemonic correctly, you will be greeted by an ASCII art of a Rhino!

**Expected output:**

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

**There will be 2 files generated.**&#x20;

1. A `keystore-m_<timestamp>.json` file: This is your validator signing keystore that your validator node will use to sign attestations. Keep this file extremely secure.
2. A `deposit_data-<timestamp>.json`: This is the file that links your ETH deposit to your validator. You will only use this once, during the deposit process.

Store both files on a new USB drive. After that, remove the original copy by running:

```
sudo rm -r $HOME/staking-deposit-cli/validator_keys
```

Restart your host device (e.g. working laptop) and remove the OS-on-a-stick. There will not be any persistent memory stored on it.
