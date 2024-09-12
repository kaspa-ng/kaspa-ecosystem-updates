# Update in Kaspa – Does it affect your integration

## KRC20 Launch

Some infos here about krc20, layer 2, high load on kaspa especially at start


## What changed in Kaspa in the last weeks

### Dust prevention KIP-0009
To prevent a UTXO bloat attack, the KIP-0009 was created and implemented. Dust prevention primarily kicks in when sending very small amounts or a high amount of outputs. This significantly increases the transaction mass. As a result, higher fees are required.
Please check the demo script to calculate the TX mass.

### Fee Estimate
As blocks become increasingly full with the start of KRC20, miners may need to prioritize transactions, meaning those with lower fees could remain in the mempool longer. 

**Note: KRC20 transactions tend to be more generous with their fees.**

In the past, we recommended using the minimum fee or simply around 0.0001 KAS per UTXO.
If Kaspa becomes a high load, you might need to pay more fees to ensure faster transactions.
With the latest update to kaspad (and the REST API), there's now a new endpoint that calculates the fee based on the current load. 


Please check it out here: http://api.kaspa.org/info/fee-estimate.


### And now?
High network load and dust prevention mean that transactions need to be carefully planned and calculated. In this GitHub repository, you’ll find an examples of

* how the storage mass, the compute mass and the resulting mass of a transaction is calculated
* how the corresponding fees are determined

