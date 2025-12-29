---
description: Hook for checking if an Abstract Name is available for registration.
---

# useNameAvailability

Returns whether a name is available to be registered. Useful for building name search interfaces and registration flows.

### Import

```tsx
import { useNameAvailability } from "@abstract-names/sdk";
```

### Usage

```tsx
import { useNameAvailability } from "@abstract-names/sdk";

export default function Example() {
  const { data: isAvailable, isLoading, error } = useNameAvailability({
    name: "vitalik"
  });

  if (isLoading) return <div>Checking availability...</div>;
  if (error) return <div>Error: {error.userMessage}</div>;

  return (
    <div>
      {isAvailable === true && <p>✅ Name is available!</p>}
      {isAvailable === false && <p>❌ Name is taken</p>}
    </div>
  );
}
```

#### Props

<mark style="color:$success;">**name**</mark> `Name` <mark style="color:$danger;">`required`</mark>

* **Type:** `string | undefined`
* **Description:** The Abstract Name to check (without the `.abs` suffix). Names must be at least 3 characters long. The `.abs` suffix is automatically normalized if provided.

<mark style="color:$success;">**enabled**</mark> `Enabled`

* **Type:** `boolean`
* **Default:** `true`
* **Description:** Enable or disable the query. Useful for conditional queries. The query is automatically disabled for names shorter than 3 characters.

### Returns

Returns a `UseNameAvailabilityResult` object.

#### UseNameAvailabilityResult

```typescript
interface UseNameAvailabilityResult {
  data: boolean | undefined;
  isLoading: boolean;
  error: AbstractNamesError | null;
  rawError: Error | null;
  refetch: () => void;
}
```

#### **Properties**

<details>

<summary>Show properties</summary>

<mark style="color:$success;">**data**</mark> `boolean | undefined`

* `true` if the name is available for registration
* `false` if the name is already taken
* `undefined` if the query hasn't completed or is disabled

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

Function to manually refetch the availability status.

</details>
