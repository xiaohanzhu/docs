# Meter Mining Guide

Mining is how cryptocurrency is created and one way to acquire coins. It is an intensive computing activity, and involves the use of specialized hardware to get rewarded for processing transaction blocks on a blockchain. Meter (MTR) itself is no exception to this.

There are a number of interlinked factors to consider when it comes to mining. Just to name a few:

* Whether it is more economical or profitable to mine or buy cryptocurrency.
* What blockchain to focus on, and why.
* How to approach mining. For example, doing it all yourself, cooperating with others, or have somebody else do it for you.

# Making The Case for Mining

Every miner will have their own reasons whether or not to choose mining. For example, under some circumstances it may be highly profitable to mine, while in other situations the costs may be too high and the return on investment too low.

When it comes to owning equipment, the main expenses involved will include the cost of the equipment, and the expenses involved in operating and maintaining it. Mining equipment is specialized hardware that is much more expensive than a standard PC computer, and requires a big upfront investment. That equipment also needs to be housed somewhere, under optimal conditions, such as keeping it cool while running. And, the other big operational factor is the price of electricity and dependability of delivery wherever the equipment is being operated.

When these challenges can be met in the right combination, the other side that needs to be looked at is the return. If the expenses are manageable, and the return on each coin is high enough, then that should equate to a good profit margin. However, it may be the case that at the time of mining a coin it is not actively being traded yet, but the anticipated return sometime later is expected to be profitable.

Mining Meter at this time is in the testing phase, and production mining is set to begin soon. This provides a very good incentive for early adopters to more easily and economically acquire MTR through mining, before it becomes more widely available, and as more miners begin to direct their resources towards the Meter Network.     

## How to Mine

Generally speaking, miners may choose to operate their own equipment, and gain all the rewards for their efforts, or join together with others in a mining pool, and share the rewards (see What is a Mining Pool). In some cases people can subscribe to a pool and gain some of the rewards, but without owning any equipment of their own. This is called cloud mining. Each has its own benefits and drawbacks, and it is up to each person to determine which approach suits them best.

The remainder of this document is in support of those that already operate their own equipment, or wish to, and are interested in mining Meter. Primarily this will include those that will mine directly on their own, mining pools that choose to support Meter, or those that want to setup cloud mining for Meter. However, this information might also be of interest to others, such as those that are already part of a mining pool, and want to request that their pool add support for Meter.  

### What is a Mining Pool?

When miners group together to use their combined processing power this is called a mining pool. The rewards from mining are split proportionally according to the amount of hash power miners contribute to the pool.

Mining pools can be split into both public ones and private ones. Public pools will generally charge a fee

Mining is strictly voluntary, and as such miners can opt to switch their mining capabilities whenever they choose, according to their own objectives or criteria. For example, if a miner that traditionally was in a Bitcoin mining pool decided to divert some or all processing power to mine another blockchain, such as Meter, it is simply a case of making a few minor configuration changes to point the mining equipment to the new pool.

## Mining Hardware

ASIC miners are specialized hardware machines that are required to efficiently mine Meter.

Since Meter uses SHA256, the same as Bitcoin, miners can use the same hardware for mining both coins, and the mining setup for the two is very similar.

### Hardware Setup Guide

Meter uses an account based system, rather than the UTXO based method that Bitcoin uses. However, this difference only amounts to a small change that needs to be made in the configuration.

