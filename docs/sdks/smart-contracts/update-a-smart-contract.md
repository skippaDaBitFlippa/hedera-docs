# Update a smart contract

A transaction that allows you to modify the smart contract entity state like admin keys, proxy account, auto renew period and memo. This transaction does not update the contract that is tied to the smart contract entity. The contract tied to the entity is immutable. The contract entity is immutable if an admin key is not specified. Once the transaction has been successfully executed on a Hedera network the previous field values will be updated with the new ones. To get a previous state of a smart contract instance, you can query a mirror node for that data. Any null field is ignored (left unchanged)

**Transaction Signing Requirements**

* If only the expiration time is being modified, then no signature is needed on this transaction other than for the account paying for the transaction itself.
* If any other smart contract entity property is being modified, the transaction must be signed by the admin key.
* If the admin key is being updated, the new key must sign

**Transaction Fees**

* Please see the transaction and query [fees](../../../mainnet/fees/#transaction-and-query-fees) table for base transaction fee
* Please use the [Hedera fee estimator](https://hedera.com/fees) to estimate your transaction fee cost

**Smart Contract Properties**

| Field                            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| -------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Admin Key**                    | Sets the new admin key and its fields can be modified arbitrarily if this key signs a transaction to modify it. If this is null, then such modifications are not possible, and there is no administrator that can override the normal operation of this smart contract instance. Note that if it is created with no admin keys, then there is no administrator to authorize changing the admin keys, so there can never be any admin keys for that instance. The bytecode will also be immutable. |
| **Proxy Account**                | The ID of the account to which this account is proxy staked. If proxyAccountID is null, or is an invalid account, or is an account that isn't a node, then this account is automatically proxy staked to a node chosen by the network, but without earning payments. If the proxyAccountID account refuses to accept proxy staking, or if it is not currently running a node, then it will behave as if proxyAccountID was null.                                                                  |
| **Auto Renew Period**            | The new period that the instance will charge its account every this many seconds to renew.                                                                                                                                                                                                                                                                                                                                                                                                        |
| **Automatic Token Associations** | The maximum number of tokens that this contract can be automatically associated with (i.e., receive air-drops from).                                                                                                                                                                                                                                                                                                                                                                              |
| **Memo**                         | The new memo to be associated with this contract.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |

| Constructor                       | Description                                      |
| --------------------------------- | ------------------------------------------------ |
| `new ContractUpdateTransaction()` | Initializes the ContractUpdateTransaction object |

```java
new ContractUpdateTransaction()
```

### Methods

{% tabs %}
{% tab title="V2" %}
| Method                                       | Type                                             | Requirement |
| -------------------------------------------- | ------------------------------------------------ | ----------- |
| `setContractId(<contractId>)`                | [ContractId](../specialized-types.md#contractid) | Required    |
| `setAdminKey(<keys>)`                        | Key                                              | Optional    |
| `setProxyAccountId(<accountId>`)             | [AccountId](../specialized-types.md#accountid)   | Optional    |
| `setContractMemo(<memo>)`                    | String                                           | Optional    |
| `setContractExpirationTime(<expirationTime)` | Instant                                          | Optional    |
| `setMaxAutomaticTokenAssociations()`         | int                                              | Optional    |
| `setContractMemo(<memo>)`                    | String                                           | Optional    |
| `setAutoRenewPeriod(<autoRenewPeriod>)`      | Duration                                         | Optional    |

{% code title="Java" %}
```java
//Create the transaction 
ContractUpdateTransaction transaction = new ContractUpdateTransaction()
    .setContractId(contractId)
    .setAdminKey(adminKey);

//Modify the default transaction fee
ContractUpdateTransaction modifyTransactionFee = transaction.setMaxTransactionFee(new Hbar(20));

//sign with the new admin key, sign with the old admin key, sign with the client operator account and submit to a Hedera network
TransactionResponse txResponse = modifyTransactionFee.freezeWith(client).sign(newAdminKey).sign(adminKey).execute(client);

//Get the consensus status of the transaction
Status transactionStatus = receipt.status;

System.out.println("The consensus status of the transaction is " +transactionStatus);

//v2.0.0
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
//Create the transaction
const transaction = await new ContractUpdateTransaction()
    .setContractId(contractId)
    .setAdminKey(adminKey)
    .setMaxTransactionFee(new Hbar(20))
    .freezeWith(client);

//Sign the transaction with the old admin key and new admin key
const signTx = await (await transaction.sign(newAdminKey)).sign(adminKey);

//Sign the transaction with the client operator private key and submit to a Hedera network
const txResponse = await signTx.execute(client);

//Request the receipt of the transaction
const receipt = await txResponse.getReceipt(client)

//Get the consensus status of the transaction
const transactionStatus = receipt.status;

console.log("The consensus status of the transaction is " +transactionStatus);

//v2.0.5
```
{% endcode %}

{% code title="Go" %}
```java
//Create the transaction
transaction := hedera.NewContractUpdateTransaction().
        SetContractID(contractId).
        SetBytecodeFileID(byteCodeFileID).
        SetAdminKey(adminKey)

//Change the default max transaction fee to 2 hbars
modifyMaxTransactionFee := transaction.SetMaxTransactionFee(hedera.HbarFrom(2, hedera.HbarUnits.Hbar))

//Freeze the transaction
freezeTransaction, err := modifyMaxTransactionFee.FreezeWith(client)
if err != nil {
        panic(err)
}

//Sign with the old admin key, sign with the new admin key, sign with the client operator account and submit to a Hedera network
txResponse, err := freezeTransaction.Sign(newAdminKey).Sign(adminKey).Execute(client)
if err != nil {
        panic(err)
}

//Request the receipt of the transaction
receipt, err = txResponse.GetReceipt(client)
if err != nil {
        panic(err)
}

//Get the transaction consensus status
transactionStatus := receipt.Status

fmt.Printf("The transaction consensus status %v\n", transactionStatus)

//v2.0.0
```
{% endcode %}
{% endtab %}

{% tab title="V1" %}
| Method                                       | Type            | Requirement          |
| -------------------------------------------- | --------------- | -------------------- |
| `setContractId(<contractId>)`                | ContractId      | Required             |
| `setAdminKey(<publicKey>)`                   | Ed25519PubicKey | Optional             |
| `setBytecodeFileId(<fileId>)`                | FileId          | Deprecated \[0.20.0] |
| `setProxyAccountId(<accountId>`)             | AccountId       | Optional             |
| `setContractMemo(<memo>)`                    | String          | Optional             |
| `setContractExpirationTime(<expirationTime)` | Instant         | Optional             |
| `setAutoRenewPeriod(<autoRenewPeriod>)`      | Duration        | Optional             |

{% code title="Java" %}
```java
ContractUpdateTransaction transaction = new ContractUpdateTransaction()
     .setContractId(newContractId)
     .setContractMemo("Update the memo of the smart contract");

TransactionId txId = transaction.execute(client);

TransactionReceipt receipt = txId.getReceipt(client);

Status transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus)

//v1.3.2
```
{% endcode %}

{% code title="JavaScript" %}
```javascript
const transaction = new ContractUpdateTransaction()
     .setContractId(newContractId)
     .setContractMemo("Update the memo of the smart contract");

const txId = await transaction.execute(client);

const receipt = await txId.getReceipt(client);

const transactionStatus = receipt.status;

System.out.println("The transaction consensus status is " +transactionStatus)

//v1.4.4
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Get transaction values

{% tabs %}
{% tab title="V2" %}
| Method                 | Type                                             | Requirement |
| ---------------------- | ------------------------------------------------ | ----------- |
| `getContractId()`      | [ContractId](../specialized-types.md#contractid) | Required    |
| `getAdminKey()`        | Key                                              | Optional    |
| `getBytecodeFileId()`  | [FileId](../specialized-types.md#fileid)         | Optional    |
| `getProxyAccountId()`  | [AccountId](../specialized-types.md#accountid)   | Optional    |
| `getContractMemo()`    | String                                           | Optional    |
| `getAutoRenewPeriod()` | Duration                                         | Optional    |

{% code title="Java" %}
```java
//Create the transaction with an admin key
ContractUpdateTransaction transaction = new ContractUpdateTransaction()
    .setContractId(contractId)
    .setAdminKey(adminKey);

//Get admin key
transaction.getAdminKey()

//v2.0.0
```
{% endcode %}

{% code title="JavaScript" %}
```java
//Create the transaction with an admin key
const transaction = new ContractUpdateTransaction()
    .setContractId(contractId)
    .setAdminKey(adminKey);

//Get admin key
transaction.getAdminKey()

//v2.0.0
```
{% endcode %}

{% code title="Go" %}
```java
//Create the transaction
transaction := hedera.NewContractUpdateTransaction().
        SetContractID(contractId).
        SetBytecodeFileID(byteCodeFileID).
        SetAdminKey(adminKey)

//Get admin key
transaction.GetAdminKey()

//v2.0.0
```
{% endcode %}
{% endtab %}
{% endtabs %}
