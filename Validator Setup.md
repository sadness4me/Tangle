# Register your Session Key with the Network for your Node
```
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "author_rotateKeys", "params":[]}' http://localhost:9933
```
If you get a similar result, then everything is great:

{"jsonrpc":"2.0","result":"**0xa0very0long0hex0string**","id":1}

- copy the key (in bold) we will need it in the next step

# Setup via Validator Tab

* Go to the [**polkadot website**](https://polkadot.js.org/apps/?rpc=wss%253A%252F%252Frpc.tangle.tools&ref=blog.webb.tools#/accounts) and first create wallet account
* For stash we configure **Set on-chain Identity** for identification
* We create a validator. To do this, select **Network - Staking - Accounts - Validator**:
  * Choose your wallet at **slash account**
  * Click **Next** button
  * Paste key copied at **Register your Session Key with the Network for your Node** into **keys from rotatekeys** field

As soon as a place among the validators becomes available, you will appear in the Staking Overview tab, but for now you can find yourself on the Waiting tab.

# Backup your validator node
```
Keep the keys located in and $HOME/.tangle/node-key$HOME/.tangle/data/chains/tangle-standalone-testnet/keystore/
```

# Delete your node
```
systemctl stop tangle && \
systemctl disable tangle && \
rm /etc/systemd/system/tangle.service && \
systemctl daemon-reload && \
cd \
rm -r .tangle
```

**Faucet: https://discord.gg/bvgXZbVK post your wallet in validators channel**<br>
**Docs: https://docs.webb.tools/docs/**
