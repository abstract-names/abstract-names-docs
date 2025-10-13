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

<mark style="color:$primary;">**name**</mark> <mark style="color:$info;">`Name`</mark>  <mark style="color:$danger;">`required`</mark>

* **Type:** `string | undefined`
* **Required:** Yes
* **Description:** The Abstract Name to resolve. The `.abs` suffix is optional and will be automatically normalized.

<mark style="color:$primary;">**enabled**</mark> <mark style="color:$info;">`Enabled`</mark>

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

#### Properties

<details>

<summary>Show properties</summary>

<mark style="color:$primary;">**data**</mark> `Address | undefined`\


\
\
The resolved Ethereum address for the name. Returns `undefined` if the name is not registered or the query is disabled.

***

<mark style="color:$primary;">**isLoading**</mark> `boolean`\
\


`true` when the query is in a loading state. Useful for showing loading indicators.

</details>

### Examples

#### Basic Usage

```tsx
import { useResolve } from "@abstract-names/sdk";

function AddressDisplay({ name }: { name: string }) {
  const { data: address } = useResolve({ name });

  return <div>{address || "Not found"}</div>;
}
```

#### With Loading State

```tsx
import { useResolve } from "@abstract-names/sdk";

function ResolveWithLoading() {
  const { data, isLoading, error } = useResolve({
    name: "vitalik.abs"
  });

  if (isLoading) return <div>Resolving name...</div>;
  if (error) return <div>Error: {error.message}</div>;
  if (!data) return <div>Name not registered</div>;

  return (
    <div>
      <p>Name: vitalik.abs</p>
      <p>Address: {data}</p>
    </div>
  );
}
```

#### Conditional Query

```tsx
import { useState } from "react";
import { useResolve } from "@abstract-names/sdk";

function NameSearch() {
  const [name, setName] = useState("");

  const { data: address, isLoading } = useResolve({
    name,
    enabled: name.length >= 3  // Only query when name is at least 3 characters
  });

  return (
    <div>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Enter a name..."
      />
      {isLoading && <p>Loading...</p>}
      {address && <p>Address: {address}</p>}
    </div>
  );
}
```

#### With Refetch

```tsx
import { useResolve } from "@abstract-names/sdk";

function ResolvableAddress({ name }: { name: string }) {
  const { data: address, refetch } = useResolve({ name });

  return (
    <div>
      <p>Address: {address || "Not found"}</p>
      <button onClick={() => refetch()}>Refresh</button>
    </div>
  );
}
```

#### Name Normalization

```tsx
import { useResolve } from "@abstract-names/sdk";

function Example() {
  // These both resolve to the same address
  const result1 = useResolve({ name: "vitalik" });
  const result2 = useResolve({ name: "vitalik.abs" });

  // Both queries will return the same data
  return (
    <div>
      <p>{result1.data}</p>
      <p>{result2.data}</p>
    </div>
  );
}
```

### Notes

* The `.abs` suffix is automatically normalized - both "vitalik" and "vitalik.abs" work
* Returns `undefined` if the name is not registered
* Automatically uses the active chain from wagmi
* Can be overridden with `AbstractNamesProvider`
* Query is disabled if the chain is not supported (Abstract Mainnet or Testnet)

