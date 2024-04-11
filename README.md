# <center> Namada infrastructure

## <center> Infrastructures

#### <center> Public RPC:
<p style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: center;
  border-radius: 12px;
  padding: 12px;
">
<a href="https://rpc.shieldedexpedition-mohismstake.site/" title="RPC endpoint">https://rpc.shieldedexpedition-mohismstake.site/</a>
</p>

#### <center> Indexer Service: 
<p style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: center;
  border-radius: 12px;
  padding: 12px;
">
<a href="https://rpc.shieldedexpedition-mohismstake.site/" title="RPC endpoint">https://rpc.shieldedexpedition-mohismstake.site/</a>
</p>

#### <center> Seed:
<p style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: center;
  border-radius: 12px;
  padding: 12px;
">
tcp://e1e4f80f1a4738f3a3983369c35e3d3fe3369b3f@154.53.45.59:26656
</p>

#### <center> Peer:
<p style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: center;
  border-radius: 12px;
  padding: 12px;
">
tcp://e1e4f80f1a4738f3a3983369c35e3d3fe3369b3f@154.53.45.59:26656
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
http://rpc.shieldedexpedition-mohismstake.site:12345/snapshot_testnetse.tar.gz
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
<b><strong>install essentials</strong></b><br />
<br />
sudo apt update<br />
sudo apt install make clang pkg-config libssl-dev build-essential git jq llvm libudev-dev -y<br />
<br />
<b><strong>install rust</strong></b><br />
<br />
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y <br />
source $HOME/.cargo/env<br />
<br />
<b><strong>install protocol buffers</strong></b><br />
<br />
sudo apt install protobuf-compiler<br />
<br />
<b><strong>install protocol buffers</strong></b><br />
<br />
sudo apt install protobuf-compiler<br />
<br />
<b><strong>install Cometbft</strong></b><br />
<br />
mkdir cometbft<br />
wget https://github.com/cometbft/cometbft/releases/download/v0.37.2/cometbft_0.37.2_linux_amd64.tar.gz<br />
tar xvf cometbft_0.37.2_linux_amd64.tar.gz -C ./cometbft<br />
chmod +x cometbft/cometbft<br />
sudo mv cometbft/cometbft /usr/local/bin/<br />
<br />
<b><strong>Compile from repo</strong></b><br />
<br />
git clone https://github.com/anoma/namada.git<br />
cd namada<br />
git checkout v0.31.6<br />
make install<br />
sudo chmod +x ~/.cargo/bin/namada*<br />
sudo mv ~/.cargo/bin/namada* /usr/local/bin<br />
<br />
</div>

## Validator management

#### Validator consensus state
```
namadac validator-state --validator "<Validator address>"
```

#### Unjail validator
```
namadac unjail-validator --validator  "<Validator address>"
```

#### Query a validator's bonded-stake
```
namadac bonded-stake
```

#### Deactivate validator
```
namadac deactivate-validator --validator "VALIDATOR"
```

#### Reactivate validator
```
namadac reactivate-validator --validator "VALIDATOR"
```

#### Query validator metadata
```
namadac validator-metadata --validator "VALIDATOR"
```

#### Change validator metadata
```
namadac change-metadata \
  --validator "VALIDATOR" \
  --website "WEBSITE_URL" \
  --description "DESCRIPTION" \
  --avatar "LINK_TO_AVATAR" \
  --discord-handle "DISCORD_HANDLE" \
  --email "EMAIL"
```

## Run node as Service
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

#### Active service
```
sudo chmod 755 /etc/systemd/system/namadad.service
sudo systemctl daemon-reload
sudo systemctl enable namadad
sudo systemctl start namadad && sudo journalctl -u namadad -n 1000 -f
```

#### Service commands
```
sudo service namadad start
sudo service namadad status
sudo service namadad stop
sudo service namadad restart
```
