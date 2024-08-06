# Upgrade

```
cd $HOME

wget https://github.com/SigmaGmbH/swisstronik-chain/releases/download/testnet-v1.0.4/swisstronikd.zip

unzip swisstronikd.zip

sudo cp $HOME/bin/libsgx_wrapper_v1.0.4.x86_64.so /usr/lib

cp bin/v1.0.4_enclave.signed.so $HOME/.swisstronik-enclave

sudo mv ~/bin/swisstronikd $(which swisstronikd)

sudo systemctl restart swisstronikd && sudo journalctl -u swisstronikd -f
```
