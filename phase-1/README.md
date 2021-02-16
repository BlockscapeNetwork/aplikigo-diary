# Phase 1

## Performance Testing

The performance testing challenge was a limited time load test on the network. It started 15:00 UTC Feb 11th and went on for three hours.

### The Tasks

For this challenge we had three tasks:
* Send as many transactions as possible in the limited time frame.
* Include as many transactions as possible in a single block (minimum 5000)
* Include as many messages as possible in a single transaction

### Our Outcome

Out of all the tasks, we only managed to make an attempt at the messages per transaction task.

We managed to squeeze 7430 msgs in one transaction at block heigth 42035, which used up 98510927 gas.
The transaction hash is B8807B4F18B1CE5F83C05E30D4F630AB8B6E826CFEC244DE8B9B5E5A14CE1CDE. It doesn't seem to come up in the explorer, we guess it's because of the size. To take a look at the transaction query your node with 'regen q tx B8807B4F18B1CE5F83C05E30D4F630AB8B6E826CFEC244DE8B9B5E5A14CE1CDE'.

We also planned to go for the transactions per block task but couldn't get it to work in time.

### How we put the messages into a single transaction

We took the regen-ledger software and modiefied it a bit. We changed the creation of the send transaction to include the send message a specified amount of times in the transaction. Then we would sign and broadcast it with the cli as usual albeit with a higher gas limit. You can check out the changes herer: https://github.com/MarcelMWS/cosmos-sdk/tree/v0.41.0-regen-tx-spam-0.0.1

You could achieve the same by creating the transaction with the --generate-only flag, than edit the resulting json by copy-pasting the contained send message several times (in our case 7429 times). This works without a problem since the messages are included in a transaction within an array. You should also adjust the gas limit (we just picked a very high gas limit and then set a fixed fee of 5000utree, which worked since not all nodes specified minimum gas prices). Now that you have your transaction completed you can sign it and then broadcast it. So the three commands you need are:
* regen tx bank send [...] --generate-only
* regen tx sign [...]
* regen tx broadcast [...]

The resulting transaction executes the same send messages several times which means tokens get send from one account to a second account 7430 times.

Why 7430? This is the highest amount we managed to squeeze in and still make it work. We also had to change some configurations to send such a big payload (>1MB).

Since this worked we wanted to expand on this idea, but instead of sending it from one account to a second, we wanted to send from one account to 7340 individual accounts and thus creating them on chain. As a next step we wanted to create transactions from all those 7340 accounts back to our validator account, sign those transactions, and then broadcast them in quick succession to achieve the task of most transactions in a block. Sadly we couldn't make it work. The send transactions would always time out. We wrote a script to do all of this in go. You can find it here: [Spam Script](./spammer.go)

The script is not complete, and not everything is documented well, but if what you read peeked your interest why not take a look.

### Problems with the explorer

As mentioned above the explorer seems to have some issues. For example our big transaction can't be found using the explorer. Another problem we encountered is that it shows more missing blocks per validator than actually exist. When checking some of the missed blocks on our node we found our signature in them. Our own monitoring also didn't find as many missed blocks. So please doublecheck important information.