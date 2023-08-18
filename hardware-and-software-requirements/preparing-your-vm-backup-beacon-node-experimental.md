# Preparing your VM - Backup beacon node (Experimental)

## Disclaimer

Using the Google Blockchain Node Engine (BNE) as a backup / failover beacon node is an experimental method and should not yet be used for production setups.

Validator clients should theoretically be able to connect to the BNE's beacon API endpoint securely by using an API key generated from the Google Cloud Console. However, there does not seem to be any way to configure the use of custom API keys for this purpose on our preferred consensus layer client, Teku.

Naturally, I could be wrong so I will be happy to edit this section if anyone manages to figure this out from where I left off.

## Provisioning the VM

Go to your Google Cloud console and search for **"Blockchain node engine".**

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 3.12.44 PM.png" alt=""><figcaption></figcaption></figure>

Click on **"Create"**, set a name for your node, and then choose the following settings. **Note:** The Google blockchain node engine only offers the Geth/Lighthouse client pair for full nodes.

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 3.14.35 PM.png" alt="" width="375"><figcaption></figcaption></figure>

Select your preferred location - preferably outside of US and EU, which leaves us with Taiwan for now.

Once you are done, click on the "Create" button again and you should see your blockchain node engine being set up.

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 3.17.32 PM.png" alt=""><figcaption></figcaption></figure>

## Finding your beacon node endpoint

Click into your backup beacon node and copy the **"Beacon API Endpoint"** URL. This will be used during your Teku setup of your main validator node.&#x20;

<figure><img src="../.gitbook/assets/Screenshot 2023-08-17 at 6.04.48 PM.png" alt="" width="375"><figcaption></figcaption></figure>

## Generate your API key

Now that our backup node is up and running, we need to generate an API key so that our validator client can connect to it securely and preventing random anons from connecting to it.

Search for **"API and services"** in the search bar, select **"Credentials"** on the left hand panel, and click **"CREATE CREDENTIALS" >> "API keys"**

<figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

You will then see your API key being created. Keep this key secure.

Next test your connection by running the following:

```bash
curl -X GET https://<Beacon_API_Endpoint>/eth/v1/beacon/states/head?key=<API_key> -H  "accept: application/json" | jq
```

If you receive a response similar to the output below, you can now theoretically connect your validator client to your backup node.

<figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

