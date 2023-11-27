```bash
cd $HOME
sudo apt update && sudo apt upgrade -y
sudo apt install make clang pkg-config libssl-dev build-essential git jq ncdu bsdmainutils htop -y < "/dev/null"
sleep 2s

cd $HOME
wget -O go1.19.1.linux-amd64.tar.gz https://golang.org/dl/go1.19.1.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.19.1.linux-amd64.tar.gz && rm go1.19.1.linux-amd64.tar.gz
echo 'export GOROOT=/usr/local/go' >> $HOME/.bash_profile
echo 'export GOPATH=$HOME/go' >> $HOME/.bash_profile
echo 'export GO111MODULE=on' >> $HOME/.bash_profile
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile && . $HOME/.bash_profile
go version

git clone https://github.com/defund-labs/defund
cd defund
git checkout v0.2.6
make build
sudo mv ./build/defundd /usr/local/bin/defundd

defundd init $Node_Name --chain-id orbit-alpha-1
curl -s https://raw.githubusercontent.com/defund-labs/testnet/main/orbit-alpha-1/genesis.json > ~/.defund/config/genesis.json

seeds="f902d7562b7687000334369c491654e176afd26d@170.187.157.19:26656,2b76e96658f5e5a5130bc96d63f016073579b72d@rpc-1.defund.nodes.guru:45656"
peers="f902d7562b7687000334369c491654e176afd26d@170.187.157.19:26656,f8093378e2e5e8fc313f9285e96e70a11e4b58d5@rpc-2.defund.nodes.guru:45656,878c7b70a38f041d49928dc02418619f85eecbf6@rpc-3.defund.nodes.guru:45656"
sed -i.default "s/^seeds *=.*/seeds = \"$seeds\"/;" $HOME/.defund/config/config.toml
sed -i "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/;" $HOME/.defund/config/config.toml
sed -i.back "s/pruning *=.*/pruning = \"custom\"/g" $HOME/.defund/config/app.toml
sed -i "s/pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/g" $HOME/.defund/config/app.toml
sed -i "s/pruning-keep-every *=.*/pruning-keep-every = \"0\"/g" $HOME/.defund/config/app.toml
sed -i "s/pruning-interval *=.*/pruning-interval = \"10\"/g" $HOME/.defund/config/app.toml
defundd tendermint unsafe-reset-all

echo "[Unit]
Description=Defund
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=/usr/local/bin/defundd start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target" > $HOME/defund.service
sudo mv $HOME/defund.service /etc/systemd/system
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF

sudo systemctl restart systemd-journald
sudo systemctl daemon-reload
sudo systemctl enable defund 

sudo systemctl restart defund
```
