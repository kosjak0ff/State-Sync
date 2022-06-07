---
description: bitcanna-1
cover: ../.gitbook/assets/1500x500.jfif
coverY: 0
---

# Bitcanna

_for version <mark style="color:red;">v.1.3.1</mark>_

stop the node and reset (this command is for bcnad launched as a service)

```bash
sudo systemctl stop bcnad && bcnad unsafe-reset-all
```

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
peers="95dc343933ff76c052c089420494de33e1ce5660@rpc2.bonded.zone:20156"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.bcna/config/config.toml
```

this is one command

```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$RPC,$RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.bcna/config/config.toml
```

start the node (this command is for bcnad launched as a service)

```bash
sudo systemctl restart bcnad
```

check logs

```bash
sudo journalctl -u bcnad -f --no-hostname -o cat
```
