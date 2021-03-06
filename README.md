<details>
  <summary><b>Mission and Current situation</b></summary>

Currently if you're building a dapp on cardano you have one of few choices on how to agragate the Cardano blockchain data your dapp might need to function properly.

One of the biggest solutions and easiest thus far has been dbSync, which is pretty resource intensive and even after full sync still takes up quite a bit of HD space and takes quite a while to sync on top of need the cardano node to sync as well.

There is also services like blockfrost where a user can sign up for an account receive an API key and use blockfrost REST API to access whatever data you might need but it doesn't really give you a dapp and if blockfrost services go down so does your dapp.

However through out the last few months several projects have emerged that give you the power of DB sync and are a lot less cumbersome. They are not replacements for DB sync and usually you have to bundle one or two of these services together to achieve what DB sync offers in one service. But in return you have only maybe 3 or 4 micro services that still take up and use a lot less resources then DB sync will.

For example DB sync system requirements currently are:
```
32 Gigabytes of RAM or more.
4 CPU cores or more.
160 Gigabytes or more of disk storage. (Note this is without full cardano node sync which adds extra 60Gb or so for about a total of 220Gb right out the door.)
```

Noderunner:
```
12 Gigabytes of RAM or more.
4 CPU cores or more
140Gb or more this is with a full cardano-node synced.
```

So at this point you're probably wondering how the fuck does Noderunner do it. If you remember mention above about software coming out you can run as micro services to aggregate the data for your dapp like you would with DB sync?

The three major components of Noderunner are: Ogmios, Kupo, Oura. All three pieces of software are open source and are developed by Cardano developers for Cardano developers. 

With these  three tools running you will be able to have access to full UTXO history of an address, when they were created and spent, you can search by address or even by datum hash. You will also have access to assets metadata as long as they were created under the Metadata label 721.

And you will have access to data like Pool information, pool delegators, stake address information on which pool it's delegating too all for a fraction of a cost in resources DB sync takes and absolutely a LOT more feasible for a user to run at home.
</details>

**Components explained**

<details>
  <summary><b>Ogmios</b></summary>

https://github.com/CardanoSolutions/ogmios ??? by Cardano Solutions developed by KtorZ:

Ogmios is a lightweight bridge interface for cardano-node. It offers a WebSocket API that enables local clients to speak Ouroboros' mini-protocols via JSON/RPC.

Ogmios plays several key rolls in Noderunner Eco system. Number one it connects directly to Cardano Nodes IPC socket and most importantly can aggregate requested information from Cardano Node based on the JSON RPC call you specify. With Ogmios you can information like: 
```
-blockHeight: The chain???s highest block number.
-chainTip: The chain???s current tip.
-currentEpoch: The current epoch of the ledger.
-currentProtocolParameters: The current protocol parameters.
-delegationsAndRewards: Current delegation settings and rewards of given reward accounts.
-eraStart: The information regarding the beginning of the current era.
-eraSummaries: Era bounds and slotting parameters details, required for proper slot arithmetic.
-genesisConfig: Get a compact version of the era???s genesis configuration.
-ledgerTip: The most recent block tip known of the ledger.
-nonMyopicMemberRewards: Non-myopic member rewards for each pool. Used in ranking.
-poolIds: The list of all pool identifiers currently registered and active.
-poolParameters: Stake pool parameters submitted with registration certificates.
-poolsRanking: Retrieve stake pools ranking (a.k.a desirabilities).
-proposedProtocolParameters: The last update proposal w.r.t. protocol parameters, if any.
-rewardsProvenance: Get details about rewards calculation for the ongoing epoch.
-stakeDistribution: Distribution of the stake across all known stake pools.
-systemStart: The chain???s start time (UTC).
-utxo: Current UTXO, possibly filtered by output reference.
```
For more on Omgios and its API please visit: https://ogmios.dev/

**Sync Time**
There is no sync time for Ogmios.
</details>

<details>
  <summary><b>Kupo</b></summary>

https://github.com/CardanoSolutions/kupo by Cardano Solutions developed by KtorZ:

Kupo is fast, lightweight and configurable chain-index for the Cardano blockchain. It synchronizes data from the blockchain according to patterns matching addresses present in transaction outputs and builds a lookup table from matches to their associated output references, values and datum hashes.

Kupo is one of my favorite tools on Cardano. With the fact that being able to query the cardano node for UTXOs by Address is being deprecated Kupo becomes almost a must have tool for any Cardano developer.

With Kupo you are able to sync a index of Addresses and their UTXO state. It???ll flag unspent UTXOs for an address and it will flag spent ones and also tell you when when they were spent and which TX.

Kupo also goes a step further for the vasil/Babbage era and it will also lets you search UTXOs by their datum hash.

**Connection and Sync time**
Kupo can connect to either your Ogmios instance or through your Cardano nodes IPC socket to sync it???s database. Kupo takes about 24 hours to sync if you start from the Shelley ERA. And takes about 45Gb of hd space on Mainnet with every single address and it???s UTXO indexed currently existing on Cardano blockchain.
</details>

<details>
  <summary><b>Carp/Oura</b></summary>
  
https://dcspark.github.io/carp by Dc Spark <br />
https://github.com/txpipe/oura/releases by TxPipe Development lead by scarmuega

So why bundle Carp and Oura under the same category even though they're developed by two different groups?

Well, main reason is cause Cardano eco system is pretty fucking bad ass and developers like to collaborate. 

But also because Carp runs Oura under the hood to aggregate the data from cardano-node, then Carp takes this information and neatly places it into postgre sql for us.

Oura is a rust-native implementation of a pipeline that connects to the tip of a Cardano node through a combination of Ouroboros mini-protocol (using either a unix socket or tcp bearer), filters the events that match a particular pattern and then submits a succint, self-contained payload to pluggable observers called "sinks".

**Connection and Sync time**
Oura in Noderunner instance connects N2C to Cardano nodes socket directly to aggregate the mainnet asset metadata and it???s final database takes up about 4.5Gb of hd space and takes about 24hours to sync.
</details>

<details>
  <summary><b> Conclusion</b></summary>

With these three services and Cardano Node we are able to aggregate and create a small Eco system for our selves where we have access to pretty much all data that should suffice for majority of dapps created or being created at a fraction of the resources cost if you were needing to spin up db sync.

Now you will still need to sync your Cardano Node which takes the longest of the three to sync up and is the most resource heavy.

We will also provide DB boot strapping services for each service mentioned if you so choose to use them.

</details>
