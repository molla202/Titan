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
git clone https://github.com/Titannet-dao/titan-chain.git
cd titan-chain
go build ./cmd/titand
cd
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
titand init node-adi-yaz --chain-id titan-test-2
```
```
titand config keyring-backend os
titand config node tcp://localhost:29657
```
### 🚧Genesis ve addrbook
```
curl -L https://raw.githubusercontent.com/molla202/Titan/main/addrbook.json > $HOME/.titan/config/addrbook.json
curl -L https://raw.githubusercontent.com/Titannet-dao/titan-chain/main/genesis/genesis.json > $HOME/.titan/config/genesis.json
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
peers="46d55979a4d71b19681e9aa51f34dc252428fd21@193.34.212.80:29656,900a710a3d653fff6e33584be887b38ceb74b90a@65.108.43.115:29656,a76fb0dabf75c946d475880ba0ce7aec8cbea541@89.32.121.114:26656,1b22c0f6b0d6c3553975cc94d5d260d42d7aa381@78.129.218.44:26656,532643788884b947beb9f2a091daec8a83302baa@65.109.115.15:29656,b656a30fd7585c68c72167805784bcd3bed2d67c@8.217.10.76:26656,78d00191a0306538e4907ff536ec71a592dc593f@37.60.235.21:35656,8e06042743e33439e09c7443197c9af7d093e257@38.242.145.244:35656,74a50c0667b79397853b28ed8576f23a1ab7af5a@217.76.49.138:29656,f75b075a5c7d2b30ad8ae1e9bf9068a1836204d5@45.10.160.26:35656,1a216aa8a63471242103ebf8a44de2ad7329fdd0@198.7.119.11:26656,a654f14a4c20e0b9d1a4c3502efcfcf4059b7251@198.7.118.87:26656,d7e49b0256a6c17b542cd60479887600249992d4@148.251.82.6:53456,cb1840de1de0403a3f46806ae1e13199cb7679dc@80.190.85.195:53456,9f6a923f9bc5bdb4f737467192db20d260433635@78.46.19.116:28956,f280eab4d6ca4047aeaca17811364865b75995de@65.109.39.80:29656,63a131a3522e85da5e0fef5764e88e185433eff3@8.219.48.39:26656,2e884329b172df683fefa6033dd0c5641372a6a0@8.222.141.140:26656,523fc6c471e910a4e01f166bd4fe12a46677f185@47.251.77.144:26656,4b8d1c6b9347ce25be13b4a3bf598f7122a2e9fe@47.89.251.251:26656,2ef5e70ed3929d52098f27518296a0ee93c0fabb@47.243.127.179:26656,bb075c8cc4b7032d506008b68d4192298a09aeea@47.76.107.159:26656,ab6e01e28245cda5dfb785be5a338d8c8064d39b@34.162.191.240:26656,0760c9219355406c7adf6c208f7a2671ab329744@8.211.46.9:26656,e445dbbeeea3ee31688cbe9294b54d09ec59e2a4@135.181.130.103:35656,419f536ec9bd0f593e89d27c9bd83457fb26e04e@94.72.111.67:35656,771f44164c300217a711b6c52ec6fb779f745c86@8.209.96.22:26656,20caba6a5113a57ba3bbef129505714c214802b5@34.48.103.157:26656,a2553f09159f90651b465e330e58a7db5ec7f356@47.251.91.138:26656,173b878254399c0e241cee7c1d0cde840e9658a3@8.218.233.176:26656,913144aa667e2d4cabeb11d20d864d027e940d4c@8.211.41.135:26656,87cf9989488b7912d45a2e0e4b48f4e3c4aa3a7b@47.237.75.218:26656,611ceae5d790a4146a35383fb795d0589c9bc8db@59.127.225.115:26656,40d7de8cd428a84169b2420b22e03f7f8c98b2ef@222.211.110.195:26656,412cdbe51c0ee7bcaaa7e8b7eebdf17f6ca98348@65.21.141.250:29656,f7260b32b5d4427ab1edec66b05b95180efd0bf8@65.108.129.49:35656,53c177bcf977b07382158174a203fac704ce126d@84.46.247.58:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.titan/config/config.toml
seeds="bb075c8cc4b7032d506008b68d4192298a09aeea@47.76.107.159:26656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.titan/config/config.toml

```

### 🚧Pruning
```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.titan/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.titan/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"10\"/" $HOME/.titan/config/app.toml
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
  --chain-id=titan-test-2 \
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
titand tx staking delegate $(titand keys show cüzdan-adi-yaz --bech val -a) 1000000uttnt --from cüzdan-adi-yaz --chain-id titan-test-2 --fees 500uttnt --node=http://localhost:29657 -y
```
### Jailden çıkma
```
titand tx slashing unjail --from cüzdan-adi-yaz --chain-id titan-test-2 --fees 500uttnt --node=http://localhost:29657 -y
```





