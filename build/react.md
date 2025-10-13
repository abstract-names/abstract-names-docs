# React

## React SDK setup

This page shows how to install the Abstract Names React SDK and get a minimal setup working in a Next.js/React app. It uses wagmi v2 and viem under the hood.

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
{% endtabs %}

### Provider setup

Wrap your app with WagmiConfig and QueryClientProvider. Use the Abstract chain you target and pass the SDK config to your hooks later.

```tsx
// app/providers.tsx or src/main.tsx
import { PropsWithChildren } from 'react';
import { WagmiConfig, http, createConfig } from 'wagmi';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { defineChain } from 'viem';

// SDK: optional helpers and pre-made configs
import { abstractTestnetConfig } from '@abstract-names/sdk';

// Define Abstract Testnet chain (11124). If you already have a chain list, reuse it.
export const abstractTestnet = defineChain({
	id: 11124,
	name: 'Abstract Testnet',
	nativeCurrency: { name: 'ETH', symbol: 'ETH', decimals: 18 },
	rpcUrls: {
		default: { http: ['https://api.testnet.abs.xyz'] },
		public: { http: ['https://api.testnet.abs.xyz'] },
	},
});

const wagmiConfig = createConfig({
	chains: [abstractTestnet],
	transports: {
		[abstractTestnet.id]: http(), // uses rpcUrls.default by default
	},
});

const queryClient = new QueryClient();

export function Providers({ children }: PropsWithChildren) {
	return (
		<QueryClientProvider client={queryClient}>
			<WagmiConfig config={wagmiConfig}>{children}</WagmiConfig>
		</QueryClientProvider>
	);
}
```

In Next.js App Router, include `Providers` at the root layout.

```tsx
// app/layout.tsx
import { Providers } from './providers';

export default function RootLayout({ children }: { children: React.ReactNode }) {
	return (
		<html lang="en">
			<body>
				<Providers>{children}</Providers>
			</body>
		</html>
	);
}
```

### Minimal example

Resolve an Abstract Name to an address using the SDK hook.

```tsx
import { useState } from 'react';
import { useResolve, abstractTestnetConfig } from '@abstract-names/sdk';

export default function ResolveExample() {
	const [name, setName] = useState('vitalik.abs');
	const { data: address, isLoading, error } = useResolve({
		name,
		config: abstractTestnetConfig,
	});

	return (
		<div>
			<input
				value={name}
				onChange={(e) => setName(e.target.value)}
				placeholder="alice.abs"
			/>
			{isLoading && <p>Resolving…</p>}
			{error && <p>Error: {error.message}</p>}
			{address && <p>Address: {address}</p>}
		</div>
	);
}
```

**Notes**

* Testnet config is provided as `abstractTestnetConfig`. When mainnet is live, switch to `abstractMainnetConfig`.
* Names can be passed with or without the `.abs` suffix; hooks normalize internally.
* Ensure your dApp connects to the same chain ID as the config you pass (11124 for testnet).

### What’s next

Explore the hooks catalog for resolution and utilities:

* useResolve, useReverseResolve, useProfile
* useNameAvailability, useNameExpiry, useTextRecord, useAllowedTextKeys

See: React Hooks reference (next page)
