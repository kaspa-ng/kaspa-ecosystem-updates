# Update in Kaspa – Does it affect your integration

## KRC20 Launch

KRC-20 is a protocol proposed by independent developers that implements inscriptions on Kaspa. However, since the only
long-term stored information in a regular full, non-archival, Kaspa node is UTXO - and transactions are deleted at each
pruning - inscriptions data is added not to the transactions but to the UTXO being created.

Protocol capabilities include, among other things, token deployment (parameter initialization and optional pre-minting)
and minting. Both operations are performed "on top of" a regular Kaspa transaction (actually 2 consequential ones but
that's irrelevant here), meaning that to deploy or mint a certain token to a specific address, you need to send some
Kaspa to that address.

## KRC20 Beta in the past

During the open beta testing of KRC-20, an issue arose in this area that led, from the perspective of an ordinary user,
to an unexpected slowdown of the Kaspa network. The issue stemmed from the decision by the Kasware wallet developers (
which was the only publicly available tool for operating tokens using the KRC-20 protocol at the time of beta testing),
stating that the amount sent from one address to another during deployment or minting would be 0.2 Kaspa. This decision
was justified in order to spare the user the need to have a large amount of Kaspa to conduct token operations. However,
the chosen parameter did not account for the implications of KIP-9, in the sense that for the vast majority of users,
the value of 0.2 Kaspa was significantly less than the size of UTXOs at their disposal. This situation resulted in the
input UTXO being split into two outputs during a transaction, one of which remained comparable in size to the input,
while the other — 0.2 KAS — was significantly smaller than these both.

In accordance with the logic and constants chosen in the current implementation of KIP-9, this led to transactions
getting a significant storage mass, such that no more than about 20 similar transactions could fit in each Kaspa block (
specifically, a typical transaction mass was approximately 25,000 grams in that case, with a block mass limit of 500,000
grams).

Due to the described situation, a massive backlog has accumulated in the mempool, reaching a peak of around 37,000
transactions. And all these transactions came with high fees, as the mining fee is set at 1 KAS in accordance with
KRC-20 rules. This fee level is so high that even with the high transaction mass, it provides an elevated fee/mass
ratio (4000 sompi/gram, whereas typical values range from 1 to 4 sompi/gram). And this ratio is a key parameter in the
weighted probabilistic selection of transactions from the mempool. As a result, regular users faced issues: their
transactions with low default fees had almost no chance of being included in a block, both due to the large number of
contenders in the mempool and, additionally, because of their low fee/mass ratio.

In the absence of mechanisms to indicate current network fees, replace-by-fee, and, in most wallets, even the ability to
specify a custom fee itself, transactions from regular users remained in the mempool for hours, creating the impression
that the network was not functioning.

## What changed in Kaspa in the last weeks

### Dust prevention KIP-0009 and the storage mass

To prevent a UTXO bloat attack, the KIP-0009 was created and implemented. Dust prevention primarily kicks in when
sending very small amounts (in KAS) or a high count of outputs. This significantly increases the transaction mass. As a result,
higher fees are required.
Please check the demo script to calculate the TX mass.

**rough-and-ready rule 1:** The storage mass is only dependent from the count and the values of the TX's inputs and
outputs \
**rough-and-ready rule 2:** Don't use outputs lower than 0.2 KAS, as this increases the TX mass significantly

See the examples below, to check how transaction mass is now calculated.

### Fee Estimate

As blocks become increasingly full with the start of KRC20, miners may need to prioritize transactions, meaning those
with lower fees could remain in the mempool longer.

**Note: KRC20 transactions tend to be more generous with their fees.**

In the past, we recommended using the minimum fee or simply around 0.0001 KAS per UTXO.
If Kaspa becomes a high load, you might need to pay more fees to ensure faster transactions.
With the latest update to kaspad (and the REST API), there's now a new endpoint that calculates the fee based
on the current load.

Fee estimation API: https://github.com/kaspanet/rusty-kaspa/blob/master/rpc/grpc/core/proto/rpc.proto#L919

The REST-API also offers this endpoint: http://api.kaspa.org/info/fee-estimate.

### Replace-by-fee (RBF)

The node now offers a RBF API
RBF (replace-by-fee) API: https://github.com/kaspanet/rusty-kaspa/blob/master/rpc/grpc/core/proto/rpc.proto#L311

## And now? What to do for exchanges / wallets / pools ?

High network load and dust prevention mean that transactions need to be carefully planned and calculated. In this GitHub
repository, you’ll find an examples of

* how the storage mass, the compute mass and the resulting mass of a transaction is calculated
* how the corresponding fees are determined

Check

* Regular TX: [tx1_regular.md](tx1_regular.md)
* Low Output TX (storage mass): [tx1_low_output.md](tx1_low_output.md)

## Explorer tooling (REST-API, Database and DB-Filler)

If you are using our explorer tools to manage your Kaspa integration (REST-API Server, Kaspa-DB Filler,
Kaspa-DB PostgreSQL) this section is interesting for you.

We are expecting a high load on Kaspa in the next days, which possibly makes the inserts of transactions longer than now
without load. However, the DB-filler should be able to handle this, even if it is slower.

However, we already have prepared a new database schema and a new database filler written in RUST, which is faster than the old
configuration set. It is running now for several weeks with very good efficiency. 

Since the PostgreSQL schema changed, the REST-Server had to be adjusted as well.

* REST-API Server [https://github.com/kaspa-ng/kaspa-rest-server](https://github.com/kaspa-ng/kaspa-rest-server)
* Database filler can be found here: https://hub.docker.com/r/supertypo/kaspa-db-filler-ng
* Migration Script, to migrate from old to new schema: Database filler can be found
  here: https://github.com/supertypo/kaspa-db-filler-migration

Feel free to use our docker-compose set
### testnet 10

```yaml
services:
  kaspa_rest_server_t10:
    container_name: kaspa_rest_server_t10
    image: kaspanet/kaspa-rest-server:latest
    restart: unless-stopped
    deploy:
      resources:
        limits:
          cpus: "2"
          memory: 1G
    environment:
      KASPAD_HOST_1: kaspad_t10:16210
      SQL_URI: postgresql+asyncpg://postgres:thisIsMYsecretAndNotyours@kaspa_db_t10:5432/postgres
      DISABLE_PRICE: "true"
      NETWORK_TYPE: "testnet"
    ports:
      - "8100:8000"
    command: pipenv run gunicorn -b 0.0.0.0:8000 -w 2 -k uvicorn.workers.UvicornWorker main:app --timeout 120
    links:
      - kaspad_t10
      - kaspa_db_t10

  kaspa_db_filler_t10:
    container_name: kaspa_db_filler_t10
    image: supertypo/kaspa-db-filler-ng:latest
    restart: unless-stopped
    deploy:
      resources:
        limits:
          cpus: "2"
          memory: 2G
    command: -u -b 1 -n testnet-10 -s ws://kaspad_t10:17210 -d postgres://postgres:thisIsMYsecretAndNotyours@kaspa_db_t10:5432/postgres
    links:
      - kaspad_t10
      - kaspa_db_t10

  kaspad_t10:
    container_name: kaspad_t10
    image: supertypo/rusty-kaspad:latest
    restart: unless-stopped
    environment:
     NETWORK_TYPE: testnet
    ports:
      - "16211:16211"
      - "16210:16210"
      - "17210:17210"
    volumes:
      - kaspad_t10:/app/data/
    command: kaspad --yes --unsaferpc --ram-scale=0.3 --testnet --utxoindex --rpclisten=0.0.0.0:16210 --rpclisten-borsh=0.0.0.0:17210 --nologfiles --disable-upnp

  kaspa_db_t10:
    container_name: kaspa_db_t10
    image: postgres:16-alpine
    restart: unless-stopped
    shm_size: 1gb
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: thisIsMYsecretAndNotyours
      POSTGRES_DB: postgres
    ports:
      - "65433:5432"
    deploy:
      resources:
        limits:
          cpus: "2"
          memory: 2G
    volumes:
      - kaspa_db_t10:/var/lib/postgresql/data/

volumes:
  kaspad_t10:
  kaspa_db_t10:
```

### mainnet

```yaml
services:
  kaspa_rest_server_mainnet:
    container_name: kaspa_rest_server_mainnet
    image: kaspanet/kaspa-rest-server:latest
    restart: unless-stopped
    deploy:
      resources:
        limits:
          cpus: "2"
          memory: 1G
    environment:
      KASPAD_HOST_1: kaspad_mainnet:16110
      SQL_URI: postgresql+asyncpg://postgres:thisIsMYsecretAndNotyours@kaspa_db_mainnet:5432/postgres
      DISABLE_PRICE: "true"
      NETWORK_TYPE: "mainnet"
    ports:
      - "80:8000"
    command: pipenv run gunicorn -b 0.0.0.0:8000 -w 2 -k uvicorn.workers.UvicornWorker main:app --timeout 120
    links:
      - kaspad_mainnet
      - kaspa_db_mainnet

  kaspa_db_filler_mainnet:
    container_name: kaspa_db_filler_mainnet
    image: supertypo/kaspa-db-filler-ng:latest
    restart: unless-stopped
    deploy:
      resources:
        limits:
          cpus: "2"
          memory: 2G
    command: -u -b 1 -n mainnet -s ws://kaspad_mainnet:17110 -d postgres://postgres:thisIsMYsecretAndNotyours@kaspa_db_mainnet:5432/postgres
    links:
      - kaspad_mainnet
      - kaspa_db_mainnet

  kaspad_mainnet:
    container_name: kaspad_mainnet
    image: supertypo/rusty-kaspad:latest
    restart: unless-stopped
    environment:
     NETWORK_TYPE: mainnet
    ports:
      - "16111:16111"
      - "16110:16110"
      - "17110:17110"
    volumes:
      - kaspad_mainnet:/app/data/
    command: kaspad --yes --unsaferpc --ram-scale=0.3 --utxoindex --rpclisten=0.0.0.0:16110 --rpclisten-borsh=0.0.0.0:17110 --nologfiles --disable-upnp

  kaspa_db_mainnet:
    container_name: kaspa_db_mainnet
    image: postgres:16-alpine
    restart: unless-stopped
    shm_size: 1gb
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: thisIsMYsecretAndNotyours
      POSTGRES_DB: postgres
    ports:
      - "65432:5432"
    deploy:
      resources:
        limits:
          cpus: "2"
          memory: 2G
    volumes:
      - kaspa_db_mainnet:/var/lib/postgresql/data/

volumes:
  kaspad_mainnet:
  kaspa_db_mainnet:
```


