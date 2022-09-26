The big picture:

For a general overview of all the contracts, this diagram can be helpful. The diagram describes the context of each contract, the content of the involved UTXOs and the spending paths.

<img width="1432" alt="image" src="https://user-images.githubusercontent.com/112732047/192360881-5035ed14-ded6-4384-a75e-c8f10d9ae1ba.png">

Description through examples:
The rest of this document will describe all the involved transactions through examples. Explorer links for these transactions have been provided so that you can use these examples as models for building your own transactions.

The Bootstrap Contract:
Alice, Bob, Charlie and Daniel wish to form a mutual agreement contract like so:

| Party        | Assets to provide      | Sale Revenue  | Number of assets  |
|--------------|------------------------|---------------|-------------------|
| Alice        | Test Asset 1           | 0.1 Erg       | 100,000           |
| Bob          | Test Asset 2           | 0.1 Erg       | 100,000           |
|              | Test Asset 3           | 0.5 Erg       | 100,000           |
| Charlie      | Test Asset 4           | 0.2 Erg       | 100,000           |
| Daniel       | Test Asset 5           | 0.1 Erg       | 100,000           |


To begin, one party needs to send their assets to the bootstrap contract. The registers of this
contract would look like so:

| Register                   | Content (Before Serialization)                                                                      |
|----------------------------|-----------------------------------------------------------------------------------------------------|
| R4 (Share for each artist) | [(100000000, Alice), (250000000, Bob), (250000000, Bob), (200000000, Charlie), (100000000, Daniel)] |
| R5 (Assets to be provided) | [Test Asset 1 id, Test Asset 2 id, Test Asset 3 id, Test Asset 4 id, Test Asset 5 id]               |
| R6 (Number of assets)      | 100,000                                                                                             |

Where R4, contains an array of tuples containing the sale revenue for each artist (in nanoErg) and the address of the party.
Notice that Bob is listed in the shares array twice. This is because sale revenue is paid for each asset being sold and Bob has two assets. Because of this, we simply divide Bob’s revenue by 2 to obtain his revenue per his asset provided.

Initial Transaction
Let’s say Charlie decides to begin the bootstrapping. Charlie simply sends her assets to the bootstrap contract p2s with the registers as given above.

On-Chain Transaction:
https://explorer.ergoplatform.com/en/transactions/50c715dd7bc9514b20b737a8fa71a30e009edd62302a871cedc4e35c2ad82ba9

Add Assets
Any other party can now add their assets to the contract. To do so the user signs a transaction that creates the exact same UTXO and simply adds their assets to it. Let’s say Alice does this here in this transaction:
https://explorer.ergoplatform.com/en/transactions/636c01f7845b62e313d0ded3db502cdf3fcf60c693f438e8f2119823732ee748

Note, the contract allows users to add multiple assets in a single transaction, so for Bob, he may wish to take advantage of this, so he doesn’t need to sign two transactions.
