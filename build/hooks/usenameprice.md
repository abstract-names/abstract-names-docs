---
description: Hook for getting tier-based pricing information for Abstract Names.
---

# useNamePrice

Gets pricing information for a name based on its tier. Names are categorized into four tiers (Diamond, Platinum, Gold, Normal) based on character length, with shorter names costing more.

### Import

```tsx
import { useNamePrice } from "@abstract-names/sdk";
```

### Usage

```tsx
import { useNamePrice } from "@abstract-names/sdk";

export default function Example() {
  const { data: pricing, isLoading } = useNamePrice({
    name: "vitalik",
    years: 2
  });

  if (isLoading) return <div>Calculating price...</div>;
  if (!pricing) return null;

  return (
    <div>
      <p>Tier: {pricing.tier === 0 ? "Diamond" :
               pricing.tier === 1 ? "Platinum" :
               pricing.tier === 2 ? "Gold" : "Normal"}</p>
      <p>Annual Price: {pricing.totalPriceFormatted} ETH/year</p>
      <p>Total for {2} years: {pricing.totalPriceFormatted} ETH</p>
    </div>
  );
}
```

### Props

<mark style="color:$success;">**name**</mark> `Name`

* **Type:** `string | undefined`
* **Description:** The Abstract Name to get pricing for (e.g., "vitalik" or "vitalik.abs"). The `.abs` suffix is automatically normalized if provided.

<mark style="color:$success;">**years**</mark> `Years`

* **Type:** `number`
* **Default:** `1`
* **Description:** Number of years to register the name for. Total price is `tierPrice * years`.

<mark style="color:$success;">**enabled**</mark> `Enabled`

* **Type:** `boolean`
* **Default:** `true`
* **Description:** Enable or disable the query. Useful for conditional queries.

### Returns

Returns a `UseNamePriceResult` object.

#### UseNamePriceResult

```typescript
interface UseNamePriceResult {
  data: NamePriceData | undefined;
  isLoading: boolean;
  error: AbstractNamesError | null;
  rawError: Error | null;
  refetch: () => void;
}
```

**Properties**

<details>

<summary>Show properties</summary>

<mark style="color:$success;">**data**</mark> `NamePriceData | undefined`

Pricing data for the name. Returns `undefined` if the query hasn't completed.

```typescript
interface NamePriceData {
  /** Price tier (0=Diamond, 1=Platinum, 2=Gold, 3=Normal) */
  tier: number;
  /** Annual price for this tier (in wei) */
  tierPrice: bigint;
  /** Total price for registration (tierPrice * years, in wei) */
  totalPrice: bigint;
  /** Total price formatted in ETH (e.g., "0.001") */
  totalPriceFormatted: string;
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

Function to manually refetch the pricing data.

</details>

### Pricing Tiers

Abstract Names uses a four-tier pricing system based on name length:

| Tier | Length | Annual Price | Tier Number |
|------|--------|--------------|-------------|
| Diamond | 3 characters | 0.15 ETH/year | 0 |
| Platinum | 4 characters | 0.05 ETH/year | 1 |
| Gold | 5 characters | 0.01 ETH/year | 2 |
| Normal | 6+ characters | 0.001 ETH/year | 3 |

### Examples

```tsx
// 3-character name (Diamond tier)
const { data } = useNamePrice({ name: "vit", years: 1 });
// Result: tier=0, totalPrice=0.15 ETH

// 7-character name (Normal tier) for 3 years
const { data } = useNamePrice({ name: "vitalik", years: 3 });
// Result: tier=3, totalPrice=0.003 ETH (0.001 * 3)

// Use the raw bigint for transactions
const { data: pricing } = useNamePrice({ name: "alice" });
if (pricing) {
  await registerName(name, years, { value: pricing.totalPrice });
}
```

### Common Errors

* `NETWORK_ERROR` - RPC connection issues
* `CONTRACT_ERROR` - Controller contract issues

### See Also

* [**useMintPhase**](usemintphase.md) - Get current mint phase
* [**useNameAvailability**](usenameavailability.md) - Check if name is available
