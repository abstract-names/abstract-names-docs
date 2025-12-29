---
description: Hook for setting the resolved address for an Abstract Name.
---

# useSetAddress

Sets the resolved address for a name. By default, names resolve to the owner's address. This function allows you to point the name to a different address. Only the name owner can update the resolved address.

### Import

```tsx
import { useSetAddress } from "@abstract-names/sdk";
```

### Usage

```tsx
import { useSetAddress } from "@abstract-names/sdk";

export default function Example() {
  const { setAddress, isPending, isSuccess } = useSetAddress({
    onSuccess: (hash) => console.log("Address updated:", hash),
    onError: (error) => toast.error(error.userMessage)
  });

  const handleSetAddress = async () => {
    // Point name to a different address
    await setAddress(tokenId, "0x1234567890123456789012345678901234567890");
  };

  const handleResetAddress = async () => {
    // Revert back to owner's address
    await setAddress(tokenId, "0x0000000000000000000000000000000000000000");
  };

  return (
    <div>
      <button onClick={handleSetAddress} disabled={isPending}>
        Point to Different Address
      </button>
      <button onClick={handleResetAddress} disabled={isPending}>
        Reset to Owner Address
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

Returns a `UseSetAddressResult` object.

#### UseSetAddressResult

```typescript
interface UseSetAddressResult {
  setAddress: (tokenId: bigint, address: Address) => Promise<`0x${string}`>;
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

<mark style="color:$success;">**setAddress**</mark> `(tokenId: bigint, address: Address) => Promise<0x${string}>`

Function to set the resolved address for a name. Returns a promise that resolves to the transaction hash.

* `tokenId` - The token ID of the name (bigint)
* `address` - The Ethereum address to resolve to (use zero address to reset)

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

### Use Cases

**Point to Different Address:**
```tsx
// Name owner: 0xAAA...
// Resolved address: 0xBBB... (after calling setAddress)
await setAddress(tokenId, "0xBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBBB");
```

**Reset to Owner Address:**
```tsx
// Pass zero address to revert back to owner
await setAddress(tokenId, "0x0000000000000000000000000000000000000000");
```

**Multi-Sig Wallets:**
```tsx
// Personal wallet owns the name, but it resolves to the team's multi-sig
await setAddress(tokenId, multiSigAddress);
```

### Common Use Cases

* **Delegation**: Point your personal name to a team wallet
* **Migration**: Redirect an old wallet to a new one without transferring ownership
* **Multi-Sig**: Personal name resolves to a multi-signature wallet
* **Contract Addresses**: Point name to a smart contract

### Common Errors

* `UNAUTHORIZED` - User does not own the name
* `NAME_EXPIRED` - Name has expired
* `NETWORK_ERROR` - RPC or network issues

### See Also

* [**useResolve**](useresolve.md) - Resolve name to address
* [**useProfile**](useprofile.md) - Get complete profile data
