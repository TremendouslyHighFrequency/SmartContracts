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
| Bob          | Test Asset 2           |               | 100,000           |
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

Initial Transaction:
Let’s say Charlie decides to begin the bootstrapping. Charlie simply sends her assets to the bootstrap contract p2s with the registers as given above.

On-Chain Transaction:
https://explorer.ergoplatform.com/en/transactions/50c715dd7bc9514b20b737a8fa71a30e009edd62302a871cedc4e35c2ad82ba9

Add Assets:
Any other party can now add their assets to the contract. To do so the user signs a transaction that creates the exact same UTXO and simply adds their assets to it. Let’s say Alice does this here in this transaction:
https://explorer.ergoplatform.com/en/transactions/636c01f7845b62e313d0ded3db502cdf3fcf60c693f438e8f2119823732ee748

Note, the contract allows users to add multiple assets in a single transaction, so for Bob, he may wish to take advantage of this, so he doesn’t need to sign two transactions.

Withdraw Assets
At any stage before the assets are sent to the sale contract, any user can withdraw their assets from the bootstrap contract. There are 3 necessary outputs for this transaction which must be set in order as:

[Screenshot 2022-12-20 at 8 20 10 AM](https://user-images.githubusercontent.com/112732047/208715174-a7ca5502-48e5-4355-a71f-1ec059d1c12a.png)

Let’s say Charlie decides to withdraw her assets. Here is her transaction on-chain:
https://explorer.ergoplatform.com/en/transactions/b0000ea032049f26097987ca348bc08b70558005194866426435a02e4d48da44

Send to Sale Contract

Once all parties have provided their assets, its time to send to the sale contract! There are 2 necessary outputs for this transaction which must be set in order as:

[Screenshot 2022-12-20 at 8 20 15 AM](https://user-images.githubusercontent.com/112732047/208715199-a80e98fc-3477-473f-9c11-d9055c2c73f1.png)


Note, the withdrawal NFT id will be the same as the boxId of the bootstrap contract box which is used as an input for the transaction. This also means the sale UTXO should be in Input position 0.

Registers R4-R6 of the withdrawal NFT UTXO represent the name, description and decimals of the NFT which can be set to anything convenient for UI purposes.
Example transaction on-chain:
https://explorer.ergoplatform.com/en/transactions/391a8b8c351eed05e02c1016e39f92012061fca3f2f9dbb7ab9c6a08cd4a0b91

The Sale Contract:
Now that the parties have their assets held in the sale contract, it is time for a buyer to make a purchase. To recap, lets have a look at Alice, Bob, Charlie and Dan’s sale box, which is a box under the sale contract address holding their assets and terms of sale.

[Screenshot 2022-12-20 at 8 20 29 AM](https://user-images.githubusercontent.com/112732047/208715241-b3a3c769-bfee-4f32-ac56-c501d11ad98d.png)

Buyer makes a purchase
Anyone can purchase one copy of each asset from the sale box by selecting the sale box as an input for a transaction where the buyer sends one of each token to themselves and creates outputs containing the sale revenue for each artist.

Here is a transaction which does this:
https://explorer.ergoplatform.com/en/transactions/0c24eefaf14eb26e99dd4a316c90edf0db542b643dbcd518f38bb232125a830c

An illustration is provided below to explain what each output represents in this transaction:
<img width="954" alt="image" src="https://user-images.githubusercontent.com/112732047/192367222-2b842b9a-b50e-4269-9aac-8e0674f1b4ae.png">

Also notice that the sale box’s registers remain identical, except for R6, which is simply reduced by 1 indicating one less set of assets are for sale.

The Withdraw NFT Contract:
The withdraw NFT contract holds the withdrawal NFTs on behalf of the artists, which may be used to terminate the sale contract. The only spending action is to withdraw a single withdrawal NFT.

Withdrawal:
Only one NFT can be withdrawal token can be withdrawn at a time. To withdraw, there are 3 necessary outputs which must be set in order as:

[Screenshot 2022-12-20 at 8 20 36 AM](https://user-images.githubusercontent.com/112732047/208715285-af5cd4ff-25a6-4f8f-b2b2-c7d5c39ce34c.png)

Let’s say Charlie decides to withdraw her withdrawal NFT, this is her transaction on chain:
https://explorer.ergoplatform.com/en/transactions/c2d81f4a14fab6b72b4517389596988615a3858112b67515b4dbdc39bf6665ec

Withdrawal Indication Contract:
The withdrawal indication contract is the destination artists can send their withdrawal NFT if they wish to terminate a sale contract.
To begin, one party needs to send their withdrawal NFT to the indication contract with the following registers:

[Screenshot 2022-12-20 at 8 20 40 AM](https://user-images.githubusercontent.com/112732047/208715320-bc971ec4-64af-480b-844a-a1f7002a79cc.png)

Let’s say Alice begins this process, with her on-chain transaction here:
https://explorer.ergoplatform.com/en/transactions/be63577df49d47c4ad8e944156680440c73df50aeff5e8bbdfeff6c7f0a25c8a

Notice that an Array of (Long, Coll[Byte]) tuples was chosen, where a dummy value can be included. This is not necessary, but was included for consistency among all the register types, it can easily be changed to just an array of Coll[Byte] if preferred.

Like the bootstrap contract, parties can now withdraw or deposit withdrawal NFTs to the indication contract until all parties have deposited their NFT, at which point there are no more spending paths. This is to prevent any exploits of the termination conditions of the sale contract.
  
Add withdrawal NFT
To add a withdrawal NFT the user signs a transaction that creates the exact same UTXO and simply adds their NFT to it and appends their address in the NFT provider’s array. Let’s say Bob does this here in this transaction:
https://explorer.ergoplatform.com/en/transactions/2112b35a004a63b551eda1abefd3c90af792d54d04c7bf75a1c3ac6ee0b7a04d

Withdraw withdrawal NFT
At any stage before all the withdrawal NFTs are held in the indication contract, any user can withdraw their assets from the indication contract. There are 3 necessary outputs for this transaction which must be set in order as:

[Screenshot 2022-12-20 at 8 20 45 AM](https://user-images.githubusercontent.com/112732047/208715365-eca03398-a951-408b-b5e4-126f10641fbf.png)
[Screenshot 2022-12-20 at 8 20 49 AM](https://user-images.githubusercontent.com/112732047/208715468-5ced50c4-28f2-454e-a233-43668be1ce1e.png)

Let’s say Bob decides to withdraw his assets. Here is his transaction on-chain:
https://explorer.ergoplatform.com/en/transactions/1858047763fa98b95f653b47ffcba73b445dee24a66c8e718fcd8cf16ee76f1c

The Sale Contract (Termination):
Once the withdrawal indication contract holds all the withdrawal NFTs, the sale contract can be terminated. Termination occurs over multiple transaction, with each transaction sending assets to a single artist. There are 3 necessary outputs for a termination transaction which must be set in order as:

A subtle difference to all the other transactions described here is that a dataInput is used. When constructing a termination transaction, simply include the withdrawal indication box as a dataInput. This is used to prove that termination consensus has been reached.

On-chain example (The dataInput is not visible on the explorer):
https://explorer.ergoplatform.com/en/transactions/b839dd8c096ecea7de324b0e5efb17bb7b8d50891f8602aba9b70cc41f7cb16c
