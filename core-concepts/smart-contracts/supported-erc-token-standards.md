# Supported ERC Token Standards

Interact with Hedera Token Service fungible and non-fungible tokens in your Solidity contract using ERC-20 and ERC-721 standards.

## ERC-20 (Fungible)

### Supported

**name**

<mark style="color:purple;">`function name() public view returns (string)`</mark>

Returns the name of the token.

**symbol**

<mark style="color:purple;">`function symbol() public view returns (string)`</mark>

Returns the symbol of the token.

**decimals**

<mark style="color:purple;background-color:purple;">`function decimals() public view returns (uint8)`</mark>

Returns the number of decimals the token uses.

**totalSupply**

<mark style="color:purple;">`function totalSupply() external view returns (uint256)`</mark>

Returns the total supply of the token.

**balanceOf**

<mark style="color:purple;">`function balanceOf(address account) external view returns (uint256)`</mark>

Returns of the balance of the token in the specified account. The <mark style="color:purple;">`account`</mark> is the Hedera account ID <mark style="color:purple;">`0.0.x`</mark> in Solidity address format or the evm address of a contract that has been created via the `CREATE2` operation.

**transfer**

<mark style="color:purple;">`function transfer(address recipient, uint256 amount) external returns (bool)`</mark>

Transfer tokens from your account to a recipient account. The <mark style="color:purple;">`recipient`</mark> is the Hedera account ID <mark style="color:purple;">`0.0.x`</mark> in Solidity format or the evm address of a contract that has been created via `CREATE2` operation.

**allowance (previewnet)**

<mark style="color:purple;">`function allowance(address owner, address spender) external view returns (uint256)`</mark>

Returns the remaining number of tokens that `spender` will be allowed to spend on behalf of `owner` through `transferFrom`. This is zero by default. This value changes when `approve` or `transferFrom` are called. This works by loading the owner `FUNGIBLE_TOKEN_ALLOWANCES` from the accounts ledger and returning the allowance approved for `spender` The `owner` and `spender` address are the account IDs (0.0.num) in solidity format.&#x20;

**approve (previewnet)**

<mark style="color:purple;">`function approve(address spender, uint256 amount) external returns (bool)`</mark>

Sets `amount` as the allowance of `spender` over the caller's tokens.

This works by creating a synthetic `CryptoApproveAllowanceTransaction` with payer - the account that called the precompile (the message sender property of the message frame in the EVM).&#x20;

Fires an approval event with the following signature when executed:\
event Approval(address indexed owner, address indexed spender, uint256 value);

\
**transferFrom (previewnet)**

<mark style="color:purple;">`function transferFrom(address sender, address recipient, uint256 amount) external returns (bool)`</mark>

Moves `amount` tokens from `from` to `to` using the allowance mechanism. `amount` is then deducted from the caller's allowance.

This works by creating a synthetic `CryptoTransferTransaction` with fungible token transfers with the `is_approval` property set to true.

## ERC-721 (Non-Fungible)

### Supported

The following ERC-721 operations will be supported. Standard ERC-721 Events will be emitted as appropriate.

#### From interface ERC721

**balanceOf**

<mark style="color:purple;">`function balanceOf(address _owner) external view returns (uint256)`</mark>

Returns balance of the HTS non fungible token from the account owner. The <mark style="color:purple;">`_owner`</mark> is the Hedera account ID <mark style="color:purple;">`0.0.x`</mark> in Solidity format or the evm address of a contract that has been created via the `CREATE2` operation.

**ownerOf**

<mark style="color:purple;">`function ownerOf(uint256 _tokenId) external view returns (address)`</mark>

Returns the account ID of the specified HTS token owner. The <mark style="color:purple;">`_tokenId`</mark> is the Hedera token ID <mark style="color:purple;">`0.0.x`</mark> in Solidity format.

**approve (previewnet)**

<mark style="color:purple;">`function approve(address _approved, uint256 _tokenId) external payable`</mark>

