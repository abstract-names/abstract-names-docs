---
description: Hook for validating Abstract Names using contract-based validation.
---

# useValidateName

Validates a name using the on-chain validator contract. Supports Unicode validation for 95% of world languages and returns normalized name output.

### Import

```tsx
import { useValidateName } from "@abstract-names/sdk";
```

### Usage

```tsx
import { useValidateName } from "@abstract-names/sdk";

export default function Example() {
  const [input, setInput] = useState("");
  const { data: validation, isLoading, error } = useValidateName({
    name: input
  });

  return (
    <div>
      <input
        value={input}
        onChange={(e) => setInput(e.target.value)}
        placeholder="Enter a name"
      />
      {isLoading && <p>Validating...</p>}
      {validation?.isValid && (
        <p>✓ Valid name: {validation.normalized}</p>
      )}
      {validation?.error && (
        <p>✗ {validation.error.userMessage}</p>
      )}
    </div>
  );
}
```

### Props

<mark style="color:$success;">**name**</mark> `Name`

* **Type:** `string | undefined`
* **Description:** The name to validate (e.g., "vitalik" or "vitalik.abs"). The `.abs` suffix is automatically normalized if provided.

<mark style="color:$success;">**enabled**</mark> `Enabled`

* **Type:** `boolean`
* **Default:** `true`
* **Description:** Enable or disable the query. Useful for conditional queries.

### Returns

Returns a `UseValidateNameResult` object.

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

`true` when the query is in a loading state. Useful for showing loading indicators.

***

<mark style="color:$success;">**error**</mark> `AbstractNamesError | null`

Structured error object with user-friendly message if the query failed, otherwise `null`.

```typescript
interface AbstractNamesError {
  type: ErrorType;
  message: string;        // Technical error message
  userMessage: string;    // User-friendly message
  details?: unknown;
}
```

***

<mark style="color:$success;">**rawError**</mark> `Error | null`

The raw error from wagmi for debugging purposes. Use `error` for user-facing messages.

***

<mark style="color:$success;">**refetch**</mark> `() => void`

Function to manually refetch the validation result.

</details>

### Validation Rules

The validator contract checks:

* **Length**: Names must be between 3 and 63 characters
* **Characters**: Only lowercase letters, numbers, and hyphens allowed
* **Unicode**: Supports international characters (95% of world languages)
* **Format**: Cannot start or end with hyphens

### Common Error Types

* `VALIDATION_ERROR` - Invalid length or characters
* `NETWORK_ERROR` - RPC connection issues
* `UNKNOWN_ERROR` - Unexpected errors
