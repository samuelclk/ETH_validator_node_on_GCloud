# Preparing your VM - External signer

## Create a new VM

Go to your google cloud console and create a new VM.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Choose the following settings:

1. **Name:** Choose your own name or leave it as the default setting
2. **Region:** Choose your preferred region but it is recommended to diversify away from the popular regions (e.g. US, EU) to minimise the risk of <mark style="color:red;">correlated downtime.</mark> I am going with Singapore in this example.
3. **Zone:** Choose the same zone as your main validator node
4. **Machine configuration:** E2
5. **Machine type:** e2-medium, 4GB memory

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 3.37.03 PM.png" alt=""><figcaption></figcaption></figure>

&#x20;Under **Boot disk**, click on the **"change"** button.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Select `Ubuntu` for the operating system, `Ubuntu 22.04 LTS x86` for the version, Balanced `persistent disk` for the boot disk type, and 32 `GB` for the size of the storage.

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 3.45.57 PM.png" alt=""><figcaption></figcaption></figure>

Scroll down and expand the "Advanced Options" dropdown, then look for the "Network Interfaces" sub-section. Select the dedicated VPC your created in an [earlier sub-section](create-a-dedicated-virtual-private-cloud-vpc.md). Reserve static addresses for both your internal and external IPv4 addresses.

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 5.10.51 PM.png" alt="" width="375"><figcaption></figcaption></figure>

Once you are done, click on the **"Create"** button at the bottom of the screen.

**Expected output:** You should see your VM instance coming online after loading for a few seconds

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 3.47.33 PM.png" alt=""><figcaption></figcaption></figure>

&#x20;Click on the dropdown beside the **"SSH"** column and select **"Open in browser window".** Click on **"Authorize"** when prompted.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

**Expected output:** Wait for the new window to load and your Ubuntu terminal will appear.

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 3.50.32 PM.png" alt=""><figcaption></figcaption></figure>

You are now logged in to your external signer server and can now [configure your external signer](../installing-and-configuring-your-validator-clients/set-up-and-configure-consensus-layer-client-teku/with-external-signer-web3signer/setup-and-configure-an-external-signer/).
