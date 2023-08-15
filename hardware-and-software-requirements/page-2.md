# Preparing your virtual machine

## Create a new VM

Go to your google cloud console and create a new VM.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Use the default settings for the region, zone, and instance type (E2-medium). Change the name of the server if you like.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

&#x20;Under **Boot disk**, click on the **"change"** button.

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Select `Ubuntu` for the operating system, `Ubuntu 22.04 LTS x86` for the version, `Balanced persistent disk` for the boot disk type, and `32 GB` for the size of the storage.

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Once you are done, click on the **"Create"** button at the bottom of the screen.

**Expected output:** You should see your VM instance coming online after loading for a few seconds

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

&#x20;Click on the dropdown beside the **"SSH"** column and select **"Open in browser window".** Click on **"Authorize"** when prompted.

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

**Expected output:** Wait for the new window to load and your Ubuntu terminal will appear.

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>
