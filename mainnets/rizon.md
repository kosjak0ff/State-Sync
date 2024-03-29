---
description: titan-1
cover: https://pbs.twimg.com/profile_banners/890466481942675456/1649319780/1500x500
coverY: 0
---

# Rizon

for _version <mark style="color:red;">v0.4.0</mark>_

set variable to bonded.zone RPC

```bash
RPC="http://wisdom.bonded.zone:24457"
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
peers="8bb5c158e167305517733efda61082761ef9f555@wisdom.bonded.zone:24456"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.rizon/config/config.toml
```

this is one command

```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$RPC,$RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.rizon/config/config.toml
```

stop and reset the node (this command is for rizond launched as a service)

```bash
sudo systemctl stop rizond && rizond unsafe-reset-all
```

start the node and check logs (this command is for rizond launched as a service)

```bash
sudo systemctl restart rizond && sudo journalctl -u rizond -f -o cat
```
