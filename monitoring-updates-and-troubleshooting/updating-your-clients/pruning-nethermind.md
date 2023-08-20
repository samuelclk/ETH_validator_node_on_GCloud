# Pruning Nethermind

## Activating pruning mode

Your ETH validator node will use up the available disk space over time as the state grows. In order to avoid out-of-storage errors, it is advisable to prune your execution layer clients periodically (EL clients take up the majority of disk usage).

To enable the pruning process for Nethermind, open up the systemd configuration file:

```bash
sudo nano /etc/systemd/system/nethermind.service
```

&#x20;and append the following flags into the `[Service]` section of the file.

```bash
[Service]
<existing_flags> \
--Pruning.Mode=Hybrid \
--Pruning.FullPruningTrigger=VolumeFreeSpace \
--Pruning.FullPruningThresholdMb=300000
```

This will instruct Nethermind to activate its pruning mechanism once the amount of available free space on your disk falls below 300GB.

_**Note:** The recommended threshold is 250GB but lets be a little more prudent._

Save with `Ctrl+O` and `Enter`, then exit with `Ctrl+X`.

Restart the daemon and the Nethermind service.&#x20;

```
sudo systemctl daemon-reload
sudo systemctl restart nethermind.service
sudo systemctl status nethermind.service
```

**Expected output:** The status should say Nethermind is **"active (running)".**

## Monitoring pruning progress

If you have configured the pruning mode correctly, you should see the following logs&#x20;

**At initiation:**

> Full Pruning Ready to start: pruning garbage before state BLOCK\_NUMBER with root ROOT\_HASH.\
> **WARN**: Full Pruning Started on root hash ROOT\_HASH: do not close the node until finished or progress will be lost.

_**\*As the warning states, do not restart your node from here on until the pruning process is completed.** Else you will have to restart the whole pruning process, or worse, end up with a corrupted database._&#x20;

After a few minutes, you will start to see some progress logs:

> Full Pruning In Progress: 00:00:57.0603307 1.00 mln nodes mirrored.\
> Full Pruning In Progress: 00:01:40.3677103 2.00 mln nodes mirrored.\
> Full Pruning In Progress: 00:02:25.6437030 3.00 mln nodes mirrored.

When the pruning process is completed, you will see the following output:

> Full Pruning Finished: 15:25:59.1620756 1,560.29 mln nodes mirrored.

## Tips

The pruning process can take **more than 30 hours** to complete (depending on CPU and IO speeds). During this time, you may experience degraded performance on your validator node - i.e. missing \~10% of attestations.&#x20;

Hence, it is important to time your pruning schedule to avoid coinciding with your scheduled sync committee or block proposer duties. You can check for these below.

* [Check scheduled sync committee duties](https://www.coincashew.com/coins/overview-eth/guide-or-how-to-setup-a-validator-on-eth2-mainnet/part-ii-maintenance/checking-my-eth-validators-sync-committee-duties)
* [Check scheduled block proposal duties](https://wenmerge.com/block-proposer-schedule/)

If you want to trigger the pruning process immediately, set the threshold of the following flag to whatever amount your available disk space is left with.

> `--Pruning.FullPruningThresholdMb=<bytes>`

Run `df -h` on your terminal to find out how much available disk space you have remaining.&#x20;
