---
description: Hook for fetching a specific text record for an Abstract Name.
---

# useTextRecord

Returns a single text record value (like avatar, description, or social links) for a name. More focused and lightweight than `useProfile` when you only need one field.

### Import

```tsx
import { useTextRecord } from "@abstract-names/sdk";
```

### Usage

```tsx
import { useTextRecord } from "@abstract-names/sdk";

export default function Example() {
  const { data: avatar, isLoading, error } = useTextRecord({
    name: "vitalik.abs",
    key: "avatar"
  });

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.userMessage}</div>;
  if (!avatar) return <div>No avatar set</div>;

  return <img src={avatar} alt="Avatar" />;
}
```

Use `useTextRecord` when:

* You need **only one** text record field
* You want to optimize performance by fetching less data
* You're building a component that displays a single piece of information

Use `useProfile` instead when:

* You need **multiple** text records
* You want the complete profile data including name and address
* You're building a full profile view

### Props

<mark style="color:$success;">**name**</mark> `Name` <mark style="color:$danger;">`required`</mark>

* **Type:** `string | undefined`
* **Description:** The Abstract Name to fetch the text record for. The `.abs` suffix is optional and will be automatically normalized.

<mark style="color:$success;">**key**</mark> `Text Key` <mark style="color:$danger;">`required`</mark>

* **Type:** `TextRecordKey | string | undefined`
* **Description:** The text record key to fetch. Can be one of the standard keys or a custom key.

**Standard Text Record Keys:**

* `avatar` - Profile avatar image URL
* `description` - Profile description/bio
* `com.x` - X (formerly Twitter) handle
* `com.discord` - Discord username
* `com.telegram` - Telegram handle
* `com.github` - GitHub username
* `url` - Personal website URL
* `header` - Profile header/banner image URL

<mark style="color:$success;">**enabled**</mark> `Enabled`

* **Type:** `boolean`
* **Default:** `true`
* **Description:** Enable or disable the query. Useful for conditional queries.

### Returns

Returns a `UseTextRecordResult` object.

#### UseTextRecordResult

```typescript
interface UseTextRecordResult {
  data: string | undefined;
  isLoading: boolean;
  error: AbstractNamesError | null;
  rawError: Error | null;
  refetch: () => void;
}
```

#### **Properties**

<details>

<summary>Show properties</summary>

<mark style="color:$success;">**data**</mark> `string | undefined`\
\
The text record value. Returns `undefined` if the key is not set, the name doesn't exist, or the query is disabled.

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

Function to manually refetch the text record.

</details>

