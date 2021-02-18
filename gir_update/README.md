# The Gir Update

On Feb 18th at roughly 14:30UTC we had our first update. Since there seemed to be a bit of confusion about the process I decided to try to explain the process a bit more. The explanations won't be too technical but rather give a general overview. Hope this helps.

## How the Upgrade Module Works

The upgrade module allows to define upgrades on chain via governance. An upgrade proposal must contain an upgrade height and name. You can check this by running "regen q gov proposal 3" on your node. The height is set to 138650 and the name is set to "Gir". If the proposal passes the chain will throw a panic as soon as the defined height is reached. The panic message will contain the upgrade name. Restarting the same binary will do nothing except panicking again. You'll need to build the binary that can handle the upgrade with the name "Gir". Only that binary will be able to run after the upgrade height.

## How Cosmovisor Works
Cosmovisor basically waits for regen to panic. Checks the name provided by the panic. Checks if it knows a suitable binary and switches the binary automatically. This means you will not run the regen binary directly but inderictly through Cosmovisor.

When you start Cosmovisor it will run the binary found in 'current' (see below). That's why you have to set the symbolic link when setting up cosmovisor ($ ln -s -T ${HOME}/.regen/cosmovisor/genesis ${HOME}/.regen/cosmovisor/current).
As soon as cosmovisor registers a panic, it will use the name found in the panic ('Gir') to look for a directory with the same name within the 'upgrades' directory. If it finds it it will change the symbolic link  'current' to point to the upgraded binary within the 'Gir' directory. Now that the symbolic link is set it will simply start the binary found in current. 

```
cosmovisor
+ genesis
|  |
|  +bin
|    |
|    + regen  (v0.6.0)
|
+ upgrades
|  |
|  +Gir
|    |
|    +bin
|      |
|      + regen  (v0.6.1)
|
+ current  (symbolic link)   <-- This will point to different binaries before and after the upgrade
```


## Some Issues

### Build Binary Without EXPERIMENTAL Flag

Some people hat issues because they didn't use the EXPERIMENTAL flag when building the version v0.6.1. This meant that they had the correct version, but the handler for the upgrade module did not work. For this problem to occur it didn't matter whether the Cosmovisor or the manual approach was chosen.

The correct way to build v0.6.1 was 

```
$ EXPERIMENTAL=true make build
```

### Unclarity How Cosmovisor Works

Some people were uncertain whether the upgrade succeeded. When they used the Cosmovisor and ran "regen version" after the upgrade the still got v0.6.0. The reason for this was that Cosmovisor doesn't change the binary in ~/go/bin it organizes its binaries in its own directory, which is not included in the PATH. Since block creation didn't continue immediatly people thought they did something wrong and thought Cosmovisor failed when they checked their version via "regen version".

### Stopping Node before the final block was reached

Some validators seem to have stopped their nodes prematurely, which resulted in incompatible block hashes. This could be fixed by restarting the chain with v0.6.0 and waiting for the panic and then upgrading again.

### Wrong Expectations

This is just me guessing, but I think some validators didn't expect the block creation to halt for so long. So when no blocks were created directly after the update they assumed the error is on their side, they panicked and tried to fix something that wasn't broken. For all validators that are still unsure why the chain halted: A block can only be created if 2/3 of the voting power agree on the block. Since voting power is distributed very evenly in this network, this means that 2/3 of all validators must upgrade successfully before block creation can continue. Earlier today this took about 6 hours on the cosmoshub which had an update as well.






