# Upgrade

```
cd $HOcd $HOME
wget https://github.com/SigmaGmbH/swisstronik-chain/releases/download/testnet-v1.0.5/swisstronikd.zip
unzip swisstronikd.zip
sudo cp $HOME/bin/libsgx_wrapper_v1.0.5.x86_64.so /usr/lib
cp $HOME/bin/v1.0.5_enclave.signed.so $HOME/.swisstronik-enclave
chmod +x $HOME/bin/swisstronikd
sudo mv $HOME/bin/swisstronikd $(which swisstronikd)
sudo systemctl restart swisstronikd && sudo journalctl -u swisstronikd -f

```
