---
description: atlantic-1
cover: https://pbs.twimg.com/profile_banners/1515104342906327045/1655924054/1500x500
coverY: 17.509727626459142
---

# Sei

for _version <mark style="color:red;">v1.2.0beta</mark>_

set RPC variable to bonded.zone RPC

```bash
RPC="http://wisdom.bonded.zone:28057"
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
peers="3f6e68bd476a7cd3f491105da50306f8ebb74643@wisdom.bonded.zone:28056"
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

stop and reset the node (this command is for seid launched as a service)

```bash
sudo systemctl stop seid && seid tendermint unsafe-reset-all --home $HOME/.sei --keep-addr-book
```

start the node and check logs (this command is for seid launched as a service)

```bash
sudo systemctl restart seid && sudo journalctl -u seid -f -o cat
```
