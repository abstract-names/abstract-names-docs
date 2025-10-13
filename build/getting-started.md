---
description: >-
  React hooks for interacting with Abstract Names contracts. Built on wagmi and
  viem for seamless integration with your existing Web3 stack.
---

# Getting Started

### Getting Started

Follow these steps to integrate Abstract Names SDK into your React project.

{% stepper %}
{% step %}
#### Install Dependencies

Install the SDK along with its required peer dependencies:

{% tabs %}
{% tab title="npm" %}
```bash
npm i @abstract-names/sdk wagmi viem react
```
{% endtab %}

{% tab title="pnpm" %}
```bash
pnpm add @abstract-names/sdk wagmi viem react
```
{% endtab %}

{% tab title="yarn" %}
```bash
yarn add @abstract-names/sdk wagmi viem react
```
{% endtab %}

{% tab title="bun" %}
```
bun add @abstract-names/sdk wagmi viem react
```
{% endtab %}
{% endtabs %}


{% endstep %}

{% step %}
#### Configure Wagmi

Set up wagmi in your application with Abstract chains. Here's a basic configuration:

```tsx
import { WagmiProvider, createConfig, http } from "wagmi";
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import { abstractTestnet } from "viem/chains";

// Create wagmi config
const config = createConfig({
  chains: [abstractTestnet],
  transports: {
    [abstractTestnet.id]: http(),
  },
});

// Create query client
const queryClient = new QueryClient();

function App() {
  return (
    <WagmiProvider config={config}>
      <QueryClientProvider client={queryClient}>
        <YourApp />
      </QueryClientProvider>
    </WagmiProvider>
  );
}
```

**Note**: If you're using a wallet provider like RainbowKit, Privy, or Dynamic, follow their setup guides instead. The Abstract Names SDK works with any wagmi-based setup.
{% endstep %}

{% step %}
#### Use the Hooks

Start using Abstract Names hooks in your components:

```tsx
import { useResolve, useReverseResolve, useProfile } from '@abstract-names/sdk';

function MyComponent() {
  // Resolve name to address
  const { data: address } = useResolve({
    name: 'vitalik.abs'
  });

  // Reverse resolve address to name
  const { data: name } = useReverseResolve({
    address: '0x1234...'
  });

  // Get complete profile
  const { data: profile } = useProfile({
    nameOrAddress: 'vitalik.abs'
  });

  return (
    <div>
      <p>Address: {address}</p>
      <p>Name: {name}</p>
      <p>Avatar: {profile?.avatar}</p>
    </div>
  );
}
```
{% endstep %}

{% step %}
#### (Optional): Override Chain with Provider

By default, the SDK uses the active chain from wagmi. To query a different chain, wrap your app with `AbstractNamesProvider`:

```tsx
import { AbstractNamesProvider } from "@abstract-names/sdk";
import { abstractTestnet } from "viem/chains";

function App() {
  return (
    <AbstractNamesProvider chainId={abstractTestnet.id}>
      <YourApp />
      {/* All hooks inside will use abstractTestnet */}
    </AbstractNamesProvider>
  );
}
```
{% endstep %}
{% endstepper %}

### Provider

#### AbstractNamesProvider

Optional provider for overriding the chain used by all hooks within its context.

**Props**

```typescript
interface AbstractNamesProviderProps {
  chainId?: number;
  children: ReactNode;
}
```

**Usage**

```tsx
import { AbstractNamesProvider } from "@abstract-names/sdk";
import { abstractTestnet } from "viem/chains";

function App() {
  return (
    <AbstractNamesProvider chainId={abstractTestnet.id}>
      <YourApp />
    </AbstractNamesProvider>
  );
}
```

### Common Patterns

#### Name Normalization

All hooks automatically normalize names by removing the `.abs` suffix if present:

```tsx
// These are equivalent
useResolve({ name: 'vitalik' })
useResolve({ name: 'vitalik.abs' })
```

#### Loading States

All hooks return standard wagmi query states:

```tsx
const { data, isLoading, error, refetch } = useResolve({
  name: 'vitalik.abs'
});

if (isLoading) return <div>Loading...</div>;
if (error) return <div>Error: {error.message}</div>;
return <div>Address: {data}</div>;
```

#### Conditional Queries

Use the `enabled` parameter to conditionally enable queries:

```tsx
const [name, setName] = useState('');

const { data } = useResolve({
  name,
  enabled: name.length >= 3  // Only query when name is at least 3 chars
});
```

#### Refetching Data

All hooks provide a `refetch` function for manual updates:

```tsx
const { data, refetch } = useResolve({ name: 'vitalik.abs' });

return (
  <div>
    <p>Address: {data}</p>
    <button onClick={() => refetch()}>Refresh</button>
  </div>
);
```

