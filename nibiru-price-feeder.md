---
hidden: true
---

# Nibiru price feeder

```
# Go to your binary directory
cd /usr/local/bin

# Remove the old binary if it exists
sudo rm -f pricefeeder

# Download the new tar.gz archive
sudo wget https://github.com/NibiruChain/pricefeeder/releases/download/v1.1.1/pricefeeder_1.1.1_linux_amd64.tar.gz

# Extract it
sudo tar -xvzf pricefeeder_1.1.1_linux_amd64.tar.gz

# Move the executable to /usr/local/bin and rename it
sudo mv pricefeeder /usr/local/bin/pricefeeder

# Make it executable
sudo chmod +x /usr/local/bin/pricefeeder

# Clean up
sudo rm -f pricefeeder_1.1.1_linux_amd64.tar.gz


sudo systemctl daemon-reload
sudo systemctl restart pricefeeder
sudo systemctl status pricefeeder

```
