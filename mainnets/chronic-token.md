---
description: morocco-1
cover: https://pbs.twimg.com/profile_banners/1412867039518244866/1634754375/1500x500
coverY: 0
---

# Chronic Token



stop the node and reset (this command is for chtd launched as a service)

```bash
sudo systemctl stop chtd && chtd unsafe-reset-all
```

set variable to bonded.zone RPC

```bash
RPC="http://rpc.bonded.zone:20157"
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
peers="7827ec36a45233031aab591eb8141fed1353fa63@rpc.bonded.zone:20156"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.cht/config/config.toml
```

this is one command

```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$RPC,$RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.cht/config/config.toml
```

start the node (this command is for chtd launched as a service)

```bash
sudo systemctl restart chtd
```

check logs

```bash
sudo journalctl -u chtd -f --no-hostname -o cat
```
