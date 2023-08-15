# Validator key generation

## Important

* It is highly recommended that you perform this step using an air-gapped machine - i.e. a device that has never connected to the public internet before. We will describe a few methods below.
* If this is not available, turn off all internet and wireless connection (e.g. Ethernet, WiFi, Bluetooth) before proceeding with the key generation step
* In both cases above, make sure you are in a safe environment (e.g. home or office) with a trusted WiFi network for building the validator key generation tool from source. Make sure to also physically block all camera devices - e.g. laptop cameras, Webcams, people standing behind you during this process

## Creating an air-gapped machine

1. The most non-technical way is to buy a cheap single board computer like the Raspberry Pi from official distributors for less than S$100 SGD
2. **"OS-on-a-stick":** For more technical workarounds, we can flash a new USB drive with either Ubuntu or TailsOS and run a completely fresh OS from this USB drive itself. This system will be completely isolated from your host device (e.g. working laptop) and the described method below will not store any files after you remove the USB drive
3. **"Throwaway" VM:** Spin up a completely new virtual machine on a cloud provider (e.g. Google Cloud Platform) and destroy/delete it immediately after

#### _**We will cover methods 2 and 3 for this guide.**_&#x20;
