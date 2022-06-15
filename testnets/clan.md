---
description: playstation-2
---

# Clan

_for version <mark style="color:red;">v1.0.4-alpha</mark>_

stop the node and reset (this command is for cland launched as a service)

```bash
sudo systemctl stop cland && cland unsafe-reset-all
```

<mark style="background-color:red;">if you get error, try:</mark>

_<mark style="background-color:red;">cland tendermint unsafe-reset-all</mark>_

set variable to bonded.zone RPC

```bash
RPC="http://rpc2.bonded.zone:21657"
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
peers="82302975bbf697fe24e6d2d7534fa17bb98c1516@rpc2.bonded.zone:21656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.clan/config/config.toml
```

this is one command

```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$RPC,$RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.clan/config/config.toml
```

start the node (this command is for cland launched as a service)

```bash
sudo systemctl restart cland
```

check logs

```bash
sudo journalctl -u cland -f -o cat
```
