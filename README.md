# <center> Namada Shielded Expedition Services

## <center> Infrastructure Services

#### <center> RPC Service:
<p style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: left;
  border-radius: 12px;
  padding: 14px;
">
https://rpc.wufangfang-namada.online/
</p>

#### <center> Indexer Service: 
<p style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: left;
  border-radius: 12px;
  padding: 14px;
">
https://se.indexer-wufangfang.online/block/last
</p>

#### <center> Seed:
<p style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: left;
  border-radius: 12px;
  padding: 14px;
">
tcp://d8025e5dea101bc573d31e53cf3f479c80d5d859@194.163.132.38:26656
</p>

#### <center> Peer:
<p style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: left;
  border-radius: 12px;
  padding: 14px;
">
tcp://d8025e5dea101bc573d31e53cf3f479c80d5d859@194.163.132.38:26656
</p>

#### <center> Address book:
<p style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: left;
  border-radius: 12px;
  padding: 14px;
">
http://rpc.wufangfang-namada.online:7060/addrbook.json
</p>

#### <center> Snapshot:
<div style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: left;
  border-radius: 12px;
  padding: 14px;
">
http://rpc.wufangfang-namada.online:7060/namada_testnet_snapshot.tar.gz
</div>

<div style="height:22px"></div>

## <center> Node Setup(Compile from source)
<div style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: left;
  border-radius: 12px;
  padding: 14px;
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
git checkout v0.32.1<br />
make install<br />
sudo chmod +x ~/.cargo/bin/namada*<br />
sudo mv ~/.cargo/bin/namada* /usr/local/bin<br />
<br />
</div>

## <center> Node Setup(Install Compiled Binaries)
<div style="
  box-shadow: 0 4px 8px 0 rgba(0,0,0,0.3);
  width: 100%;
  text-align: left;
  border-radius: 12px;
  padding: 14px;
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
  text-align: left;
  border-radius: 12px;
  padding: 14px;
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
  text-align: left;
  border-radius: 12px;
  padding: 14px;
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
  text-align: left;
  border-radius: 12px;
  padding: 14px;
">
sudo service namadad start<br />
sudo service namadad status<br />
sudo service namadad stop<br />
sudo service namadad restart<br />
</div>

## <center> Crew Member Operations

#### <center> Recover Account
```
namadaw derive --alias "<Wallet address>"
```

#### <center> Check Balance
```
namadac balance --owner "Wallet address"
```

#### <center> Stake
```
namadac bond \
  --validator "Validator address" \
  --source "Wallet address" \
  --signing-keys "Wallet Public key" \
  --amount "Amount"
```

#### <center> Unstake
```
namadac unbond --source "Wallet address" --validator "Validator address" --amount "Amount"
```

#### <center> Withdraw
```
namadac withdraw --source "Wallet address" --validator "Validator address" 
```

#### <center> Claim
```
namadac claim-rewards --validator "Validator address" --source "Wallet address"
```

#### <center> Query Delegations
```
namadac delegations --owner "Wallet address"
```

#### <center> Redelegate
```
namadac redelegate \
 --source-validator "<Source Validator address>" \
 --destination-validator "<Destination Validator address>" \
 --owner "<Wallet address>" \
 --amount "<Amount>"
```

#### <center> Transfer Token
```
namadac transfer \
  --token naan \
  --amount "Amount" \
  --target "Destination Wallet Alias/address" \
  --source "Source Wallet Alias/address" \
  --signing-keys "Wallet Public key"
```

#### <center> Generate Shielded Account 
```
namadaw gen --shielded --alias "Spending Key Alias"
namadaw gen-payment-addr --key "Spending Key Alias" --alias "Payment Key Alias"
```

#### <center> Transparent To Shielded Transfer
```
namadac transfer \
  --token naan \
  --amount "Amount" \
  --target "Payment Key Alias" \
  --source "Wallet address" \
  --signing-keys "Wallet Public key"
```

#### <center> Shielded To Shielded Transfer
```
namadac transfer \
  --token naan \
  --amount "Amount" \
  --target "Destination Payment Key Alias>" \
  --source "Source Spending Key Alias" \
  --signing-keys "Wallet Public key"
```

#### <center> Unshielded Transfer
```
namadac transfer \
  --token naan \
  --amount "Amount" \
  --target "Wallet address" \
  --source "Spending Key Alias" \
  --signing-keys "Wallet Public key"
```

#### <center> Shielded Sync
```
namadac shielded-sync
```

