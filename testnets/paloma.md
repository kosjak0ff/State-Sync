---
description: paloma
cover: ../.gitbook/assets/photo_2022-05-03_22-34-27.jpg
coverY: 0
---

# Paloma

_for version <mark style="color:red;">v0.1.0-alpha</mark>_

stop the node and reset (this command is for palomad launched as a service)

```bash
sudo systemctl stop palomad && palomad unsafe-reset-all
```

<mark style="background-color:red;">if you get error, try:</mark>

_<mark style="background-color:red;">palomad tendermint unsafe-reset-all</mark>_

set variable to bonded.zone RPC

```bash
RPC="http://rpc2.bonded.zone:21557"
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
peers="1003cf3b68ddfd3a55bb20f5c6041c1efe2e52eb@rpc2.bonded.zone:21556"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.paloma/config/config.toml
```

this is one command

```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$RPC,$RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.paloma/config/config.toml
```

start the node (this command is for palomad launched as a service)

```bash
sudo systemctl restart palomad
```

check logs

```bash
sudo journalctl -u palomad -f -o cat
```
