---
description: atlantic-1
cover: https://pbs.twimg.com/profile_banners/1515104342906327045/1655924054/1500x500
coverY: 22.373540856031127
---

# Sei

<mark style="color:red;">**Attention!**</mark>\ <mark style="color:red;">**With state-synced validator may not work cosmwasm txs!**</mark>\ <mark style="color:red;">**Need to download updated wasm folder**</mark>

_<mark style="color:red;">for version 1.0.6beta</mark>_

set variable to bonded.zone RPC

```bash
RPC="http://rpc2.bonded.zone:21157"
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

_1769362 1768362 311AFA4925B213B366879589640F4CA03CCB58841DBE2CE85A7FC1D19BBEB909_



stop the node and reset (this command is for seid launched as a service)

```bash
sudo systemctl stop seid && seid unsafe-reset-all
```

<mark style="background-color:red;">if you get error, try:</mark>

_<mark style="background-color:red;">seid tendermint unsafe-reset-all</mark>_

configure persistent peers

```bash
peers="3f6e68bd476a7cd3f491105da50306f8ebb74643@rpc2.bonded.zone:21156"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.sei/config/config.toml
```

this is one command

```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$RPC,$RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.sei/config/config.toml
```

start the node (this command is for seid launched as a service)

```bash
sudo systemctl restart seid
```

check logs

```bash
sudo journalctl -u seid -f --no-hostname -o cat
```
