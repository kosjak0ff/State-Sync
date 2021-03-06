---
description: killerqueen-1
cover: https://pbs.twimg.com/profile_banners/1471200836504199168/1643792144/1500x500
coverY: -215.9533073929961
---

# Quicksilver

_for version <mark style="color:red;">v0.4.2</mark>_

stop the node and reset (this command is for quicksilverd launched as a service)

```bash
sudo systemctl stop quicksilverd && quicksilverd unsafe-reset-all
```

set variable to bonded.zone RPC

```bash
RPC="http://rpc2.bonded.zone:20557"
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
peers="13efad5e98f92b7fa1a1a24a1fc3aae1e7c3321f@rpc2.bonded.zone:20556"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.quicksilverd/config/config.toml
```

this is one command

```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$RPC,$RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.quicksilverd/config/config.toml
```

start the node (this command is for quicksilverd launched as a service)

```bash
sudo systemctl restart quicksilverd
```

check logs

```bash
sudo journalctl -u quicksilverd -f --no-hostname -o cat
```
