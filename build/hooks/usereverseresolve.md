---
description: >-
  Hook for resolving an Abstract Global Wallet address to it's associated
  Abstract Name.
---

# useReverseResolve

Resolves an address like "0x1234..." to its primary Abstract Name (e.g., "vitalik.abs"). This is the primary hook for reverse resolution (address → name).

#### Import

```tsx
import { useReverseResolve } from "@abstract-names/sdk";
```

#### Usage

```tsx
import { useReverseResolve } from "@abstract-names/sdk";

export default function Example() {
  const { data: name, isLoading, error } = useReverseResolve({
    address: "0x1234567890123456789012345678901234567890"
  });

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  if (!name) return <div>No primary name set</div>;

  return <div>Name: {name}</div>;
}
```

### Props

<mark style="color:$success;">**address**</mark> `Address` <mark style="color:$danger;">`required`</mark>

* **Type:** `Address | undefined`
* **Description:** The Ethereum address to reverse resolve to a name.

<mark style="color:$success;">**enabled**</mark> `Enabled`

* **Type:** `boolean`
* **Default:** `true`
* **Description:** Enable or disable the query. Useful for conditional queries.

### Returns

Returns a `UseReverseResolveResult` object.

#### **UseReverseResolveResult**

```typescript
interface UseReverseResolveResult {
  data: string | undefined;
  isLoading: boolean;
  error: Error | null;
  refetch: () => void;
}
```

#### **Properties**

<details>

<summary>Show properties</summary>

<mark style="color:$success;">**data**</mark> `string | undefined`\
\
The primary Abstract Name for the address (e.g., "vitalik.abs"). Returns `undefined` if no primary name is set or the query is disabled.

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
