# Upgrade



```
cd $HOME
rm -rf wardenprotocol
git clone https://github.com/warden-protocol/wardenprotocol.git
cd wardenprotocol
git checkout v0.4.1
make install-wardend
sudo mv wardend $HOME/go/bin/
```
