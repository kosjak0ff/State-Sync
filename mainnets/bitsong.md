---
description: bitsong-2b
cover: https://pbs.twimg.com/profile_banners/949260226829660162/1643823516/1500x500
coverY: 94.35797665369651
---

# Bitsong

for _version <mark style="color:red;">v0.10.0</mark>_

stop the node and reset (this command is for bitsongd launched as a service)

```bash
sudo systemctl stop bitsongd && bitsongd unsafe-reset-all
```

set variable to bonded.zone RPC

```bash
RPC="http://wisdom.bonded.zone:26957"
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
peers="2af83a42fc643ddeeb5a1a10afc70fe0fa7e9201@wisdom.bonded.zone:26956"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.bitsongd/config/config.toml
```

this is one command

```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$RPC,$RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.bitsongd/config/config.toml
```

start the node (this command is for bitsongd launched as a service)

```bash
sudo systemctl restart bitsongd
```

check logs

```bash
sudo journalctl -u bitsongd -f --no-hostname -o cat
```
