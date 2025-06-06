<meta http-equiv="refresh" content="0; url=/0g-labs/storage-node-v3-chain">


# Storage Node Update V2 to V3

This guide will help you set up a Storage Node for OG Labs.
For official documentation, check [here](https://docs.0g.ai/run-a-node/storage-node).

Version: `v1.0.0`

## One-Click Command
```bash
bash <(wget -qO- https://astrostake.xyz/upgrade_storage_node_v3.sh)
```

## Manual Install

1. **Stop Service**

```bash
systemctl stop zgs
```

2. **Remove db folder**
```bash
rm -rf $HOME/0g-storage-node/run/db
```

3. **Backup Config**

```bash
cp $HOME/0g-storage-node/run/config.toml $HOME/zgs-config.toml.backup
```
4. **Update to v1.0.0**
```bash
cd $HOME/0g-storage-node
git stash
git fetch --all --tags
git checkout v1.0.0
git submodule update --init
cargo build --release
```

5. **Download V3 Config**

```bash
rm -rf $HOME/0g-storage-node/run/config.toml
curl -o $HOME/0g-storage-node/run/config.toml https://vault.astrostake.xyz/0g-labs/config-v3.toml
```

check `miner_key` and input your private key

```bash
nano $HOME/0g-storage-node/run/config.toml
```

6. **Delete and Create New Service**
```bash
sudo rm -f /etc/systemd/system/zgs.service
```
```bash
sudo tee /etc/systemd/system/zgs.service > /dev/null <<EOF
[Unit]
Description=ZGS Node
After=network.target

[Service]
User=$USER
WorkingDirectory=$HOME/0g-storage-node/run
ExecStart=$HOME/0g-storage-node/target/release/zgs_node --config $HOME/0g-storage-node/run/config.toml
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

7. **Restart Service**

```bash
sudo systemctl daemon-reload && sudo systemctl enable zgs && sudo systemctl start zgs
```

## Useful Command

Check full logs
```bash
tail -f ~/0g-storage-node/run/log/zgs.log.$(TZ=UTC date +%Y-%m-%d)
```

Check block & peers
```bash
source <(curl -s https://astrostake.xyz/check_block.sh)
```

