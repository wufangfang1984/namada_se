# CREW S CLASS ASTEROIDS

**Category:**  
Operating IBC/ Interoperability infrastructure - Operate a Shielded Expedition-compatible Osmosis testnet relayer

**Description:**  
We deploy a fullnode for namada SE (194.163.132.38).  
We also deploy another fullnode for osmo-test-5, namada CLI and Hermes. All operations below are based on this server.   

Below is the IBC relayer channel we created for namada and osmosis.  
| |Namada|Osmosis|
|-|-|-|
|Chain-id|shielded-expedition.88f17d1d14|osmo-test-5|
|Channel-id|channel-509|channel-5956|
|Client-id|07-tendermint-1801|07-tendermint-2435|

# Install Namada, Osmosisd and Hermes
```
export HERMES_CONFIG=$HOME/.hermes/config.toml  
export BASE_DIR=$HOME/.local/share/namada  
mkdir $HOME/.hermes
mkdir -p $BASE_DIR
```
Install Namada v0.31.6
```
Download namada and copy to /usr/local/bin/  
https://github.com/anoma/namada/releases/download/v0.31.6/namada-v0.31.6-Linux-x86_64.tar.gz
namadac utils join-network --chain-id shielded-expedition.88f17d1d14
```
Deploy osmo-test-5 fullnode  
```
https://github.com/osmosis-labs/testnets/blob/main/testnets/osmo-test-5/README.md  
```
Install hermes
```
Download hermes-v1.7.4 and copy to /usr/local/bin/

https://github.com/heliaxdev/hermes/releases/download/v1.7.4-namada-beta7/hermes-v1.7.4-namada-beta7-x86_64-unknown-linux-gnu.tar.gz
```
Check release version
```
namada --version    
Namada v0.31.6

osmosisd version  
23.0.0-rc1

hermes --version  
hermes 1.7.4+38f41c6  
```
Generate relayer account for osmosis
```
osmosisd keys add wuff_relayer_osmo_5
Enter keyring passphrase (attempt 1/3):

- address: osmo1tykueau3pd5q56f89528ls3szzq6pkgqeusq5n
  name: wuff_relayer_osmo_5
  pubkey: '{"@type":"/cosmos.crypto.secp256k1.PubKey","key":"Asw8hAZkP5AgCamffZiytfBuHfpoqy+9n2OI3jI7qht+"}'
  type: local


**Important** write this mnemonic phrase in a safe place.
It is the only way to recover your account if you ever forget your password.

snap mixture suit secret welcome wild trick cream blush slide range judge eight truly account melt ensure hungry forward manage sport fever magnet dignity
```
Derive Namada account for relayer
```
namadaw derive --alias wuff_relayer_namada_se  
namadaw find --alias wuff_relayer_namada_se  
Found transparent keys:
  Alias "wuff_relayer_namada_se" (encrypted):
    Public key hash: 111A07AFA2C052312A5F99DE0996E7B769BB3475
    Public key: tpknam1qzndq3sytzpv6z0t6ufug47g7kc0x7xpk3d228swfy3rz2zlp7v8cmd2xt8
Found transparent address:
  "wuff_relayer_namada_se": Implicit: tnam1qqg35pa05tq9yvf2t7vauzvku7mknwe5w5jzs8ew
```

Fund some faucets and check balances
```
osmosisd query bank balances osmo1tykueau3pd5q56f89528ls3szzq6pkgqeusq5n
balances:
- amount: "100000000"
  denom: uosmo

namadac balance --owner wuff_relayer_namada_se --node http://194.163.132.38:26657
naan: 242.772897
```
# Configure Hermes
vi $HERMES_CONFIG
```
...
[[chains]]
id = 'shielded-expedition.88f17d1d14' 
type = 'Namada'
rpc_addr = 'http://194.163.132.38:26657'  
grpc_addr = 'http://194.163.132.38:9090' 
event_source = { mode = 'push', url = 'ws://194.163.132.38:26657/websocket', batch_delay = '500ms' } 
account_prefix = ''
key_name = 'wuff_relayer_namada_se' 
store_prefix = 'ibc'
gas_price = { price = 0.0001, denom = 'tnam1qxvg64psvhwumv3mwrrjfcz0h3t3274hwggyzcee' } 
rpc_timeout = '20s'

[[chains]]
id = 'osmo-test-5'
type = 'CosmosSdk'
rpc_addr = 'http://127.0.0.1:26657' 
grpc_addr = 'http://127.0.0.1:9090'
event_source = { mode = 'push', url = 'ws://127.0.0.1:26657/websocket', batch_delay = '500ms' } 
account_prefix = 'osmo'
key_name = 'wuff_relayer_osmo_5'
address_type = { derivation = 'cosmos' }
store_prefix = 'ibc'
default_gas = 400000
max_gas = 120000000
gas_price = { price = 0.0025, denom = 'uosmo' }
gas_multiplier = 1.2
max_msg_num = 30
max_tx_size = 1800000
clock_drift = '15s'
max_block_time = '30s'
trusting_period = '4days'
trust_threshold = { numerator = '1', denominator = '3' }
rpc_timeout = '20s'
```
# Add keys to Hermes
```
echo "snap mixture suit secret welcome wild trick cream blush slide range judge eight truly account melt ensure hungry forward manage sport fever magnet dignity" > ./mnemonic

hermes --config $HERMES_CONFIG keys add --chain osmo-test-5 --mnemonic-file ./mnemonic
SUCCESS Restored key 'wuff_relayer_osmo_5' (osmo1tykueau3pd5q56f89528ls3szzq6pkgqeusq5n) on chain osmo-test-5

hermes --config $HERMES_CONFIG keys add --chain shielded-expedition.88f17d1d14 --key-file ~/.local/share/namada/shielded-expedition.88f17d1d14/wallet.toml  
UCCESS Added key 'wuff_relayer_namada_se' (tnam1qqg35pa05tq9yvf2t7vauzvku7mknwe5w5jzs8ew) on chain shielded-expedition.88f17d1d14
```
# Start Hermes
hermes --config $HOME/.hermes/config.toml start

