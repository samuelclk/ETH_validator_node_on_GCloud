# Create a dedicated virtual private cloud (VPC)

Go to your Google Cloud Console and search for **"VPC networks".** Then create VPC network.

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 4.31.11 PM.png" alt=""><figcaption></figcaption></figure>

Configure the following settings:

1. **Name:** any
2. **Description:** optional
3. **VPC network IPv6:** Disabled
4. **Subnet creation mode:** Custom
5. **New subnet name:** any
6. **New subnet description:** optional
7. **New subnet region:** asia-southeast1
8. **IPv4 (single stack)**
9. **IPv4 range:** 192.168.0.0/24

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 4.57.07 PM.png" alt=""><figcaption></figcaption></figure>

Scroll all the way down to the "**Firewall rules"** section and click into **"xx-xx-allow-custom".** Change the **"Source IPv4 ranges"** to `0.0.0.0/0` and hit **"Save".**

<figure><img src="../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>



Then, check all of the boxes and click **"Create".**

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 5.00.56 PM.png" alt=""><figcaption></figcaption></figure>

