---
description: >-
  eth1 nodes grow quickly. Run this process to prune the blockchain and free up
  space.
---

# How to free up eth1 node disk space with pruning

## Quick steps guide

{% hint style="info" %}
The following steps align with our [mainnet guide](./). You may need to adjust file names and directory locations where appropriate. The core concepts remain the same.
{% endhint %}

### 🗡 Why do I want to prune my eth1 node?

* Free up gigs of precious disk space. Typically 200gb or more is common.

### 🤖 Pre-requisites

* Works with **geth** currently
* Ensure at least 30+ GB of free disk space is available otherwise database corruption may occur.

### 🚧 How to prune a eth1 node

{% hint style="info" %}
Ensure adequate failover or backup eth1 nodes are configured for use with your beacon-chain node before proceeding. geth is offline and unavailable during this process.
{% endhint %}

1. Note how much disk space is currently used and stop the eth1 node

```text
df
sudo service eth1 stop
```

2. Backup your current eth1 unit file

```bash
cp /etc/systemd/system/eth1.service /etc/systemd/system/eth1.service_backup
```

3. Add the prune command to the ExecStart line.

{% tabs %}
{% tab title="Geth" %}
```bash
cat > $HOME/eth1.service << EOF 
[Unit]
Description     = geth eth1 service
Wants           = network-online.target
After           = network-online.target 

[Service]
User            = $(whoami)
ExecStart       = /usr/bin/geth snapshot prune-state
Restart         = on-failure
RestartSec      = 3
TimeoutSec      = 300

[Install]
WantedBy    = multi-user.target
EOF
```
{% endtab %}
{% endtabs %}

4. Move the unit file to /etc/systemd/system, give it proper permissions and reload.

```bash
sudo mv $HOME/eth1.service /etc/systemd/system/eth1.service
```

```bash
sudo chmod 644 /etc/systemd/system/eth1.service
sudo systemctl daemon-reload
```

5. Start the pruning process and monitor it's process.

{% hint style="warning" %}
🔥 **Pruning Caveats**: 

* Pruning can take a few hours or longer depending on your node's disk speed performance.
{% endhint %}

```bash
sudo service eth1 start
journalctl -fu eth1
```

6. Once the pruning is finished, restore the original service file and restart the eth1 service.

```bash
mv /etc/systemd/system/eth1.service_backup /etc/systemd/system/eth1.service
sudo systemctl daemon-reload
sudo service eth1 restart
```

7. Compare the disk space of the node after pruning.

```bash
df
```

{% hint style="success" %}
Nice work. Enjoy the extra disk breathing room.
{% endhint %}

##  🤖 Start staking by building a validator <a id="start-staking-by-building-a-validator"></a>

### Visit here for our [Mainnet guide](https://www.coincashew.com/coins/overview-eth/guide-or-how-to-setup-a-validator-on-eth2-mainnet) and here for our [Testnet guide](https://www.coincashew.com/coins/overview-eth/guide-or-how-to-setup-a-validator-on-eth2-testnet). <a id="visit-here-for-our-mainnet-guide-and-here-for-our-testnet-guide"></a>

{% hint style="success" %}
Congrats on completing the guide. ✨

Did you find our guide useful? Send us a signal with a tip and we'll keep updating it.

It really energizes us to keep creating the best crypto guides.

Use [cointr.ee to find our donation](https://cointr.ee/coincashew) addresses. 🙏

Any feedback and all pull requests much appreciated. 🌛

Hang out and chat with fellow stakers on Discord @

​[https://discord.gg/w8Bx8W2HPW](https://discord.gg/w8Bx8W2HPW) 😃
{% endhint %}

