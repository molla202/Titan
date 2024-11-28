
```
cd $HOME
snap install lz4
sudo systemctl stop titan
cp $HOME/.titan/data/priv_validator_state.json $HOME/.titan/priv_validator_state.json.backup
rm -rf $HOME/.titan/data
curl -o - -L http://37.120.189.81/titan_testnet/titan_snap.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.titan --strip-components 2
mv $HOME/.titan/priv_validator_state.json.backup $HOME/.titan/data/priv_validator_state.json
sudo systemctl restart titan && journalctl -fu titan -o cat
```
