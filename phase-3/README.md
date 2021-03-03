# Phase 3

## Task 1

### Custom Messages for cw20_base

To create custom messages I modified the cw20_base contract. And added two new messages: robin_hood and distibute_wealth (yes with typo, sorry).

Both are messages you don't actually want in your contract, but are supposed to be fun.

---

**robin_hood**

The robin_hood message will look through all addresses that own tokens in this contract, will chose the account with the most tokens and will transfer half of his tokens to the signer of the message. So you can take from the rich and keep it for yourself.

The command:

```
regen tx wasm execute regen:1kj4anavh78wlx457j83tdunpc5rnqpj0pfp62x '{"robin_hood":{}}' --from testVal --chain-id aplikigo-1 --gas-prices 0.025utree
```

You can see one tx here: 57EF260430893DF1288E6FF25ECFAEE629E2B148C137D04181097F1A89885AAA

---

**redistibute_wealth**

This little function will revolutionize your market. It will take all tokens that exist and distribute them evenly among all addresses that had tokens of this contract at the time of execution.

The command:

```
regen tx wasm execute regen:1kj4anavh78wlx457j83tdunpc5rnqpj0pfp62x '{"redistibute_wealth":{}}' --from testVal --chain-id aplikigo-1 --gas-prices 0.025utree
```

You can see one tx here: 37B58DC6375F4949498E4F2D91DCF858F424BFEF17530B21C756966631A790D2

### Code

You can find the modified code [here](./modified_cw20_base).
The only files that haven been changed are [msg.rs](./modified_cw20_base/src/msg.rs) where the new messages have simply been added, and [contract.rs](./modified_cw20_base/src/contract.rs) where the handlers for those two messages have been added.