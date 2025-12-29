---
description: Hook for getting the current mint phase of Abstract Names.
---

# useMintPhase

Gets the current mint phase from the controller contract. Abstract Names uses a three-phase launch system: Whitelist → Waitlist → Public.

### Import

```tsx
import { useMintPhase, Phase } from "@abstract-names/sdk";
```

### Usage

```tsx
import { useMintPhase, Phase } from "@abstract-names/sdk";

export default function Example() {
  const { data: phaseData, isLoading } = useMintPhase();

  if (isLoading) return <div>Loading phase info...</div>;
  if (!phaseData) return null;

  return (
    <div>
      <h2>Current Phase: {phaseData.phaseName}</h2>

      {phaseData.phase === Phase.NONE && (
        <p>Minting has not started yet</p>
      )}

      {phaseData.phase === Phase.WHITELIST && (
        <p>Only whitelisted addresses can mint</p>
      )}

      {phaseData.phase === Phase.WAITLIST && (
        <p>Waitlisted addresses can mint</p>
      )}

      {phaseData.phase === Phase.PUBLIC && (
        <p>🎉 Public minting is open!</p>
      )}
    </div>
  );
}
```

### Props

<mark style="color:$success;">**enabled**</mark> `Enabled`

* **Type:** `boolean`
* **Default:** `true`
* **Description:** Enable or disable the query. Useful for conditional queries.

### Returns

Returns a `UseMintPhaseResult` object.

#### UseMintPhaseResult

```typescript
interface UseMintPhaseResult {
  data: MintPhaseData | undefined;
  isLoading: boolean;
  error: AbstractNamesError | null;
  rawError: Error | null;
  refetch: () => void;
}
```

**Properties**

<details>

<summary>Show properties</summary>

<mark style="color:$success;">**data**</mark> `MintPhaseData | undefined`

Current mint phase data. Returns `undefined` if the query hasn't completed.

```typescript
interface MintPhaseData {
  /** Current phase value (0-3) */
  phase: Phase;
  /** Human-readable phase name */
  phaseName: string;
  /** Whether minting is currently closed */
  isClosed: boolean;
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

Function to manually refetch the phase data.

</details>

### Phase Enum

```typescript
enum Phase {
  NONE = 0,       // Minting not started
  WHITELIST = 1,  // Only whitelisted addresses
  WAITLIST = 2,   // Waitlisted addresses
  PUBLIC = 3      // Anyone can mint
}
```

### Phase Progression

| Phase | Value | Name | Description |
|-------|-------|------|-------------|
| Not Started | 0 | `NONE` | Minting has not begun |
| Whitelist | 1 | `WHITELIST` | Only whitelisted addresses can mint |
| Waitlist | 2 | `WAITLIST` | Waitlisted addresses can mint |
| Public | 3 | `PUBLIC` | Open to everyone |

### Conditional Rendering

```tsx
import { useMintPhase, Phase } from "@abstract-names/sdk";

function MintButton() {
  const { data: phaseData } = useMintPhase();

  if (!phaseData) return null;

  // Disable minting if phase is NONE
  if (phaseData.isClosed) {
    return <button disabled>Minting Not Started</button>;
  }

  // Show different messages per phase
  const buttonText = {
    [Phase.WHITELIST]: "Mint (Whitelist Only)",
    [Phase.WAITLIST]: "Mint (Waitlist)",
    [Phase.PUBLIC]: "Mint Now"
  }[phaseData.phase];

  return <button>{buttonText}</button>;
}
```

### Polling for Phase Changes

```tsx
import { useMintPhase } from "@abstract-names/sdk";
import { useEffect } from "react";

function PhaseMonitor() {
  const { data: phaseData, refetch } = useMintPhase();

  // Poll every 30 seconds for phase updates
  useEffect(() => {
    const interval = setInterval(() => {
      refetch();
    }, 30000);

    return () => clearInterval(interval);
  }, [refetch]);

  return <div>Current Phase: {phaseData?.phaseName}</div>;
}
```

### Common Errors

* `NETWORK_ERROR` - RPC connection issues
* `CONTRACT_ERROR` - Controller contract issues

### See Also

* [**useNamePrice**](usenameprice.md) - Get pricing information
* [**useNameAvailability**](usenameavailability.md) - Check name availability