# Create Channel
```
hermes --config $HERMES_CONFIG \
  create channel \
  --a-chain shielded-expedition.88f17d1d14 \
  --b-chain osmo-test-5 \
  --a-port transfer \
  --b-port transfer \
  --new-client-connection --yes
```
```
SUCCESS Channel {
    ordering: Unordered,
    a_side: ChannelSide {
        chain: BaseChainHandle {
            chain_id: ChainId {
                id: "shielded-expedition.88f17d1d14",
                version: 0,
            },
            runtime_sender: Sender { .. },
        },
        client_id: ClientId(
            "07-tendermint-1801",
        ),
        connection_id: ConnectionId(
            "connection-773",
        ),
        port_id: PortId(
            "transfer",
        ),
        channel_id: Some(
            ChannelId(
                "channel-509",
            ),
        ),
        version: None,
    },
    b_side: ChannelSide {
        chain: BaseChainHandle {
            chain_id: ChainId {
                id: "osmo-test-5",
                version: 5,
            },
            runtime_sender: Sender { .. },
        },
        client_id: ClientId(
            "07-tendermint-2435",
        ),
        connection_id: ConnectionId(
            "connection-2266",
        ),
        port_id: PortId(
            "transfer",
        ),
        channel_id: Some(
            ChannelId(
                "channel-5956",
            ),
        ),
        version: None,
    },
    connection_delay: 0ns,
}
```

# Send naan from Namada to Osmosis
```
namadac --base-dir $BASE_DIR \
    ibc-transfer \
    --amount 1 \
    --source wuff_relayer_namada_se \
    --receiver osmo1tykueau3pd5q56f89528ls3szzq6pkgqeusq5n \
    --token naan \
    --channel-id channel-509 \
    --node http://194.163.132.38:26657 \
    --memo tpknam1qzndq3sytzpv6z0t6ufug47g7kc0x7xpk3d228swfy3rz2zlp7v8cmd2xt8

Enter your decryption password: 
Transaction added to mempool.
Wrapper transaction hash: 8CF4CD8FC5C14F04DFBE1867775F9B80B7D6AB699A1CC7BDBD1EDF3B13F8FB23
Inner transaction hash: 368CF4FB6990D92099CD6E996F9E8F820D9210C24BA0D3021EE3DF42EAF468E5
Wrapper transaction accepted at height 77920. Used 26 gas.
Waiting for inner transaction result...
Transaction was successfully applied at height 77921. Used 6193 gas.
```

# Send uosmo from Osmosis to Namada
```
osmosisd tx ibc-transfer transfer \
  transfer \
  channel-5956 \
  tnam1qqg35pa05tq9yvf2t7vauzvku7mknwe5w5jzs8ew \
  1000000uosmo \
  --from wuff_relayer_osmo_5 \
  --gas auto \
  --gas-prices 0.04uosmo \
  --gas-adjustment 1.2 \
  --node "http://127.0.0.1:26657" \
  --home "$HOME/.osmosisd" \
  --chain-id osmo-test-5 \
  --yes

Enter keyring passphrase (attempt 1/3):
gas estimate: 130992
code: 0
codespace: ""
data: ""
events: []
gas_used: "0"
gas_wanted: "0"
height: "0"
info: ""
logs: []
raw_log: '[]'
timestamp: ""
tx: null
txhash: D8ECE091E8E9FDE32C3D2F9E68A97B64B951192EE6A88C46559F42C9238E6E40
```
We can see wuff_relayer_osmo_5 received 1 naan token and wuff_relayer_namada_se received 1 uosmo token.
```
osmosisd query bank balances osmo1tykueau3pd5q56f89528ls3szzq6pkgqeusq5n
balances:
- amount: "1"
  denom: ibc/58E815D9F31B27E2E750E792264415FBE039083703EAB3C10C249D3EC457A369
- amount: "98983196"
  denom: uosmo
pagination:
  next_key: null
  total: "0"

namadac balance --owner wuff_relayer_namada_se --node http://194.163.132.38:26657
naan: 218.272897
transfer/channel-509/uosmo: 1000000
```
