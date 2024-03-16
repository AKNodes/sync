# Upgrade

```
cd $HOME
rm -rf $HOME/terp-core
git clone https://github.com/terpnetwork/terp-core.git
cd $HOME/terp-core
git checkout v4.1.1
make build
sudo mv $HOME/terp-core/build/terpd $(which terpd)
sudo systemctl restart terpd && sudo journalctl -u terpd -f
```
