# Upgrade



```
cd $HOME
rm -rf wardenprotocol
git clone https://github.com/warden-protocol/wardenprotocol.git
cd wardenprotocol
git checkout v0.7.2
make install-wardend
sudo mv wardend $HOME/go/bin/
sudo systemctl restart wardend && sudo journalctl -u wardend -fo cat
```
