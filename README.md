<h1 align="center"> Titan </h1>



![image](https://github.com/molla202/Titan/assets/91562185/381f7d35-6421-43b0-9fcd-3b67be169410)




 * [Topluluk kanalımız](https://t.me/corenodechat)<br>
 * [Topluluk Twitter](https://twitter.com/corenodeHQ)<br>
 * [Titan Website](https://titannet.io/)<br>
 * [Blockchain Explorer](https://explorers.titannet.io/en)<br>
 * [Discord](https://discord.com/invite/titannet)<br>
 * [Twitter](https://twitter.com/Titannet_dao)<br>
 * [Faucet](https://faucet.titannet.io/)<br>



## 💻 Sistem Gereksinimleri
| Bileşenler | Minimum Gereksinimler | 
| ------------ | ------------ |
| CPU |	4|
| RAM	| 8+ GB |
| Storage	| 200+ GB SSD |


### Daha önce katıldıysanız faucetten ödülleri talep ederek coin alabiliyoruz discorda daha açılmadı faucet ara ara kontrol edin discord.
### Proje HAZEN tarafındna bulunup repolaştırılmıştır. ben cosmovisorlüsünü yazdım sadece orjinal dokuman https://github.com/hazennetworksolutions/titannetwork/blob/main/consensus-validator-node-turkish.md
### 🚧Gerekli kurulumlar
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

### 🚧Go kurulumu
```
cd $HOME
VER="1.21.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```
### 🚧Dosyaları çekelim
```
git clone https://github.com/nezha90/titan.git
cd titan
go build ./cmd/titand
```
```
mkdir -p /root/.titan/cosmovisor/genesis/bin
cp -r /root/titan/titand /root/.titan/cosmovisor/genesis/bin/
```
```
sudo ln -s $HOME/.titan/cosmovisor/genesis $HOME/.titan/cosmovisor/current -f
sudo ln -s $HOME/.titan/cosmovisor/current/bin/titand /usr/local/bin/titand -f
```
### 🚧Cosmovisor kuralım
```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
```
### 🚧Servis oluşturalım
```
sudo tee /etc/systemd/system/titand.service > /dev/null << EOF
[Unit]
Description=titand node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.titan"
Environment="DAEMON_NAME=titand"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.titan/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
```
### 🚧Etkinleştirelim
```
sudo systemctl daemon-reload
sudo systemctl enable titand
```
### 🚧İnit
NOT: node adını yaz
```
titand init node-adi-yaz --chain-id titan-test-1
```
```
titand config keyring-backend os
titand config node tcp://localhost:29657
```
### 🚧Genesis ve addrbook
```
curl -L https://raw.githubusercontent.com/molla202/Titan/main/addrbook.json > $HOME/.titan/config/addrbook.json
curl -L https://raw.githubusercontent.com/nezha90/titan/main/genesis/genesis.json > $HOME/.titan/config/genesis.json
```
### 🚧Port
```
echo "export T_PORT="29"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
```
sed -i.bak -e "s%:1317%:${T_PORT}317%g;
s%:8080%:${T_PORT}080%g;
s%:9090%:${T_PORT}090%g;
s%:9091%:${T_PORT}091%g;
s%:8545%:${T_PORT}545%g;
s%:8546%:${T_PORT}546%g;
s%:6065%:${T_PORT}065%g" $HOME/.titan/config/app.toml
```
### Port
```
sed -i.bak -e "s%:26658%:${T_PORT}658%g;
s%:26657%:${T_PORT}657%g;
s%:6060%:${T_PORT}060%g;
s%:26656%:${T_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${T_PORT}656\"%;
s%:26660%:${T_PORT}660%g" $HOME/.titan/config/config.toml
```
### 🚧Seed ve Peer
```
peers="b656a30fd7585c68c72167805784bcd3bed2d67c@8.217.10.76:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.titan/config/config.toml
seeds="bb075c8cc4b7032d506008b68d4192298a09aeea@47.76.107.159:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.titan/config/config.toml

```

### 🚧Pruning
```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.titan/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.titan/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.titan/config/app.toml
```
### 🚧Gas ve index ayarı
```
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.0025uttnt"|g' $HOME/.titan/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.titan/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.titan/config/config.toml
```
### Snap 
```
soon
```
### 🚧Başlatalım
```
sudo systemctl daemon-reload && sudo systemctl restart titand && sudo journalctl -u titand -f --no-hostname -o cat
```
### Log
```
sudo journalctl -u titand -f --no-hostname -o cat
```
### Cüzdan olusturma
```
titand keys add cüzdan-adi-yaz
```
### Cüzdan import
```
titand keys add cüzdan-adi-yaz --recover
```
### Validator oluştur
NOT: moniker ve cüzdan adınızı girin...
```
titand tx staking create-validator \
  --amount=1000000uttnt \
  --pubkey=$(titand tendermint show-validator) \
  --chain-id=titan-test-1 \
  --from=cuzdanadiniz \
  --moniker "monikeradiniz" \
  --identity "opsiyonel" \
  --details "opsiyonel" \
  --website "opsiyonel" \
  --commission-max-change-rate=0.01 \
  --commission-max-rate=1.0 \
  --commission-rate=0.07 \
  --min-self-delegation=1 \
  --fees 500uttnt \
  --node=http://localhost:29657
  -y
```
### Kendinize stake
NOT: cüzdan adınızı yazın
```
titand tx staking delegate $(titand keys show cüzdan-adi-yaz --bech val -a) 1000000uttnt --from cüzdan-adi-yaz --chain-id titan-test-1 --fees 500uttnt --node=http://localhost:29657 -y
```
### Jailden çıkma
```
titand tx slashing unjail --from cüzdan-adi-yaz --chain-id titan-test-1 --fees 500uttnt --node=http://localhost:29657 -y
```





