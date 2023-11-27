## Install

```bash
cd $HOME
sudo apt update && sudo apt upgrade -y
sudo apt install make clang pkg-config libssl-dev build-essential git jq ncdu bsdmainutils htop -y < "/dev/null"
```
```bash
cd $HOME
wget -O go.tar.gz https://go.dev/dl/go$VERSION.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go.tar.gz && rm go.tar.gz
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile && . $HOME/.bash_profile
go version
```

```bash
cd $HOME
wget https://share.utsa.tech/selfchain/selfchaind
chmod +x selfchaind
mv selfchaind /usr/local/bin/selfchaind
```

```bash
selfchaind init <<node_name>> --chain-id self-dev-1
selfchaind config chain-id self-dev-1

wget -O $HOME/.selfchain/config/genesis.json "https://raw.githubusercontent.com/hotcrosscom/selfchain-genesis/main/networks/devnet/genesis.json"
```

```bash
pruning="custom"
pruning_keep_recent="1000"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.selfchain/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.selfchain/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.selfchain/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.selfchain/config/app.toml
```

```bash
echo "[Unit]
Description=selfchain
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which selfchaind) start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/selfchaind.service
sudo mv $HOME/selfchaind.service /etc/systemd/system
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF
```

```bash
sudo systemctl restart systemd-journald
sudo systemctl daemon-reload
sudo systemctl enable selfchaind 

sudo systemctl restart selfchaind
```

```bash
selfchaind keys add <<wallet_name>>
```
```bash
selfchaind keys add <<wallet_name>> --recover
```
```bash
curl -s localhost:26657/status | jq .result.sync_info.catching_up
```

```bash
selfchaind q bank balances <<address>>
```

```bash
selfchaind tx staking create-validator \
  --amount=1000000uself \
  --pubkey=$(selfchaind tendermint show-validator) \
  --moniker="<<node_name>>" \
  --details="" \
  --identity="" \
  --website="" \
  --chain-id="self-dev-1" \
  --commission-rate="0.10" \
  --commission-max-rate="0.50" \
  --commission-max-change-rate="0.2" \
  --min-self-delegation="1" \
  --from=<<wallet_name>>
  -y
```
