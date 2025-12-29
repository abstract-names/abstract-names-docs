---
description: Hook for validating Abstract Names with debouncing for real-time input.
---

# useValidateNameDebounced

Validates a name with debouncing to reduce RPC calls during typing. Perfect for search boxes and registration forms with live validation. Reduces RPC calls by approximately 70% compared to `useValidateName`.

### Import

```tsx
import { useValidateNameDebounced } from "@abstract-names/sdk";
```

### Usage

```tsx
import { useValidateNameDebounced } from "@abstract-names/sdk";
import { useState } from "react";

export default function Example() {
  const [name, setName] = useState("");
  const { data: validation, isLoading } = useValidateNameDebounced({
    name,
    debounceMs: 300
  });

  return (
    <div>
      <input
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Search for a name"
      />
      {isLoading && <span>Checking...</span>}
      {validation?.isValid && <span>✓ Available</span>}
      {validation?.error && <span>{validation.error.userMessage}</span>}
    </div>
  );
}
```

### Props

<mark style="color:$success;">**name**</mark> `Name`

* **Type:** `string | undefined`
* **Description:** The name to validate (e.g., "vitalik" or "vitalik.abs"). The `.abs` suffix is automatically normalized if provided.

<mark style="color:$success;">**debounceMs**</mark> `Debounce Delay`

* **Type:** `number`
* **Default:** `300`
* **Description:** Debounce delay in milliseconds. Validation will occur after the user stops typing for this duration.

<mark style="color:$success;">**enabled**</mark> `Enabled`

* **Type:** `boolean`
* **Default:** `true`
* **Description:** Enable or disable the query. Useful for conditional queries.

### Returns

Returns a `UseValidateNameResult` object (same as `useValidateName`).

#### UseValidateNameResult

```typescript
interface UseValidateNameResult {
  data: ValidateNameResult | undefined;
  isLoading: boolean;
  error: AbstractNamesError | null;
  rawError: Error | null;
  refetch: () => void;
}
```

**Properties**

<details>

<summary>Show properties</summary>

<mark style="color:$success;">**data**</mark> `ValidateNameResult | undefined`

Validation result object. Returns `undefined` if the query hasn't completed.

```typescript
interface ValidateNameResult {
  /** Whether the name is valid */
  isValid: boolean;
  /** Normalized name (only if valid) */
  normalized?: string;
  /** Structured error (only if invalid) */
  error?: AbstractNamesError;
}
```

***

<mark style="color:$success;">**isLoading**</mark> `boolean`

`true` when the query is in a loading state or during the debounce period. Useful for showing loading indicators.

***

<mark style="color:$success;">**error**</mark> `AbstractNamesError | null`

Structured error object with user-friendly message if the query failed, otherwise `null`.

***

<mark style="color:$success;">**rawError**</mark> `Error | null`

The raw error from wagmi for debugging purposes. Use `error` for user-facing messages.

***

<mark style="color:$success;">**refetch**</mark> `() => void`

Function to manually refetch the validation result.

</details>

### Performance

**Without debouncing** (`useValidateName`):
* ~10 RPC calls per second while typing
* Higher RPC costs
* Immediate validation

**With debouncing** (`useValidateNameDebounced`):
* ~3 RPC calls per second while typing (70% reduction)
* Lower RPC costs
* Slight delay before validation (configurable)

### Use Cases

* ✅ Search boxes with live validation
* ✅ Registration forms with real-time feedback
* ✅ Name availability checkers
* ❌ One-time validation (use `useValidateName` instead)
