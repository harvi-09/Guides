# pivx-clone
utxo(pivx) chain clone 
# TO CLONE COIN (PIVX)

## Build depend
[guide](https://bitcointalk.org/index.php?topic=3345808.msg35016844#msg35016844)
### Changing parameters : 
- from source code change params accordingly
- remove vseed given that we don't have servers
- remove checkpoints
- change rpc port and p2p port
- change timestamp 
- change reward table 
- change consensus  vUpdrade:
```javascript
POS = last pow block + 1
POS_V2 = 1
BIP65 = last pow block + 200
V3_4 = last pow block + 1
v4_0 = last pow block + 50
V5_0 = last pow block + 300
V5_2 = last pow block + 300
```
- update spork keys :
	- install node modules
	- server.js --> privatekey prefix = secret key prefix in source code 
	- node server.js --> spork key
	- set spork key parameter
	- set epoch timestamp

## Remove Zerocoin

- Run autogen :
```javascript
chmod +x autogen.sh share/genbuild.sh
./autogen.sh
```

- Run configure(for your system) : 
```javascript
sudo ./configure --prefix=/home/rain/depends/PIVX_5.6.1/PIVX-5.6.1_win64/depends/x86_64-w64-mingw32 --enable-tests=no --disable-online-rust
sudo ./configure --prefix=/home/rain/depends/PIVX_5.6.1/PIVX-5.6.1_ubuntu/depends/x86_64-linux-gnu --enable-tests=no --disable-online-rust
sudo ./configure --prefix=/home/rain/depends/PIVX_5.6.1/PIVX-5.6.1_macos/depends/x86_64-apple-darwin16 --enable-tests=no --disable-online-rust
```

- Run below command to build daemon and QT
```javascript
sudo make
```

### Genesis
- comment genesis code
- replace create code with bleow :-
```javascript
uint32_t nNonce = 0;
while (genesis.GetHash() > consensus.powLimit) {
	nNonce++;
	genesis = CreateGenesisBlock(1716459105(past time), nNonce, 0x1e0ffff0, 1, 0);
}
// (uint32_t nTimeTx, uint32_t nTimeBlock, uint32_t nNonce, uint32_t nBits, int32_t nVersion, const CAmount& genesisReward)
genesis = CreateGenesisBlock(1716459105, nNonce, 0x1e0ffff0, 1, 0);
consensus.hashGenesisBlock = genesis.GetHash();
```
- genesis hash and merkle hash = `0x`
- In validation --> loadgenesis --> log 
	- `LogPrintf(">>>>>>>>>>>>%s", block.ToString().c_str());`
- replace the parameters you get from above at commented geneseis code.
- `addnode=ip` of peer in `.mycoin/mycoin.conf` (for pow & pos code)
- pos --> daemon --> getaddress 5
- pow --> qt --> check connection --> `setgenerate true 1` (pow phase)
- make tx to 5 address from blockreward as block mines upto 260 block (in this case : 200 pow --> pos)
- change your ui of qt accordingly and make again

### Setting up masternode
- take null wallet addrres and deposite MN collateral in it
- After that tx gets 1 confirmation  
- in qt console fire => getmasternodeoutput
                     => createmasternodekey
- set masternode.conf file in data dir as :
```javascript

Format: alias IP:port masternodeprivkey collateral_output_txid collateral_output_index

alias = MN_Name
IP:Port = SERVER ip and coin P2P Port
masternodeprivkey = output of crearemasternodekey
collateral_output_txid = output of getmasternodeoutputs
collateral_output_index = index of above output

example:
MN1 192.168.0.168:16050 8918UVrzyPQ5giVe5oZnqMTLZxxSfD5zjDCR9SWoKH3z4ERXVvd 2bcd3c84c84f87eaa86e4e56834c92927a07f9e18718810b92e0d0324456a67c 0
```


- set mycoin.conf in data dir as :

```javascript
masternode=1
masternodeprivkey=output of crearemasternodekey
externalip=SERVER ip and coin P2P Port
```
- start daemon and wait till synced
- wait for 15 confirmations for collateral tx
- Fire in qt console => startmasternode alias false MN_Name

### Setting up explorer
()[https://github.com/bulwark-crypto/bulwark-explorer/blob/master/README.md]
()[https://github.com/iquidus/explorer/blob/master/README.md]
- iquidus and bulwark
- create mongodb user and password
- edit config.json(iquidus) / setting.json(bulwark)
	- rpc port, username, password
	- db user, password, database
	- api host to our local ip
- set rpc user and password in mycoin.conf (data dir)
