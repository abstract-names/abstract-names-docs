---
description: Hook for setting individual text records on Abstract Names.
---

# useSetTextRecord

Sets a single text record for a name. Only the name owner can update text records. The key must be in the allowed text keys list.

### Import

```tsx
import { useSetTextRecord } from "@abstract-names/sdk";
```

### Usage

```tsx
import { useSetTextRecord } from "@abstract-names/sdk";
import { parseEther } from "viem";

export default function Example() {
  const { setText, isPending, isSuccess, error } = useSetTextRecord({
    onSuccess: (hash) => console.log("Transaction sent:", hash),
    onError: (error) => toast.error(error.userMessage)
  });

  const handleSetAvatar = async () => {
    try {
      await setText(
        tokenId,
        "avatar",
        "https://example.com/avatar.png"
      );
    } catch (err) {
      console.error("Failed to set avatar:", err);
    }
  };

  return (
    <button onClick={handleSetAvatar} disabled={isPending}>
      {isPending ? "Setting..." : "Set Avatar"}
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

Returns a `UseSetTextRecordResult` object.

#### UseSetTextRecordResult

```typescript
interface UseSetTextRecordResult {
  setText: (tokenId: bigint, key: string, value: string) => Promise<`0x${string}`>;
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

<mark style="color:$success;">**setText**</mark> `(tokenId: bigint, key: string, value: string) => Promise<0x${string}>`

Function to set a text record. Returns a promise that resolves to the transaction hash.

* `tokenId` - The token ID of the name (bigint)
* `key` - Text record key (e.g., "avatar", "com.x")
* `value` - Text record value

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

### Supported Text Record Keys

* `avatar` - Profile avatar image URL
* `description` - Profile description/bio
* `com.x` - X (formerly Twitter) handle
* `com.discord` - Discord username
* `com.telegram` - Telegram handle
* `com.github` - GitHub username
* `url` - Personal website URL
* `header` - Profile header/banner image URL

### Common Errors

* `UNAUTHORIZED` - User does not own the name
* `INVALID_TEXT_KEY` - Text record key is not in allowed list
* `NAME_EXPIRED` - Name has expired
* `NETWORK_ERROR` - RPC or network issues

### See Also

* [**useBatchSetText**](usebatchsettext.md) - Set multiple text records in one transaction (more gas efficient)
* [**useTextRecord**](usetextrecord.md) - Read text records
* [**useAllowedTextKeys**](useallowedtextkeys.md) - Get list of allowed keys
