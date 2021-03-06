---
description: STRIDE
cover: https://pbs.twimg.com/profile_banners/1519990048497754113/1655747456/1500x500
coverY: 105.05836575875486
---

# Stride

stop the node and reset (this command is for strided launched as a service)

```bash
sudo systemctl stop strided && strided unsafe-reset-all
```

<mark style="background-color:red;">if you get error, try:</mark>

_<mark style="background-color:red;">strided tendermint unsafe-reset-all</mark>_

set variable to bonded.zone RPC

```bash
RPC="http://rpc2.bonded.zone:20157"
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
peers="520632a028527a00f4147ea955edd54484e1ba74@rpc2.bonded.zone:20156"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.stride/config/config.toml
```

this is one command

```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$RPC,$RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.stride/config/config.toml
```

start the node (this command is for strided launched as a service)

```bash
sudo systemctl restart strided
```

check logs

```bash
sudo journalctl -u strided -f -o cat
```
