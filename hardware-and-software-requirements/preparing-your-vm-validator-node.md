# Preparing your VM - Validator node

## Cost considerations

Because the estimated monthly running costs on Google Cloud from just the validator node is **USD615,** the economics will not make sense if you are planning on just running a single validator.&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2023-08-16 at 5.55.10 PM.png" alt=""><figcaption></figcaption></figure>

A single validator generating 6.0% yield at ETH prices of USD1800 will give you USD3456 per year, or USD288 per month. This means you will need to run at least 3 validators by depositing 96 ETH for this method to make sense economically.

## Create a new VM

Go to your google cloud console and create a new VM.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Choose the following settings:

1. **Name:** Choose your own name or leave it as the default setting
2. **Region:** Choose your preferred region but it is recommended to diversify away from the popular regions (e.g. US, EU) to minimise the risk of <mark style="color:red;">correlated downtime.</mark> I am going with Singapore in this example.
3. **Zone:** Choose any
4. **Machine configuration:** E2
5. **Machine type:** e2-standard-8, 32GB

<figure><img src="../.gitbook/assets/Screenshot 2023-08-16 at 5.49.17 PM.png" alt=""><figcaption></figcaption></figure>

&#x20;Under **Boot disk**, click on the **"change"** button.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Select `Ubuntu` for the operating system, `Ubuntu 22.04 LTS x86` for the version, `SSD persistent disk` for the boot disk type, and `2000 GB` for the size of the storage.

<figure><img src="../.gitbook/assets/Screenshot 2023-08-16 at 5.52.13 PM.png" alt=""><figcaption></figcaption></figure>

Once you are done, click on the **"Create"** button at the bottom of the screen.

**Expected output:** You should see your VM instance coming online after loading for a few seconds

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

&#x20;Click on the dropdown beside the **"SSH"** column and select **"Open in browser window".** Click on **"Authorize"** when prompted.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

**Expected output:** Wait for the new window to load and your Ubuntu terminal will appear.

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

You are now logged in to your validator node server and can begin [installing & configuring your validator clients](broken-reference) if you do not want to use an external signer.
