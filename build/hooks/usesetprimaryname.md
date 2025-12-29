---
description: Hook for setting or unsetting a primary name for reverse resolution.
---

# useSetPrimaryName

Sets or unsets a primary name for an address. Primary names enable reverse resolution (address → name.abs). Setting a primary name requires payment of a small fee.

### Import

```tsx
import { useSetPrimaryName } from "@abstract-names/sdk";
```

### Usage

```tsx
import { useSetPrimaryName } from "@abstract-names/sdk";
import { parseEther } from "viem";

export default function Example() {
  const {
    setPrimaryName,
    unsetPrimaryName,
    isPending,
    isSuccess
  } = useSetPrimaryName({
    onSuccess: (hash) => console.log("Transaction sent:", hash),
    onError: (error) => toast.error(error.userMessage)
  });

  const handleSetPrimary = async () => {
    // Set primary name with 0.0001 ETH fee
    await setPrimaryName(tokenId, parseEther("0.0001"));
  };

  const handleUnset = async () => {
    // Remove primary name (no fee)
    await unsetPrimaryName();
  };

  return (
    <div>
      <button onClick={handleSetPrimary} disabled={isPending}>
        Set as Primary Name
      </button>
      <button onClick={handleUnset} disabled={isPending}>
        Remove Primary Name
      </button>
    </div>
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

Returns a `UseSetPrimaryNameResult` object.

#### UseSetPrimaryNameResult

```typescript
interface UseSetPrimaryNameResult {
  setPrimaryName: (tokenId: bigint, fee: bigint) => Promise<`0x${string}`>;
  unsetPrimaryName: () => Promise<`0x${string}`>;
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

<mark style="color:$success;">**setPrimaryName**</mark> `(tokenId: bigint, fee: bigint) => Promise<0x${string}>`

Function to set a name as your primary name. Returns a promise that resolves to the transaction hash.

* `tokenId` - The token ID of the name to set as primary (bigint)
* `fee` - Fee amount in wei (typically 0.0001 ETH, use `parseEther("0.0001")`)

***

<mark style="color:$success;">**unsetPrimaryName**</mark> `() => Promise<0x${string}>`

Function to remove your current primary name. Returns a promise that resolves to the transaction hash. No fee required.

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

### Primary Names Explained

**What is a Primary Name?**

A primary name is the "main" name associated with your address. It enables reverse resolution, allowing dapps to display your name instead of your address.

**How it Works:**

* Without primary name: `0x1234...5678` is displayed
* With primary name: `vitalik.abs` is displayed

**Requirements:**

* You must own the name
* Setting requires a small fee (typically 0.0001 ETH)
* Unsetting is free
* Only one primary name per address

### Fee Structure

**Typical Fee:** 0.0001 ETH (~$0.30 at $3000 ETH)

The fee helps prevent spam and ensures primary names are only set by serious users. The exact fee is determined by the contract and may vary.

### Common Errors

* `UNAUTHORIZED` - User does not own the name
* `INSUFFICIENT_PAYMENT` - Fee amount is too low
* `NAME_EXPIRED` - Name has expired
* `NETWORK_ERROR` - RPC or network issues

### See Also

* [**useReverseResolve**](usereverseresolve.md) - Get primary name for an address
* [**useProfile**](useprofile.md) - Get complete profile including primary name status
