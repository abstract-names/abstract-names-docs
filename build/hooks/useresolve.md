---
description: Hook for resolving an Abstract Name to its associated address.
---

# useResolve

Resolves a name like "vitalik.abs" to its Ethereum address. This is the primary hook for forward resolution (name → address).

### Import

```tsx
import { useResolve } from "@abstract-names/sdk";
```

### Usage

```tsx
import { useResolve } from "@abstract-names/sdk";

export default function Example() {
  const { data: address, isLoading, error } = useResolve({
    name: "vitalik.abs"
  });

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  if (!address) return <div>Name not found</div>;

  return <div>Address: {address}</div>;
}
```

### Props

<mark style="color:$success;">**name**</mark> `Name` <mark style="color:$danger;">`required`</mark>

* **Type:** `Address | undefined`
* **Description:** The Ethereum address to reverse resolve to a name.

<mark style="color:$success;">**enabled**</mark> `Enabled`

* **Type:** `boolean`
* **Default:** `true`
* **Description:** Enable or disable the query. Useful for conditional queries.

### Returns

Returns a `UseResolveResult` object.

#### UseResolveResult

```typescript
interface UseResolveResult {
  data: Address | undefined;
  isLoading: boolean;
  error: Error | null;
  refetch: () => void;
}
```

**Properties**

<details>

<summary>Show properties</summary>

<mark style="color:$success;">**data**</mark> `Address | undefined`

The resolved Ethereum address for the name. Returns `undefined` if the name is not registered or the query is disabled.

***

<mark style="color:$success;">**isLoading**</mark> `boolean`

`true` when the query is in a loading state. Useful for showing loading indicators.

***

<mark style="color:$success;">**error**</mark> `Error | null`

The error object if the query failed, otherwise `null`.

***

<mark style="color:$success;">**refetch**</mark> `() => void`

Function to manually refetch the data.

</details>