Gives the spender permission to transfer a token (`tokenId`) to another account from the owner. The approval is cleared when the token is transferred.

This works by creating a synthetic `CryptoApproveAllowanceTransaction` with payer - the account that called the precompile (the message sender property of the message frame in the EVM).

If the `spender` address is 0, this creates a `CryptoDeleteAllowanceTransaction` instead and removes any allowances previously approved on the token.

Fires an approval event with the following signature when executed:

event Approval(address indexed owner, address indexed approved, uint256 indexed tokenId);

**setApprovalForAll (previewnet)**

<mark style="color:purple;">`function setApprovalForAll(address _operator, bool _approved) external`</mark>

Approve or remove an `operator` as an operator for the caller. Operators can call `transferFrom` for any token owned by the caller.

This works by creating a synthetic `CryptoApproveAllowanceTransaction` with payer - the account that called the precompile (the message sender property of the message frame in the EVM).

**getApproved (previewnet)**

<mark style="color:purple;">`function getApproved(uint256 _tokenId) external view returns (address)`</mark>

Returns the account approved for the specified `tokenId`.

This works by loading the `SPENDER` property of the token from the NFTs ledger.

**isApprovedForAll (previewnet)**

<mark style="color:purple;">`function isApprovedForAll(address _owner, address _operator) external view returns (bool)`</mark>

Returns if the `operator` is allowed to manage all of the assets of `owner`.

This works by loading the `APPROVE_FOR_ALL_NFTS_ALLOWANCES` property of the owner account and verifying if the list of approved for all accounts contains the account id of the `operator`.

**transferFrom (previewnet)**

<mark style="color:purple;">`function transferFrom(address _from, address _to, uint256 _tokenId) external payable`</mark>

Transfers a token (`tokenId`) from a Hedera account (`from`) to another Hedera account (`to`) in solidity format.

This works by creating a synthetic `CryptoTransferTransaction` with nft token transfers with the `is_approval` property set to true.

#### From interface ERC721Metadata

**name**

<mark style="color:purple;">`function name() external view returns (string _name)`</mark>

Returns the name of the HTS non fungible token.

**symbol**

<mark style="color:purple;">`function symbol() external view returns (string _symbol)`</mark>

Returns the symbol of the HTS non fungible token.

**tokenURI**

<mark style="color:purple;">`function tokenURI(uint256 _tokenId) external view returns (string)`</mark>

Returns the token metadata of the HTS non fungible token. This corresponds to the NFT meta data field when minting an NFT using HTS.

#### From interface ERC721Enumerable

**totalSupply**

<mark style="color:purple;">`function totalSupply() external view returns (uint256)`</mark>

Returns the total supply of the HTS non-fungible token.

### Not Supported

The following ERC-721 operations are currently not supported.

#### From interface ERC721

* <mark style="color:purple;background-color:purple;">`safeTransferFrom`</mark>

#### All semantics of interface ERC721TokenReceiver.

* Existing Hedera token association rules will take the place of such checks.

#### From interface ERC721Enumerable

* <mark style="color:purple;">`tokenByIndex`</mark>
* <mark style="color:purple;">`tokenOfOwnerByIndex`</mark>

## Examples

* [ERC20Contract.sol](https://github.com/hashgraph/hedera-services/blob/master/test-clients/src/main/resource/contract/contracts/ERC20Contract/ERC20Contract.sol)
* [ERC721Contract.sol](https://github.com/hashgraph/hedera-services/blob/master/test-clients/src/main/resource/contract/contracts/ERC721Contract/ERC721Contract.sol)

## References

* [HIP-218](https://hips.hedera.com/hip/hip-218)
* [HIP-376](https://hips.hedera.com/hip/hip-376)
* [EIP-20](https://eips.ethereum.org/EIPS/eip-20)
* [EIP-721](https://eips.ethereum.org/EIPS/eip-721)
