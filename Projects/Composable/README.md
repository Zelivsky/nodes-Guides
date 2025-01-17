<!-- START_TABLE -->
| 🛡Trusted Delegations🛡 | Token price🧲 | 💰Result in USD💰 |
|-------------|---------|---------------|
| 16627715.395 | 0.00643275 | 106961.936210773237 |

<!-- END_TABLE -->


[🔥OUR VALIDATOR🔥](https://restake.app/composable/centaurivaloper180wngzau7jzdw9xdqp0a4mm7740y5rfzar2p69)
=

# Composable MAINNET guide
![compo](https://github.com/obajay/nodes-Guides/assets/44331529/49502f93-cb03-461e-b788-78a391456f72)

[WebSite](https://www.composable.finance/)\
[GitHub](https://github.com/notional-labs/composable-networks)
=
[EXPLORER 1](https://explorer.stavr.tech/Composable-Mainnet) \
[EXPLORER 2](https://explorer.indonode.net/composable)
=

- **Minimum hardware requirements**:

| Node Type |CPU | RAM  | Storage  | 
|-----------|----|------|----------|
| Mainnet   |   8| 16GB | 250GB    |

# 1) Auto_install script
```python
wget -O centauridm https://raw.githubusercontent.com/obajay/nodes-Guides/main/Projects/Composable/centauridm && chmod +x centauridm && ./centauridm
```

# 2) Manual installation

### Preparing the server
```python
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```

## GO 1.21.4
```python
ver="1.21.4"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
go version
```

# Build 05.04.24
```python
cd $HOME
git clone https://github.com/notional-labs/composable-centauri
cd composable-centauri
git checkout v6.5.30
make install
```
*******🟢UPDATE🟢******* 05.04.24
```python
cd $HOME/composable-centauri
git pull
git checkout v6.5.30
make install
centaurid version --long | grep -e commit -e version
#version: v6.5.30
#commit: 0e1eb20bafce1c2c09cfdd39b28d4d7b40365d9d
sudo systemctl restart centaurid && sudo journalctl -u centaurid -f -o cat

```

`centaurid version --long`
- version: v6.5.30
- commit: 0e1eb20bafce1c2c09cfdd39b28d4d7b40365d9d

```python
centaurid init STAVR_guide --chain-id centauri-1
centaurid config chain-id centauri-1
```    

## Create/recover wallet
```python
centaurid keys add <walletname>
  OR
centaurid keys add <walletname> --recover
```

## Download Genesis
```python
wget -O ~/.banksy/config/genesis.json https://raw.githubusercontent.com/obajay/nodes-Guides/main/Projects/Composable/genesis.json
```
`sha256sum $HOME/.banksy/config/genesis.json`
+ ae5ff9e5807c78ddc745a683f197d374959d7c15bc7fd1307208083fa9820335

## Set up the minimum gas price and Peers/Seeds/Filter peers/MaxPeers
```python
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0ppica\"/;" ~/.banksy/config/app.toml
external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.banksy/config/config.toml
peers="4cb008db9c8ae2eb5c751006b977d6910e990c5d@65.108.71.163:2630,63559b939442512ed82d2ded46d02ab1021ea29a@95.214.55.138:53656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.banksy/config/config.toml
seeds="c7f52f81ee1b1f7107fc78ca2de476c730e00be9@65.109.80.150:2635"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.banksy/config/config.toml
sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 50/g' $HOME/.banksy/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 50/g' $HOME/.banksy/config/config.toml

```
### Pruning (optional)
```python
pruning="custom"
pruning_keep_recent="1000"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.banksy/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.banksy/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.banksy/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.banksy/config/app.toml
```
### Indexer (optional) 
```bash
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.banksy/config/config.toml
```

## Download addrbook
```python
wget -O $HOME/.banksy/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Projects/Composable/addrbook.json"
```


# Create a service file
```python
tee /etc/systemd/system/centaurid.service > /dev/null <<EOF
[Unit]
Description=centaurid
After=network-online.target

[Service]
User=$USER
ExecStart=$(which centaurid) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

# StateSync Composable Mainnet
```python
SNAP_RPC=https://composable.rpc.m.stavr.tech:443
peers="aa6398f9644e98fa3d04f7dbdd7740c995eb0530@composable.seed.stavr.tech:20306"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.banksy/config/config.toml
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.banksy/config/config.toml
centaurid tendermint unsafe-reset-all --home /root/.banksy
wget -O $HOME/.banksy/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Projects/Composable/addrbook.json"
curl -o - -L https://composable.wasm.stavr.tech/wasm-composable.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.banksy --strip-components 2
systemctl restart centaurid && journalctl -fu centaurid -o cat
```
# SnapShot Mainnet updated every 5 hours  
```python
cd $HOME
apt install lz4
sudo systemctl stop centaurid
cp $HOME/.banksy/data/priv_validator_state.json $HOME/.banksy/priv_validator_state.json.backup
rm -rf $HOME/.banksy/data
curl -o - -L https://composable.snapshot.stavr.tech/centauri-snap.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.banksy --strip-components 2
curl -o - -L https://composable.wasm.stavr.tech/wasm-composable.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.banksy --strip-components 2
mv $HOME/.banksy/priv_validator_state.json.backup $HOME/.banksy/data/priv_validator_state.json
wget -O $HOME/.banksy/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Projects/Composable/addrbook.json"
sudo systemctl restart centaurid && journalctl -fu centaurid -o cat
```



## Start
```python
sudo systemctl daemon-reload
sudo systemctl enable centaurid
sudo systemctl restart centaurid && sudo journalctl -u centaurid -f -o cat
```

### Create validator
```python
centaurid tx staking create-validator \
--commission-rate 0.1 \
--commission-max-rate 1 \
--commission-max-change-rate 1 \
--min-self-delegation "1" \
--amount 1000000ppica \
--pubkey $(centaurid tendermint show-validator) \
--from <wallet> \
--moniker="STAVR_guide" \
--chain-id centauri-1\
--gas 350000 \
--identity="" \
--website="" \
--details="" -y
```

## Delete node
```python
sudo systemctl stop centaurid
sudo systemctl disable centaurid
rm /etc/systemd/system/centaurid.service
sudo systemctl daemon-reload
cd $HOME
rm -rf composable-centauri
rm -rf .banksy
rm -rf $(which centaurid)
```

[🧩Services and Tools🧩](https://github.com/obajay/StateSync-snapshots/tree/main/Projects/Composable)
=

#
### Sync Info
```python
centaurid status 2>&1 | jq .SyncInfo
```
### NodeINfo
```python
centaurid status 2>&1 | jq .NodeInfo
```
### Check node logs
```python
sudo journalctl -u centaurid -f -o cat
```
### Check Balance
```python
centaurid query bank balances banksy...addressjkl1yjgn7z09ua9vms259j
```
