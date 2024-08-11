# Upgrade



```
cd $HOME
wget https://github.com/MANTRA-Finance/public/releases/download/v2.0.0/mantrachaind-2.0.0-linux-amd64.tar.gz
tar -xvf mantrachaind-2.0.0-linux-amd64.tar.gz
rm $HOME/mantrachaind-2.0.0-linux-amd64.tar.gz
chmod +x mantrachaind
sudo mv $HOME/mantrachaind $(which mantrachaind)
sudo systemctl restart mantrachaind && sudo journalctl -u mantrachaind -f
```
