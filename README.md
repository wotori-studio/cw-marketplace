# Marketplace Smart Contract

The marketplace smart contracts provides a generic platform used for selling and buying CW721 tokens with CW20 tokens. It maintains a list of all current offerings, including the seller's address, the token ID put up for sale, the list price of the token and the contract address the offerings originated from. This ensures maximum visibility on a per-sale instead of a per-contract basis, allowing users to browse through list of offerings in one central place.

## Messages

### Sell CW721 Token

Puts an NFT token up for sale.

> :warning: The seller needs to be the owner of the token to be able to sell it.

```shell
# Execute send_nft action to put token up for sale for specified list_price on the marketplace
wasmcli tx wasm execute <CW721_BASE_CONTRACT_ADDR> '{
  "send_nft": {
    "contract": "<MARKETPLACE_CONTRACT_ADDR>",
    "token_id": "<TOKEN_ID>",
    "msg": "BASE64_ENCODED_JSON --> { "list_price": { "address": "<INSERT_CW20_CONTRACT_ADDR>", "amount": "<INSERT_AMOUNT_WITHOUT_DENOM>" }} <--"
  }
}' --gas-prices="0.025ucosm" --gas="auto" --gas-adjustment="1.2" -y --from client
```

### Withdraw CW721 Token Offering

Withdraws an NFT token offering from the global offerings list and returns the NFT token back to its owner.

> :warning: Only the token's owner/seller can withdraw the offering. This will only work after having used `sell_nft` on a token.

```shell
# Execute withdraw_nft action to withdraw the token with the specified offering_id from the marketplace
wasmcli tx wasm execute <MARKETPLACE_CONTRACT_ADDR> '{
  "withdraw_nft": {
    "offering_id": "<INSERT_OFFERING_ID>"
  }
}' --gas-prices="0.025ucosm" --gas="auto" --gas-adjustment="1.2" -y --from client
```

### Buy CW721 Token

Buys an NFT token, transferring funds to the seller and the token to the buyer.

> :warning: This will only work after having used `sell_nft` on a token.

```shell
# Execute send action to buy token with the specified offering_id from the marketplace
wasmcli tx wasm execute <CW20_BASE_CONTRACT_ADDR> '{
  "send": {
    "contract": "<MARKETPLACE_CONTRACT_ADDR>",
    "amount": "<INSERT_AMOUNT>",
    "msg": "BASE64_ENCODED_JSON --> { "offering_id": "<INSERT_OFFERING_ID>" } <--"
  }
}' --gas-prices="0.025ucosm" --gas="auto" --gas-adjustment="1.2" -y --from client
```

## Queries

### Query Offerings

Retrieves a list of all currently listed offerings.

```shell
wasmcli query wasm contract-state smart <MARKETPLACE_CONTRACT_ADDR> '{
  "get_offerings": {}
}'
```
