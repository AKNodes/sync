# Installation

### Step 1 <a href="#f165" id="f165"></a>

**Download ready-made binaries from the official github** _(this link is relevant for MAIN.1.0, if you are reading this guide, but there is already a new version is available, try changing the number 1.0 in the link to the number of the current version)_:

<pre><code><strong>Linux:
</strong>
wget https://github.com/massalabs/massa/releases/download/MAIN.2.0/massa_MAIN.2.0_release_linux.tar.gz

<strong>Linux Arm64:
</strong>
wget https://github.com/massalabs/massa/releases/download/MAIN.2.0/massa_MAIN.2.0_release_linux_arm64.tar.gz
</code></pre>

Unpack the downloaded archive:

<pre><code><strong>Linux:
</strong>
tar zxvf massa_MAIN.2.0_release_linux.tar.gz

<strong>Linux Arm64:
</strong>
tar zxvf massa_MAIN.2.0_release_linux_arm64.tar.gz
</code></pre>

You won’t believe it, but that’s it! You have installed the node.

### Step 2 <a href="#id-26a7" id="id-26a7"></a>

Before starting the node, we will create a config file with the ip-address of your server into it — for the node to be routable (replace the **IP\_ADDRESS** with the IPv4/IPv6 address of your server), then copy and paste whole the block:

<pre><code>sudo tee &#x3C;&#x3C;EOF >/dev/null $HOME/massa/massa-node/config/config.toml 
[protocol] 
<strong>routable_ip = "IP_ADDRESS"
</strong>EOF
</code></pre>

### Step 3 <a href="#id-84dc" id="id-84dc"></a>

Create a **service file** for the node to run in the background and does not stop after closing the terminal.\
Please note — replace the word **YOUR\_PASSWORD** with your own password, the same password that you used when creating a wallet!

(Copy the block below in Notepad, replace the word YOUR\_PASSWORD with your password and paste it entirely on the command line, not line by line.)

<pre><code>printf "[Unit]
Description=Massa Node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/massa/massa-node
<strong>ExecStart=$HOME/massa/massa-node/massa-node -p YOUR_PASSWORD
</strong>Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target" > /etc/systemd/system/massad.service
</code></pre>

Next, run the node using the service file (3 commands):

```
sudo systemctl daemon-reload

sudo systemctl enable massad

sudo systemctl restart massad
```

Checking that the node is working properly:

```
sudo journalctl -f -n 100 -u massad
```

### Step 4 <a href="#id-04b5" id="id-04b5"></a>

Now let’s move on to setting up the client / wallet.\
Launching the client for the first time. It will require, like the node, to come up with a password (I advise you to create the same as on the node, although most likely this is not entirely correct).

```
cd $HOME/massa/massa-client/

./massa-client
```

Next we need to restore the wallet (Your wallet secret key instead of Secretkey):

```
wallet_add_secret_keys Secretkey
```

Copy the **wallet address** and add it to the stake:

```
node_start_staking WALLET_ADDRESS
```

Checking that the node is ok:

<pre><code><strong>cd /$HOME/massa/massa-client/ &#x26;&#x26; ./massa-client -p YOUR_PASSWORD wallet_info
</strong></code></pre>

### It remains only to buy a roll for our coins. We go to the client: <a href="#id-1326" id="id-1326"></a>

<pre><code><strong>cd /$HOME/massa/massa-client/ &#x26;&#x26; ./massa-client -p YOUR_PASSWORD
</strong></code></pre>

and in the client we buy a roll using the command:

```
buy_rolls YOUR_WALLET_ADDRESS Number_of_rolls Commission

# In the above case, the command looks like this:

buy_rolls A1zpSerGtbwjiAEK3yueeTAFTgFzrFtJ3p33c4ACu9ytDFQTAjU 1 0
```
