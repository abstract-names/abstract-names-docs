---
description: Hook for fetching complete profile data for an Abstract Name or address.
---

# useProfile

Gets comprehensive profile information including the name, resolved address, text records (avatar, description, social links), and content hash. Works with both names and addresses.

### Import

```tsx
import { useProfile } from "@abstract-names/sdk";
```

### Usage

```tsx
import { useProfile } from "@abstract-names/sdk";

export default function Example() {
  const { data: profile, isLoading, error, getTextRecord } = useProfile({
    nameOrAddress: "vitalik.abs"
  });

  if (isLoading) return <div>Loading profile...</div>;
  if (error) return <div>Error: {error.message}</div>;
  if (!profile) return <div>Profile not found</div>;

  const twitter = getTextRecord('com.x');
  const avatar = getTextRecord('avatar');

  return (
    <div>
      {avatar && <img src={avatar} alt={profile.name} />}
      <h2>{profile.name}</h2>
      <p>{profile.resolvedAddress}</p>
      {twitter && <a href={`https://x.com/${twitter}`}>@{twitter}</a>}
    </div>
  );
}
```

### Props

<mark style="color:$success;">**nameOrAddress**</mark> `Name or Address` <mark style="color:$danger;">`required`</mark>

* **Type:** `string | undefined`
* **Description:** Either an Abstract Name (e.g., "vitalik.abs") or an Ethereum address. When an address is provided, fetches the profile for that address's primary name.

<mark style="color:$success;">**enabled**</mark> `Enabled`

* **Type:** `boolean`
* **Default:** `true`
* **Description:** Enable or disable the query. Useful for conditional queries.

### Returns

Returns a `UseProfileResult` object.

#### UseProfileResult

```typescript
interface UseProfileResult {
  data: NameProfile | undefined;
  isLoading: boolean;
  error: Error | null;
  refetch: () => void;
  getTextRecord: (key: string) => string | undefined;
}
```

**Properties**

<details>

<summary>Show properties</summary>

<mark style="color:$success;">**data**</mark> `NameProfile | undefined`\
\
Complete profile data for the name. Returns `undefined` if the name is not registered or has no profile data.

```typescript
interface NameProfile {
  /** Full name with TLD (e.g., "vitalik.abs") */
  name: string;
  /** Address the name resolves to */
  resolvedAddress: Address;
  /** Text record keys (e.g., ["avatar", "com.twitter"]) */
  keys: string[];
  /** Text record values (corresponding to keys) */
  values: string[];
  /** Content hash for decentralized content */
  contenthash: `0x${string}`;
}
```

***

<mark style="color:$success;">**isLoading**</mark> `boolean`

`true` when the query is in a loading state. Useful for showing loading indicators.

***

<mark style="color:$success;">**error**</mark> `Error | null`

The error object if the query failed, otherwise `null`.

***

<mark style="color:$success;">**refetch**</mark> `() => void`

Function to manually refetch the profile data.

***

<mark style="color:$success;">**getTextRecord**</mark> `(key: string) => string | undefined`

Helper function to retrieve a specific text record value by key. Returns `undefined` if the key doesn't exist.

**Supported Text Record Keys:**

* `avatar` - Profile avatar image URL
* `description` - Profile description/bio
* `com.x` - X (formerly Twitter) handle
* `com.discord` - Discord username
* `com.telegram` - Telegram handle
* `com.github` - GitHub username
* `url` - Personal website URL
* `header` - Profile header/banner image URL

</details>
