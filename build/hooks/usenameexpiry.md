---
description: Hook for getting expiration information for an Abstract Name.
---

# useNameExpiry

Returns comprehensive expiry data including registration date, expiration date, name tier, and calculated days until expiry. Useful for showing renewal reminders and expiration warnings.

### Import

```tsx
import { useNameExpiry } from "@abstract-names/sdk";
```

### Usage

```tsx
import { useNameExpiry } from "@abstract-names/sdk";

export default function Example() {
  const { data: expiry, isLoading, error } = useNameExpiry({
    name: "vitalik.abs"
  });

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.userMessage}</div>;
  if (!expiry) return <div>Name not found</div>;

  if (expiry.isExpired) {
    return <div>⚠️ This name has expired</div>;
  }

  if (expiry.daysUntilExpiry < 30) {
    return <div>⏰ Expires in {expiry.daysUntilExpiry} days</div>;
  }

  return <div>✅ Active until {new Date(Number(expiry.expiresAt) * 1000).toLocaleDateString()}</div>;
}
```

### Props

<mark style="color:$success;">**name**</mark> `Name` <mark style="color:$danger;">`required`</mark>

* **Type:** `string | undefined`
* **Description:** The Abstract Name to check expiration for. The `.abs` suffix is optional and will be automatically normalized.

<mark style="color:$success;">**enabled**</mark> `Enabled`

* **Type:** `boolean`
* **Default:** `true`
* **Description:** Enable or disable the query. Useful for conditional queries.

### Returns

Returns a `UseNameExpiryResult` object.

#### UseNameExpiryResult

```typescript
interface UseNameExpiryResult {
  data: NameExpiryData | undefined;
  isLoading: boolean;
  error: AbstractNamesError | null;
  rawError: Error | null;
  refetch: () => void;
}
```

#### **Properties**

<details>

<summary>Show properties</summary>

<mark style="color:$success;">**data**</mark> `NameExpiryData | undefined`

Expiry information for the name. Returns `undefined` if the name is not registered or the query is disabled.

```typescript
interface NameExpiryData {
  /** Timestamp when name was registered (Unix timestamp as bigint) */
  registeredAt: bigint;
  /** Timestamp when registration expires (Unix timestamp as bigint) */
  expiresAt: bigint;
  /** Name tier (0=Diamond, 1=Platinum, 2=Gold, 3=Normal) */
  tier: number;
  /** Whether the name is currently expired */
  isExpired: boolean;
  /** Days until expiry (negative if expired) */
  daysUntilExpiry: number;
}
```

***

<mark style="color:$success;">**isLoading**</mark> `boolean`

`true` when the query is in a loading state. Useful for showing loading indicators.

***

<mark style="color:$success;">**error**</mark> `AbstractNamesError | null`

Structured error object with user-friendly message if the query failed, otherwise `null`.

***

<mark style="color:$success;">**rawError**</mark> `Error | null`

The raw error from wagmi for debugging purposes. Use `error` for user-facing messages.

***

<mark style="color:$success;">**refetch**</mark> `() => void`

Function to manually refetch the expiry data.

</details>
