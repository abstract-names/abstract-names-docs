---
description: Hook for setting multiple text records in a single transaction.
---

# useBatchSetText

Sets multiple text records in a single transaction. More gas-efficient than calling `setText` multiple times. Only the name owner can update text records.

### Import

```tsx
import { useBatchSetText } from "@abstract-names/sdk";
```

### Usage

```tsx
import { useBatchSetText } from "@abstract-names/sdk";

export default function Example() {
  const { batchSetText, isPending, isSuccess } = useBatchSetText({
    onSuccess: (hash) => console.log("Batch update sent:", hash),
    onError: (error) => toast.error(error.userMessage)
  });

  const handleUpdateSocials = async () => {
    await batchSetText(
      tokenId,
      ["com.x", "com.github", "url"],
      ["@vitalik", "vitalik", "https://vitalik.ca"]
    );
  };

  return (
    <button onClick={handleUpdateSocials} disabled={isPending}>
      {isPending ? "Updating..." : "Update Social Links"}
    </button>
  );
}
```

### Props

<mark style="color:$success;">**onSuccess**</mark> `Success Callback`

* **Type:** `(hash: `0x${string}`) => void`
* **Description:** Callback fired when the transaction hash is received (before confirmation).

<mark style="color:$success;">**onError**</mark> `Error Callback`

* **Type:** `(error: AbstractNamesError) => void`
* **Description:** Callback fired when an error occurs during transaction preparation or sending.

### Returns

Returns a `UseBatchSetTextResult` object.

#### UseBatchSetTextResult

```typescript
interface UseBatchSetTextResult {
  batchSetText: (
    tokenId: bigint,
    keys: string[],
    values: string[]
  ) => Promise<`0x${string}`>;
  isPending: boolean;
  isConfirming: boolean;
  isSuccess: boolean;
  isError: boolean;
  transactionHash?: `0x${string}`;
  error: AbstractNamesError | null;
  rawError: Error | null;
  reset: () => void;
}
```

**Properties**

<details>

<summary>Show properties</summary>

<mark style="color:$success;">**batchSetText**</mark> `(tokenId: bigint, keys: string[], values: string[]) => Promise<0x${string}>`

Function to set multiple text records in one transaction. Returns a promise that resolves to the transaction hash.

* `tokenId` - The token ID of the name (bigint)
* `keys` - Array of text record keys (e.g., ["avatar", "com.x"])
* `values` - Array of corresponding values (must match keys length)

***

<mark style="color:$success;">**isPending**</mark> `boolean`

`true` when the transaction is being prepared or sent. Use this for loading states.

***

<mark style="color:$success;">**isConfirming**</mark> `boolean`

`true` when waiting for transaction confirmation on-chain.

***

<mark style="color:$success;">**isSuccess**</mark> `boolean`

`true` when the transaction has been confirmed successfully.

***

<mark style="color:$success;">**isError**</mark> `boolean`

`true` when an error has occurred during transaction preparation, sending, or confirmation.

***

<mark style="color:$success;">**transactionHash**</mark> `0x${string} | undefined`

The transaction hash once the transaction has been sent. `undefined` before sending.

***

<mark style="color:$success;">**error**</mark> `AbstractNamesError | null`

Structured error object with user-friendly message if an error occurred, otherwise `null`.

***

<mark style="color:$success;">**rawError**</mark> `Error | null`

The raw error from wagmi for debugging purposes. Use `error` for user-facing messages.

***

<mark style="color:$success;">**reset**</mark> `() => void`

Function to reset the mutation state (clears errors, success state, etc.).

</details>

### Gas Efficiency

**Individual Updates** (`useSetTextRecord`):
* 3 text records = 3 transactions
* ~150,000 gas total
* Higher transaction fees

**Batch Update** (`useBatchSetText`):
* 3 text records = 1 transaction
* ~70,000 gas total
* Single transaction fee
* **~53% gas savings**

### Validation

* Arrays `keys` and `values` must have the same length
* All keys must be in the allowed text keys list
* Only the name owner can perform batch updates
* Name must not be expired

### Common Errors

* `UNAUTHORIZED` - User does not own the name
* `INVALID_TEXT_KEY` - One or more keys not in allowed list
* `VALIDATION_ERROR` - Keys and values array length mismatch
* `NAME_EXPIRED` - Name has expired

### See Also

* [**useSetTextRecord**](usesettextrecord.md) - Set a single text record
* [**useTextRecord**](usetextrecord.md) - Read text records
* [**useAllowedTextKeys**](useallowedtextkeys.md) - Get list of allowed keys
