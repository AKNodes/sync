---
description: 'Upgrade height: 907696 to version 1.2.1'
---

# Upgrade



<pre><code><strong>cd $HOME
</strong><strong>wget -O pellcored https://github.com/0xPellNetwork/network-config/releases/download/v1.2.1/pellcored-v1.2.1-linux-amd64
</strong>chmod +x pellcored
sudo mv $HOME/pellcored $(which pellcored)
sudo systemctl restart pellcored &#x26;&#x26; sudo journalctl -u pellcored -f
</code></pre>
