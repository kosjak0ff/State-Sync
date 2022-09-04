---
description: haqq_53211-1
cover: https://pbs.twimg.com/profile_banners/1518783995101724672/1651040481/1500x500
coverY: -65.1750972762646
---

# Haqq

_for version <mark style="color:red;">1.0.3</mark>_

set RPC variable to bonded.zone RPC

```bash
RPC="http://wisdom.bonded.zone:26857"
```

set variables $LATEST\_HEIGHT $BLOCK\_HEIGHT $TRUST\_HASH

this is one command

```bash
LATEST_HEIGHT=$(curl -s $RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
```

check variables

```bash
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
```

if output is something like this one, then continue to the next step

1769362 1768362 311AFA4925B213B366879589640F4CA03CCB58841DBE2CE85A7FC1D19BBEB909

configure persistent peers

```bash
peers="bf76464aacf4e4b15dec3b230fdc322f9222cb87@wisdom.bonded.zone:26856"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.haqqd/config/config.toml
```

this is one command

```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$RPC,$RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.haqqd/config/config.toml
```

stop and reset the node (this command is for haqqd launched as a service)

```bash
sudo systemctl stop haqqd && haqqd tendermint unsafe-reset-all --home $HOME/.haqqd --keep-addr-book
```

start the node and check logs (this command is for haqqd launched as a service)

```bash
sudo systemctl restart haqqd && sudo journalctl -u haqqd -f -o cat
```