Follow [this guide](https://www.bitcoin.com/guides/how-to-setup-a-bitcoin-asic-miner-and-what-they-are) on installing and configuring your ASICs.

## The Meter Mining Pool

Meter is actively working with mining pools for support. However, in the meantime there are two dedicated Meter mining pools currently running for test purposes:

```
stratum+tcp://34.222.111.82:3256
stratum+tcp://54.184.235.97:3256
```

The following are the rough production parameters for different mining hardware on the Meter testnet, tuned to 1 meter = 10 kwh on an Antminer S9:

|                      | Power | Hash | Efficiency | Meter/Day | Margin Index |
|----------------------|-------|------|------------|-----------|--------------|
| AntMiner S9          | 1323  | 13.5 | 98         | 31.75     | 1.00         |
| WhatsMiner M10       | 3500  | 55   | 63.64      | 129.36    | 1.54         |
| Inno Silicon T3+ 52T | 2200  | 52   | 42.31      | 79.42     | 2.32         |
| AntMiner S17Pro      | 2094  | 53   | 39.51      | 80.95     | 2.48         |

These results are still relatively slow now. However, closer to the mainnet launch, parameters will be tuned to more efficient mining hardware, and achieve faster end results.

The Meter mining pool status statistics can be viewed at http://54.184.235.97:8088/stats.

### Joining the Meter Mining Pool

!> BE AWARE NO REWARD will be distributed from these pools, as the logic has not been implemented yet.

In order to configure a miner to join a Meter mining pool, the following fields in the `Miner configuration` tab of the ASIC's web panel need to be set:

![miner_configuration_example](./conf_s9.png)

Where:

* `URL` is the address of the mining pool.
* `Worker` is the Meter wallet address.
* `Password` is the password associated with the wallet.

## Starting a Mining Pool on the Meter Network

An example implementation of the Meter mining pool is available on [GitHub](https://github.com/meterio/meter-nomp), based on the open source nomp Bitcoin mining pool. This code has been provided to show the changes needed compared to standard bitcoin mining pools, and is not intended to be used in a production environment.

### Requirements

There are three things required to run Meter nomp: Node.js, Redis, and a connection to a coin daemon on the Meter Network, which is a Meter full node that monitors transactions on both the Meter PoW and PoS chains.

On the testnet, coin deamons have already been setup by the Meter team. Normally a pool operator would setup its own coin daemon, and this will be possible in the future.

The following are the minimal version requirements for Node.js and Redis. If older versions than the following are used (e.g. installed by a package manager) then problems will arise:

* [Node.js](http://nodejs.org/) v0.10+ ([follow these installation instructions](https://tecadmin.net/install-nodejs-with-nvm/#))
* [Redis](http://redis.io/) v2.6+ ([follow these instructions](http://redis.io/topics/quickstart))

**Important Warning!** It is always a good idea to learn about and understand any software that you are using. An important security measure to implement for nomp is to secure Redis so it cannot be accessed externally. An easy way to do this is to include `bind 127.0.0.1` in your `redis.conf` file, and use a firewall with strict rules in place to only allow accessing Redis locally. For more information please read [Security](http://redis.io/topics/security). Another good place to start for additional information about using Redis for nomp is [Data Persistence](http://redis.io/topics/persistence).

### Downloading & Installing

Clone the repository and run `npm update` for all the dependencies to be installed:

```bash
git clone https://github.com/meterio/meter-nomp.git
cd meter-nomp
npm update
```

### Optional: Using Docker

Rather than using the host system's Redis and Node.js, it is possible to containerize everything using Docker. The following describes this optional approach with Docker Compose. Do the following before continuing with the remaining steps, and in further sections any additional adjustments that need to be made will be highlighted.

Create the following `docker-compose.yml` file inside the `meter-nomp` sub-directory in the previous step.

```version: '3.5'
services:
  redis:
    image: "redis:alpine"
    networks:
      backend:
        ipv4_address: 172.16.238.10
  node:
    image: "node:10"
    user: "node"
    working_dir: /home/node/app
    volumes:
      - ./:/home/node/app
    depends_on:
      redis:
        condition: service_healthy
    networks:
      - frontend
      - backend
    expose:
      - "8088:8088"
    command: "npm init.js"

  networks:
    frontend:
      name: nomp_frontend
      driver: custom-driver-1
    backend:
      driver: bridge
      ipam:
        driver: default
        config:
        - subnet: 172.16.238.0/24
          gateway: 172.16.238.1

```

### Portal Configuration

Inside the `config_example.json` file, ensure the default configuration will work for your environment, then copy the file to `config.json`.

**Field Description**

```js
{
    /* Specifies the level of log output verbosity. Anything more severe than the level specified
       will also be logged. */
    "logLevel": "debug", //or "warning", "error"

    /* By default meter-NOMP logs to console and gives pretty colors. If you direct that output to a
       log file then disable this feature to avoid nasty characters in your log file. */
    "logColors": true,


    /* The NOMP CLI (command-line interface) will listen for commands on this port. For example,
       blocknotify messages are sent to NOMP through this. */
    "cliPort": 17117,

    /* By default 'forks' is set to "auto" which will spawn one process/fork/worker for each CPU
       core in your system. Each of these workers will run a separate instance of your pool(s),
       and the kernel will load balance miners using these forks. Optionally, the 'forks' field
       can be a number for how many forks will be spawned. */
    "clustering": {
        "enabled": true,
        "forks": "auto"
    },

    /* Pool config file will inherit these default values if they are not set. */
    "defaultPoolConfigs": {

        /* Poll RPC daemons for new blocks every this many milliseconds. */
        "blockRefreshInterval": 1000,

        /* If no new blocks are available for this many seconds update and rebroadcast job. */
        "jobRebroadcastTimeout": 55,

        /* Disconnect workers that haven't submitted shares for this many seconds. */
        "connectionTimeout": 600,

        /* (For MPOS mode) Store the block hashes for shares that aren't block candidates. */
        "emitInvalidBlockHashes": false,

        /* This option will only authenticate miners using an address or mining key. */
        "validateWorkerUsername": true,

        /* Enable for client IP addresses to be detected when using a load balancer with TCP
           proxy protocol enabled, such as HAProxy with 'send-proxy' param:
           http://haproxy.1wt.eu/download/1.5/doc/configuration.txt */
        "tcpProxyProtocol": false,

        /* If under low-diff share attack we can ban their IP to reduce system/network load. If
           running behind HAProxy be sure to enable 'tcpProxyProtocol', otherwise you'll end up
           banning your own IP address (and therefore all workers). */
        "banning": {
            "enabled": true,
            "time": 600, //How many seconds to ban worker for
            "invalidPercent": 50, //What percent of invalid shares triggers ban
            "checkThreshold": 500, //Perform check when this many shares have been submitted
            "purgeInterval": 300 //Every this many seconds clear out the list of old bans
        },

        /* Used for storing share and block submission data and payment processing. */
        "redis": {
            "host": "127.0.0.1",
            "port": 6379
        }
    },

    /* This is the front-end. Its not finished. When it is finished, this comment will say so. */
    "website": {
        "enabled": true,
        /* If you are using a reverse-proxy like nginx to display the website then set this to
           127.0.0.1 to not expose the port. */
        "host": "0.0.0.0",
        "port": 8088,
        /* Used for displaying stratum connection data on the Getting Started page. */
        "stratumHost": "example.com",
        "stats": {
            /* Gather stats to broadcast to page viewers and store in redis for historical stats
               every this many seconds. */
            "updateInterval": 15,
            /* How many seconds to hold onto historical stats. Currently set to 24 hours. */
            "historicalRetention": 43200,
            /* How many seconds worth of shares should be gathered to generate hashrate. */
            "hashrateWindow": 300
        },
        /* Not done yet. */
        "adminCenter": {
            "enabled": true,
            "password": "password"
        }
    },

    /* Redis instance of where to store global portal data such as historical stats, proxy states,
       ect.. */
    "redis": {
        "host": "127.0.0.1",
        "port": 6379
    },


    /* With this switching configuration, you can setup ports that accept miners for work based on
       a specific algorithm instead of a specific coin. Miners that connect to these ports are
       automatically switched a coin determined by the server. The default coin is the first
       configured pool for each algorithm and coin switching can be triggered using the
       cli.js script in the scripts folder.

       Miners connecting to these switching ports must use their public key in the format of
       RIPEMD160(SHA256(public-key)). An address for each type of coin is derived from the miner's
       public key, and payments are sent to that address. */
    "switching": {
        "switch1": {
            "enabled": false,
            "algorithm": "sha256",
            "ports": {
                "3333": {
                    "diff": 10,
                    "varDiff": {
                        "minDiff": 16,
                        "maxDiff": 512,
                        "targetTime": 15,
                        "retargetTime": 90,
                        "variancePercent": 30
                    }
                }
            }
        },
        "switch2": {
            "enabled": false,
            "algorithm": "scrypt",
            "ports": {
                "4444": {
                    "diff": 10,
                    "varDiff": {
                        "minDiff": 16,
                        "maxDiff": 512,
                        "targetTime": 15,
                        "retargetTime": 90,
                        "variancePercent": 30
                    }
                }
            }
        },
        "switch3": {
            "enabled": false,
            "algorithm": "x11",
            "ports": {
                "5555": {
                    "diff": 0.001
                }
            }
        }
    },

    "profitSwitch": {
        "enabled": false,
        "updateInterval": 600,
        "depth": 0.90,
        "usePoloniex": true,
        "useCryptsy": true,
        "useMintpal": true
    }
}
```

### Coin Configuration

Inside the `coins` directory, ensure that a json file exists for Meter coin, and if no create it. Here is an example of what the file should look like:

```js
{
    "name": "Meter",
    "symbol": "MTR",
    "algorithm": "sha256",
}
```

For additional documentation on how to configure coins and their different algorithms see [these instructions](github.com/meterio/meter-stratum-pool#module-usage).

### Pool Configuration

There is a json config file `meter.json`. Make sure to configure the appropriate fields in this file, especially the address fields and the `daemon`/`daemons` fields.

For example, assuming the miner's account in Meter for receiving a reward is the `rewardBeneficiary` field, in the example configuration below the value is `0a05c2d862ca051010698b69b54278cbaf945ccb`. In the same example, the value for Meter test coin in the `daemons` section is configured as follows:

```js
[
    {
        "host": "test.meter.io",
        "port": 8332,
        "user": "testuser",
        "password": "testpass"
    }
]
```

**Field Description**

```js
{
    "enabled": true, //Set this to false and a pool will not be created from this config file
    "coin": "meter.json", //Reference to coin config file in 'coins' directory

    "address": "mi4iBXbBsydtcc5yFmsff2zCFVX4XG7qJc", //Address to where block rewards are given

    /* Block rewards go to the configured pool wallet address to later be paid out to miners,
       except for a percentage that can go to, for examples, pool operator(s) as pool fees or
       or to donations address. Addresses or hashed public keys can be used. Here is an example
       of rewards going to the main pool op, and pool co-owner.*/
    "rewardRecipients": {
        "n37vuNFkXfk15uFnGoVyHZ6PYQxppD3QqK": 1.5, //1.5% goes to pool op
        "mirj3LtZxbSTharhtXvotqtJXUY7ki5qfx": 0.5, //0.5% goes to a pool co-owner
    },

    /* this is Pos beneficiary. It is 20 bytes address. Mining reward goes to this address in Pos ssytem. */
    "rewardBeneficiary": "0a05c2d862ca051010698b69b54278cbaf945ccb",

    /* payment must be disabled */
    "paymentProcessing": {
        "enabled": false,

        /* Every this many seconds get submitted blocks from redis, use daemon RPC to check
           their confirmation status, if confirmed then get shares from redis that contributed
           to block and send out payments. */
        "paymentInterval": 30,

        /* Minimum number of coins that a miner must earn before sending payment. Typically,
           a higher minimum means less transactions fees (you profit more) but miners see
           payments less frequently (they dislike). Opposite for a lower minimum payment. */
        "minimumPayment": 0.01,

        /* This daemon is used to send out payments. It MUST be for the daemon that owns the
           configured 'address' that receives the block rewards, otherwise the daemon will not
           be able to confirm blocks or send out payments. */
        "daemon": {
            "host": "127.0.0.1",
            "port": 8332,
            "user": "testuser",
            "password": "testpass"
        }
    },

    /* Each pool can have as many ports for your miners to connect to as you wish. Each port can
       be configured to use its own pool difficulty and variable difficulty settings. varDiff is
       optional and will only be used for the ports you configure it for. */
    "ports": {
        "3008": {
            "diff": 1
        },

        "3032": { //A port for your miners to connect to
            "diff": 32, //the pool difficulty for this port

            /* Variable difficulty is a feature that will automatically adjust difficulty for
               individual miners based on their hashrate in order to lower networking overhead */
            "varDiff": {
                "minDiff": 8, //Minimum difficulty
                "maxDiff": 512, //Network difficulty will be used if it is lower than this
                "targetTime": 15, //Try to get 1 share per this many seconds
                "retargetTime": 90, //Check to see if we should retarget every this many seconds
                "variancePercent": 30 //Allow time to very this % from target without retargeting
            }
        },
        "3256": { //Another port for your miners to connect to, for ASIC miner
            "diff": 200000 //The pool difficulty
            "varDiff": {
                "minDiff": 100000,
                "maxDiff": 2000000,
                "targetTime": 15,
                "retargetTime": 90,
                "variancePercent": 30
            }

        }
    },

    /* More than one daemon instances can be setup in case one drops out-of-sync or dies. */
    "daemons": [
        {   //Main daemon instance
            "host": "127.0.0.1",
            "port": 8332,
            "user": "testuser",
            "password": "testpass"
        }
    ],

    /* This allows the pool to connect to the daemon as a node peer to receive block updates.
       It may be the most efficient way to get block updates (faster than polling, less
       intensive than blocknotify script). It requires the additional field "peerMagic" in
       the coin config. */
    "p2p": {
        "enabled": false,

        /* Host for daemon */
        "host": "127.0.0.1",

        /* Port configured for daemon (this is the actual peer port not RPC port) */
        "port": 19333,

        /* If your coin daemon is new enough then it will support a p2p
           feature that prevents the daemon from spamming our peer node with unnecessary
           transaction data. Assume its supported but if you have problems try disabling it. */
        "disableTransactions": true
    },

    /* Enabled this mode and shares will be inserted into in a MySQL database. You may also want
       to use the "emitInvalidBlockHashes" option below if you require it. The config options
       "redis" and "paymentProcessing" will be ignored/unused if this is enabled. */
    "mposMode": {
        "enabled": false,
        "host": "127.0.0.1", //MySQL db host
        "port": 3306, //MySQL db port
        "user": "me", //MySQL db user
        "password": "mypass", //MySQL db password
        "database": "mtr", //MySQL db database name

        /* Checks for valid password in database when miners connect. */
        "checkPassword": true,

        /* Unregistered workers can automatically be registered (added to database) on stratum
           worker authentication if this is true. */
        "autoCreateWorker": false
    }
}
```

You can create as many of these pool config files as you want. If you are creating multiple pools, ensure that they have unique stratum ports.

For more information on these configuration options see the [pool module documentation](https://github.com/meterio/meter-stratum-pool#module-usage).

### Start the Portal

After all the configuration files has been set up, you're ready to start your mining pool in NodeJS:

```bash
node init.js
```