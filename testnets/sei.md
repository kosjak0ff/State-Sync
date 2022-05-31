---
description: sei-testnet-1
---

# Sei

<mark style="color:red;">**Attention!**</mark>\ <mark style="color:red;">**With state-synced validator may not work cosmwasm txs!**</mark>\ <mark style="color:red;">**Need to download updated wasm folder**</mark>

stop the node and reset (this command is for seid launched as a service)

```bash
sudo systemctl stop seid && seid unsafe-reset-all
```

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

1769362 1768362 311AFA4925B213B366879589640F4CA03CCB58841DBE2CE85A7FC1D19BBEB909

configure persistent peers

```bash
peers="3547e9f19336a85533b4dd087165a13d5a9921f0@rpc2.bonded.zone:21156"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.sei-chain/config/config.toml
```

this is one command

```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$RPC,$RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.sei-chain/config/config.toml
```

start the node (this command is for seid launched as a service)

```bash
sudo systemctl restart seid
```

check logs

```bash
sudo journalctl -u seid -f --no-hostname -o cat
```