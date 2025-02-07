# zkLink Exodus Model
The Server, Prover and React App of ZkLink Exodus Model.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
    - Backend
        - [Create the Database](#create-the-database)
        - [Build the Project](#build-the-project)
        - [Configure the Environment Variables](#configure-the-environment-variables)
        - [Recover zklink State](#Recover-ZkLink-State)
        - [Start Exodus Server and Exodus Prove](#Start-Exodus-Server-and-Exodus-Prove)
    - [Frontend setup](exodus-interface/README.md)
- [License](#license)

## Prerequisites

You need to select a suitable server first, and we provide three recommended configurations.

| AWS EC2 Instance | Price     | Prove Performance |
|------------------|-----------|-------------------|
| c5a.4xlarge      | $0.768/hr | 1.6 proofs/min    |
| c5a.12xlarge     | $2.304/hr | 4 proofs/min      |
| c5a.24xlarge     | $4.608/hr | 5.5 proofs/min    |

First, install basic lib:


Before you begin, you will need to have the following software installed:
- [Rust and rustup](https://www.rust-lang.org/tools/install)
- [PostgreSQL](https://www.postgresql.org/download/linux/ubuntu/) database. Before install:
```shell
sudo apt-get install libpq-dev libssl-dev pkg-config axel
```
- [Diesel](http://diesel.rs/) command-line tool for Rust. You can install it by running:
```shell
cargo install diesel_cli --no-default-features --features postgres
```
Load git repository:
```shell
git clone https://github.com/zkLinkProtocol/recover_state_server.git
```

## Getting Started
### Download the setup
Run the following command in the `zklink_keys` directory:
```shell
axel -c https://universal-setup.ams3.digitaloceanspaces.com/setup_2%5E21.key
```
### Create the Database
First, to configure the `DATABASE_URL` environment:
```shell
export DATABASE_URL=postgres://user:password@localhost/plasma
```
For the first time, please refer to [psql.md](docs/psql.md) for setting the password.

Then, to create the database, run the following command in the `storage` directory:
```shell
diesel database setup
```
This will create the necessary tables in your PostgreSQL database.

### Build the Project

To build the project in release mode, run the following command:
```shell
cargo build --release
```
This will create a binary file in the `target/release` directory.

### Configure the Environment Variables
First, there is a `.env.eg` file in the root path of our project, copy and rename it to `.env`.
```shell
cp .env.e.g .env
```
Then, you need to modify the following configuration:

**note:**
1. `RUNTIME_CONFIG_ZKLINK_HOME` and `DATABASE_URL` must be configured by your current environment
2. Before "dunkerque," a link will be published here that will display contract and chain configurations. if test, only use default.


| configuration variables                        | description                                                  | example                                                              |
|------------------------------------------------|--------------------------------------------------------------|----------------------------------------------------------------------|
| `RUNTIME_CONFIG_ZKLINK_HOME`                   | The current project path                                     | /home/xxx_user/recover_state_server                                  |
| `DATABASE_URL`                                 | the default is local.                                        | postgres://user:passwd@localhost/plasma                              |
| `CHAIN_IDS`                                    | The chains that supported, the chain id is defined by zkLink | 1,2                                                                  |
| `CHAIN_{CHAIN_ID}_CHAIN_ID`                    | The chain ID defined by zkLink                               | 1                                                                    |
| `CHAIN_{CHAIN_ID}_CHAIN_TYPE`                  | The layer1 chain type                                        | EVM                                                                  |
| `CHAIN_{CHAIN_ID}_GAS_TOKEN`                   | The gas token price symbol                                   | MATIC                                                                |
| `CHAIN_{CHAIN_ID}_IS_COMMIT_COMPRESSED_BLOCKS` | Whether the data is fully on-chain in this chain             | true                                                                 |
| `CHAIN_{CHAIN_ID}_CONTRACT_DEPLOYMENT_BLOCK`   | The block number of CONTRACT deployed                        | 33377564                                                             |
| `CHAIN_{CHAIN_ID}_CONTRACT_ADDRESS`            | The zkLink main contract address                             | "0x517aa9dec0E297B744aC7Ac8ddd8B127c1993055"                         |
| `CHAIN_{CHAIN_ID}_CONTRACT_GENESIS_TX_HASH`    | The zkLink contract deployed tx hash                         | "0x5c576039ffefce307ffbc5556899ee0772efcf2046051cc4fe9ca633987061ca" |
| `CHAIN_{CHAIN_ID}_CLIENT_CHAIN_ID`             | The real chain id defined in layer1                          | 80001                                                                |

### Recover ZkLink state
To recover the state, run the following `recover` command:
```shell
./exodus.sh recover
```
This command will take several hours to complete.

If you want to see the recover state process, please:
```shell
tail -f log/recover_state.log
```
**Please be patient and wait until the command finishes.**

If there is an interruption, run the `continue` command
```shell
./exodus.sh continue
```

### Start Exodus Server and Exodus Prove
To start the server, run the following command:
```shell
./exodus.sh server
```
To start the prover and generate proofs for the server to receive proof tasks, run the following command:
```shell
./exodus.sh prover
```
Please refer to prover [README.md](prover/README.md) for detailed command details

### Close Exodus Server and Exodus Prove
```shell
./exodus.sh stop
```

## License
This project is licensed under the MIT License - see the `LICENSE` file for details.
