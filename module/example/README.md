# Examples

Runnable examples, tested on Linux and MacOs.

Before running anything:

- Ensure the [latest release of the native Convex Shell is installed](https://github.com/Convex-Dev/convex.cljc/tree/main/module/shell)
- Add an alias `cvx` in your system pointing to that standalone executable
- Clone this repository
- Always run examples from the root of this repository


## Load testing against a local network

The following example creates a local network of 5 peers and simulates 1000
users transacting according to a selected scenario. It is important that the
Convex Shell is aliased in your system as `cvx` since each peer will run in its
own process (launched automatically).

Available example scenarios:

| Name       | Description                     |
|------------|---------------------------------|
| `dao`      | Simple DAO-like behavior        |
| `empty`    | NO-OP. just testing consensus   |
| `fungible` | Transfers of fungible tokens    |
| `nft`      | Transfers of NFTs               |
| `torus`    | Automated Market Maker trades   |
| `transfer` | Transfer of native Convex Coins |

Run a scenario by substituting `$NAME` for any name from the table above.

First, prepare the genesis state for the network:

    cvx '(.dep.exec (quote (example sim local $NAME))) (prepare)'

Then, start the network and the load generator:

    cvx '(.dep.exec (quote (example sim local $NAME))) (run)'
