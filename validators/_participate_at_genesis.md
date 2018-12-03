## Participate in genesis as a validator

__Note__: This section only concerns validators that want to be in the genesis file. If the chain you want to validate is already live, skip this section.

__Note__: `Gaia-9002` and `Game of stakes` will not use this process. They will be bootsrapped using Tendermint seed validators. You will just need to use the [create-validator](#create-your-validator) command in order to join as a validator for these networks.

If you want to participate in genesis as a validator, you need to justify that you (or a delegator) have some stake at genesis, create one (or multiple) transaction to bond this stake to your validator address, and include this transaction in the genesis file.

We thus need to distinguish two cases:

- Case 1: You want to bond the initial stake from your validator's address.
- Case 2: You want to bond the initial stake from a delegator's address.

### Case 1: The initial stake comes from your validator's address

In this case, you will create a `gentx`:

```bash
joyd gentx \
  --amount <amount_of_delegation> \
  --commission-rate <commission_rate> \
  --commission-max-rate <commission_max_rate> \
  --commission-max-change-rate <commission_max_change_rate> \
  --pubkey <consensus_pubkey> \
  --name <key_name>
```

__Note__: This command automatically store your `gentx` in `~/.joyd/config/gentx` for it to be processed at genesis.

::: tip
Consult `joyd gentx --help` for more information on the flags defaults.
:::

A `gentx` is a JSON file carrying a self-delegation. All genesis transactions are collected by a `genesis coordinator` and validated against an initial `genesis.json`. Such initial `genesis.json` contains only a list of accounts and their coins. Once the transactions are processed, they are merged in the `genesis.json`'s `gentxs` field.

### Case 2: The initial stake comes from a delegator's address

In this case, you need both the signature of the validator and the delegator. Start by creating an unsigned `create-validator` transaction, and save it in a file called `unsignedValTx`:

```bash
joycli tx stake create-validator \
  --amount=5joy \
  --pubkey=$(joyd tendermint show-validator) \
  --moniker="choose a moniker" \
  --chain-id=<chain_id> \
  --from=<key_name> \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --address-delegator="address of the delegator" \
  --generate-only \
  > unsignedValTx.json
```

Then, sign this `unsignedValTx` with your validator's private key, and save the output in a new file `signedValTx.json`:

```bash
joycli tx sign unsignedValTx.json --from=<validator_key_name> > signedValTx.json
```

Then, pass this file to the delegator, who needs to run the following command:

```bash
joycli tx sign signedValTx.json --from=<delegator_key_name> > gentx.json
```

This `gentx.json` needs to be included in the `~/.joyd/config/gentx` folder on the validator's machine to be processed at genesis, just like in case 1 (except here it needs to be copied manually into the folder).

### Copy the Initial Genesis File and Process Genesis Transactions

Fetch the `genesis.json` file into `joyd`'s config directory.

```bash
mkdir -p $HOME/.joyd/config
curl https://raw.githubusercontent.com/mnaamani/joystream-testnets/master/latest/genesis.json > $HOME/.joyd/config/genesis.json
```

Note we use the `latest` directory in the [testnets repo](https://github.com/mnaamani/joystream-testnets)
which contains details for the latest testnet. If you are connecting to a different testnet, ensure you get the right files.

You also need to fetch the genesis transactions of all the other genesis validators. For now there is no repository where genesis transactions can be submitted by validators, but this will as soon as we try out this feature in a testnet.

Once you've collected all genesis transactions in `~/.joyd/config/gentx`, you can run:

```bash
joyd collect-gentxs
```

The previous command will collect all genesis transactions and finalise `genesis.json`. To verify the correctness of the configuration and start the node run:

```bash
joyd start
```
