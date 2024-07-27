

Güncelleme

### 🚧Gerekliliklerle dosyaları çekelim

```
sudo apt install rsync
sh -c "$(curl -fsSL https://raw.githubusercontent.com/MictoNode/titan-cosmos/main/update.sh)"
git clone https://github.com/Titannet-dao/titan-chain.git
cd titan-chain
go build ./cmd/titand
cd
```

### 🚧Bi de binary'i taşıyalım
```
mkdir -p ~/.titan/cosmovisor/upgrades/2.0.0/bin
mv $HOME/titan-chain/titand ~/.titan/cosmovisor/upgrades/2.0.0/bin/
```
```
sudo ln -sfn ~/.titan/cosmovisor/upgrades/2.0.0 ~/.titan/cosmovisor/current
sudo ln -sfn ~/.titan/cosmovisor/current/bin/titand /usr/local/bin/titand
```

### 🚧Tutun kolumdan kaldırın beni
```
sudo systemctl daemon-reload
sudo systemctl restart titand
```
```
sudo journalctl -u titand -f -o cat
```
### 🚧Teşekkürler baybay