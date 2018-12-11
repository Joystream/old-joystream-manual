# Join the Testnet

::: tip Current Testnet
See the [testnet repo](https://github.com/joystream/joystream-testnets) for
information on the latest testnet, including the correct version
of Joystream to use and details about the genesis file.
:::

**Please ensure you have the [joystream binaries](./installation.md) installed.**

If you ran a full node on a previous testnet, please skip to [Upgrading From Previous Testnet](#upgrading-from-previous-testnet).

## Setting Up a New Node

These instructions are for setting up a brand new full node from scratch.

First, initialize the node and create the necessary config files:

```bash
joyd init --moniker <your_custom_moniker>
```

::: warning Note
Only ASCII characters are supported for the `--moniker`. Using Unicode characters will render your node unreachable.
:::

You can edit this `moniker` later, in the `~/.joyd/config/config.toml` file:

```toml
# A custom human readable name for this node
moniker = "<your_custom_moniker>"
```

You can edit the `~/.joyd/config/gaiad.toml` file in order to enable the anti spam mechanism and reject incoming transactions with less than a minimum fee:

```
# This is a TOML config file.
# For more information, see https://github.com/toml-lang/toml

##### main base config options #####

# Validators reject any tx from the mempool with less than the minimum fee per gas.
minimum_fees = ""
```


Your full node has been initialized! Please skip to [Genesis & Seeds](#genesis--seeds).

## Upgrading From Previous Testnet

These instructions are for full nodes that have ran on previous testnets and would like to upgrade to the latest testnet.

### Reset Data

First, remove the outdated files and reset the data.

```bash
rm $HOME/.joyd/config/addrbook.json $HOME/.joyd/config/genesis.json
joyd unsafe-reset-all
```

Your node is now in a pristine state while keeping the original `priv_validator.json` and `config.toml`. If you had any sentry nodes or full nodes setup before,
your node will still try to connect to them, but may fail if they haven't also
been upgraded.

::: danger Warning
Make sure that every node has a unique `priv_validator.json`. Do not copy the `priv_validator.json` from an old node to multiple new nodes. Running two nodes with the same `priv_validator.json` will cause you to double sign.
:::

### Software Upgrade

Now it is time to upgrade the software. Start in the directory where you previously cloned the joystream-builder repo:

```bash
cd $HOME/joystream-builder
git fetch --all && git checkout master
./build.sh
# copy your platform's binaries to your home bin folder
cp bin/linux_amd64/joy* ~/bin
```

::: tip
*NOTE*: If you have issues at this step, review the [install instructions](./installation.md) step
:::

Note we use `master` here since it contains the latest stable release.
See the [testnet repo](https://github.com/joystream/joystream-testnets)
for details on which version is needed for which testnet.

Your full node has been cleanly upgraded!

## Genesis & Seeds

### Copy the Genesis File

Fetch the testnet's `genesis.json` file into `joyd`'s config directory.

```bash
mkdir -p $HOME/.joyd/config
curl https://raw.githubusercontent.com/joystream/joystream-testnets/master/latest/genesis.json > $HOME/.joyd/config/genesis.json
```

Note we use the `latest` directory in the [testnets repo](https://github.com/joystream/joystream-testnets)
which contains details for the latest testnet. If you are connecting to a different testnet, ensure you get the right files.

Next configure seed nodes to connect to.

### Add Seed Nodes

Your node needs to know how to find peers. You'll need to add healthy seed nodes to `$HOME/.joyd/config/config.toml`. The `testnets` repo contains links to the seed nodes for each testnet. If you are looking to join the running testnet please [check the repository for details](https://github.com/joystream/joystream-testnets) on which nodes to use.

The `Persistent Peer` field contains the connection string.

You can also ask for peers on the [Joystream Chat Room](https://chat.joystream.org)

For more information on seeds and peers, you can [read this](https://github.com/tendermint/tendermint/blob/develop/docs/tendermint-core/using-tendermint.md#peers).

## Run a Full Node

Start the full node with this command:

```bash
joyd start
```

Check that everything is running smoothly:

```bash
joycli status
```

View the status of the network with the [Joystream Explorer](http://explorer.joystream.org). Once your full node syncs up to the current block height, you should see it appear.

## Export State

Joystream can dump the entire application state to a JSON file, which could be useful for manual analysis and can also be used as the genesis file of a new network.

Export state with:

```bash
joyd export > [filename].json
```

You can also export state from a particular height (at the end of processing the block of that height):

```bash
joyd export --height [height] > [filename].json
```

If you plan to start a new network from the exported state, export with the `--for-zero-height` flag:

```bash
joyd export --height [height] --for-zero-height > [filename].json
```

## Upgrade to Validator Node

You now have an active full node. What's the next step? You can upgrade your full node to become a Joystream Validator. The top 100 validators have the ability to propose new blocks to the Joystream Network. Continue onto [the Validator Setup](./validators/validator-setup.md).
