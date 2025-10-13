---
description: Hook for getting the list of allowed text record keys.
---

# useAllowedTextKeys

Returns an array of all supported text record keys. Useful for building profile editors, form builders, and knowing which fields are supported by the Abstract Names system.

### Import

```tsx
import { useAllowedTextKeys } from "@abstract-names/sdk";
```

### Usage

```tsx
import { useAllowedTextKeys } from "@abstract-names/sdk";

export default function Example() {
  const { data: allowedKeys, isLoading, error } = useAllowedTextKeys();

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  if (!allowedKeys) return <div>No keys available</div>;

  return (
    <ul>
      {allowedKeys.map((key) => (
        <li key={key}>{key}</li>
      ))}
    </ul>
  );
}
```

### Props

<mark style="color:$success;">**enabled**</mark> `Enabled`

* **Type:** `boolean`
* **Default:** `true`
* **Description:** Enable or disable the query. Useful for conditional queries.

### Returns

Returns a `UseAllowedTextKeysResult` object.

#### UseAllowedTextKeysResult

```typescript
interface UseAllowedTextKeysResult {
  data: readonly string[] | undefined;
  isLoading: boolean;
  error: Error | null;
  refetch: () => void;
}
```

#### **Properties**

<details>

<summary>Show properties</summary>

<mark style="color:$success;">**data**</mark> `readonly string[] | undefined`\
\
Array of allowed text record keys. Returns `undefined` if the query hasn't completed or is disabled.

**Common keys include:**

* `avatar` - Profile avatar image URL
* `description` - Profile description/bio
* `com.x` - X (formerly Twitter) handle
* `com.discord` - Discord username
* `com.telegram` - Telegram handle
* `com.github` - GitHub username
* `url` - Personal website URL
* `header` - Profile header/banner image URL

***

<mark style="color:$success;">**isLoading**</mark> `boolean`

`true` when the query is in a loading state. Useful for showing loading indicators.

***

<mark style="color:$success;">**error**</mark> `Error | null`

The error object if the query failed, otherwise `null`.

***

<mark style="color:$success;">**refetch**</mark> `() => void`

Function to manually refetch the allowed keys list.

</details>

