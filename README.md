# <center> Namada Basic Services

## <center> Basic Services(RPC, Indexer, Seed, Peer, etc)

#### <center> RPC Endpoints:
<p style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: center;
  border-radius: 12px;
  padding: 12px;
">
<a href="https://rpc.shoeiico-se-namada.asia/" title="RPC endpoint">https://rpc.shoeiico-se-namada.asia/</a>
</p>

#### <center> Indexer Endpoints: 
<p style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: center;
  border-radius: 12px;
  padding: 12px;
">
<a href="https://se.shoeiico-indexer-namada.online/block/last" title="Indexer endpoint">https://se.shoeiico-indexer-namada.online/block/last</a>
</p>

#### <center> Seed:
<p style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: center;
  border-radius: 12px;
  padding: 12px;
">
tcp://c903e6596d8cada85426d81cbfe08387993413cb@213.136.71.166:26656
</p>

#### <center> Peer:
<p style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: center;
  border-radius: 12px;
  padding: 12px;
">
tcp://c903e6596d8cada85426d81cbfe08387993413cb@213.136.71.166:26656
</p>

#### <center> Address book:
<p style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: center;
  border-radius: 12px;
  padding: 12px;
">
http://rpc.shieldedexpedition-mohismstake.site:12345/addrbook.json
</p>

#### <center> Snapshot:
<div style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: center;
  border-radius: 12px;
  padding: 12px;
">
http://rpc.shoeiico-se-namada.asia:61000/snapshot_202404.tar.gz
</div>

<div style="height:22px"></div>

## <center> Node Setup(Compile from source)
<div style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: center;
  border-radius: 12px;
  padding: 8px;
">
<b><strong>Install Essentials</strong></b><br />
<br />
sudo apt update<br />
sudo apt install make clang pkg-config libssl-dev build-essential git jq llvm libudev-dev -y<br />
<br />
<b><strong>Install Rust</strong></b><br />
<br />
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y <br />
source $HOME/.cargo/env<br />
<br />
<b><strong>Install Protocol Buffers</strong></b><br />
<br />
sudo apt install protobuf-compiler<br />
<br />
<b><strong>Install Cometbft</strong></b><br />
<br />
mkdir cometbft<br />
wget https://github.com/cometbft/cometbft/releases/download/v0.37.2/cometbft_0.37.2_linux_amd64.tar.gz<br />
tar xvf cometbft_0.37.2_linux_amd64.tar.gz -C ./cometbft<br />
chmod +x cometbft/cometbft<br />
sudo mv cometbft/cometbft /usr/local/bin/<br />
<br />
<b><strong>Compile From Repo</strong></b><br />
<br />
git clone https://github.com/anoma/namada.git<br />
cd namada<br />
git checkout v0.31.6<br />
make install<br />
sudo chmod +x ~/.cargo/bin/namada*<br />
sudo mv ~/.cargo/bin/namada* /usr/local/bin<br />
<br />
</div>

## <center> Node Setup(Install Official Binaries)
<div style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: center;
  border-radius: 12px;
  padding: 8px;
">
<b><strong>Install Official Namada Binaries</strong></b><br />
<br />
wget https://github.com/anoma/namada/releases/download/v0.31.6/namada-v0.31.6-Linux-x86_64.tar.gz<br />
tar -zxvf namada-v0.31.6-Linux-x86_64.tar.gz<br />
sudo mv namada-v0.31.6-Linux-x86_64/namada* /usr/local/bin/<br />
</div>

## <center> Start Node
<div style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: center;
  border-radius: 12px;
  padding: 8px;
">
export CHAIN_ID=shielded-expedition.88f17d1d14<br />
namadac utils join-network --chain-id $CHAIN_ID<br />
namadan ledger run
</div>

## <center> Run node as Service
```
sudo tee /etc/systemd/system/namadad.service << EOF
[Unit]
Description=Namada Node
After=network.target
[Service]
User=$USER
WorkingDirectory=$HOME/.local/share/namada
Type=simple
ExecStart=/usr/local/bin/namada --base-dir=$HOME/.local/share/namada node ledger run
Environment=NAMADA_CMT_STDOUT=true
Environment=TM_LOG_LEVEL=p2p:none,pex:error
RemainAfterExit=no
Restart=on-failure
RestartSec=10s
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

#### <center> Enable Service
<div style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: center;
  border-radius: 12px;
  padding: 8px;
">
sudo chmod 755 /etc/systemd/system/namadad.service<br />
sudo systemctl daemon-reload<br />
sudo systemctl enable namadad<br />
sudo systemctl start namadad && sudo journalctl -u namadad -n 1000 -f
</div>

#### <center> Service Operations
<div style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: center;
  border-radius: 12px;
  padding: 8px;
">
sudo service namadad start<br />
sudo service namadad status<br />
sudo service namadad stop<br />
sudo service namadad restart<br />
</div>

## <center> Crew Member Commands

#### <center> Import Registered Account
```
namadac validator-state --validator "<Validator address>"
```

#### <center> Check Balance
```
namadac unjail-validator --validator  "<Validator address>"
```


