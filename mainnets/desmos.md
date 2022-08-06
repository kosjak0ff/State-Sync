---
description: desmos-mainnet
cover: https://pbs.twimg.com/profile_banners/1117333487390879744/1646197127/1500x500
coverY: 0
---

# Desmos

for _version <mark style="color:red;">v2.3.1</mark>_

set variable to bonded.zone RPC

```bash
RPC="http://wisdom.bonded.zone:21157"
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
peers="007522c5b2d036549d02cc407027ea8beccfc149@wisdom.bonded.zone:21156"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.desmos/config/config.toml
```

this is one command

```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$RPC,$RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.desmos/config/config.toml
```

stop and reset the node(this command is for desmosd launched as a service)

```bash
sudo systemctl stop desmos && desmos unsafe-reset-all
```

start the node and check logs(this command is for desmosd launched as a service)

```bash
sudo systemctl restart desmos && sudo journalctl -u desmos -f -o cat
```
