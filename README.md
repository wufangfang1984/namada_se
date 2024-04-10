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


## <center> Node Setup
<p style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: center;
  border-radius: 12px;
  padding: 8px;
">
<b>install essentials</b>
sudo apt update; sudo apt upgrade -y<br />
sudo apt install make clang pkg-config libssl-dev build-essential git jq llvm libudev-dev -y<br />
</p>

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
