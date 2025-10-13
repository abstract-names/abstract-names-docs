---
description: >-
  This page shows how to integrate Abstract Names in Solidity contracts with
  small, copy‑pasteable snippets.
---

# Solidity

### Interfaces at a glance

You’ll resolve names, verify ownership, read profiles, handle renewals, and validate user input. The examples call directly into the Registry, Resolver, Controller, and Validator as implemented in this repository.

Key calls you’ll use:

* Registry: `getTokenId(name)`, `isExpired(tokenId)`, `ownerOf(tokenId)`, `getNameData(tokenId)`
* Resolver: `getAddress(tokenId)`, `reverseResolve(address)`, `getText(tokenId, key)`, `getContentHash(tokenId)`, `getNameData(tokenId)`
* Controller: `getTierPrice(tier)`, `renew(tokenId, yearsPaid)`
* Validator: `validateName(name)`

Assume you have [contract addresses](../learn/deployments.md) wired and interfaces imported.

### Resolve a name to an address

The resolver will return a custom address if set; otherwise it falls back to the current owner. Always guard `tokenId != 0` before calling resolver methods.

```solidity
function addressFromName(
	IAbstractNamesRegistry registry,
	IAbstractNamesResolver resolver,
	string memory name
) public view returns (address) {
	uint256 tokenId = registry.getTokenId(name);
	if (tokenId == 0) return address(0); // name not minted
	// resolver.getAddress handles expiry and owner fallback
	return resolver.getAddress(tokenId);
}
```

### Verify ownership and expiry

To check whether a user controls a name right now, verify both existence and non‑expiry.

```solidity
function isCurrentOwner(
	IAbstractNamesRegistry registry,
	string memory name,
	address user
) public view returns (bool) {
	uint256 tokenId = registry.getTokenId(name);
	if (tokenId == 0) return false; // not minted
	if (registry.isExpired(tokenId)) return false; // expired
	return registry.ownerOf(tokenId) == user;
}
```

### Read a profile (address + records + content)

Fetch a compact profile from the resolver. It includes the fully qualified name (e.g., `name.abs`), the resolved address (with owner fallback), selected text records, and a content hash if set.

```solidity
function profileFor(
	IAbstractNamesRegistry registry,
	IAbstractNamesResolver resolver,
	string memory name
) public view returns (
	string memory fqdn,
	address resolved,
	string[] memory keys,
	string[] memory values,
	bytes memory contenthash
) {
	uint256 tokenId = registry.getTokenId(name);
	if (tokenId == 0) return ("", address(0), new string[](0), new string[](0), "");
	IAbstractNamesResolver.NameProfile memory p = resolver.getNameData(tokenId);
	return (p.name, p.resolvedAddress, p.keys, p.values, p.contenthash);
}
```

### Reverse resolution (address → name)

If a user has set a primary name, the resolver returns `name.abs`; otherwise it returns an empty string or empty if the name is expired.

```solidity
function primaryName(
	IAbstractNamesResolver resolver,
	address user
) public view returns (string memory) {
	return resolver.reverseResolve(user);
}
```

### Renew a name and compute cost

Renewals are paid per tier per year. You can fetch the tier from the Registry and read the per‑year price from the Controller, then pass the exact value as `msg.value` to `renew`.

```solidity
function renewalQuote(
	IAbstractNamesRegistry registry,
	IAbstractNamesController controller,
	uint256 tokenId,
	uint256 yearsPaid
) public view returns (uint256) {
	IAbstractNamesRegistry.NameData memory data = registry.getNameData(tokenId);
	uint256 perYear = controller.getTierPrice(data.tier);
	return perYear * yearsPaid;
}

function renewName(
	IAbstractNamesRegistry registry,
	IAbstractNamesController controller,
	uint256 tokenId,
	uint256 yearsPaid
) public payable {
	// Example pattern: require callers to fund exactly
	uint256 price = renewalQuote(registry, controller, tokenId, yearsPaid);
	require(msg.value == price, "Incorrect payment");
	controller.renew{value: msg.value}(tokenId, yearsPaid);
}
```

### Normalize or validate names

If you accept a free‑form name on‑chain, normalize and validate it with the Validator. For many flows you can avoid this by using `registry.getTokenId(name)`, which validates internally.

```solidity
function normalize(
	INameValidator validator,
	string memory input
) public view returns (string memory) {
	// Reverts if invalid; returns lower‑cased ASCII or original Unicode
	return validator.validateName(input);
}
```

### Safety notes

* Transfers clear resolver data (explicit address and content hash) and invalidate older text records, so don’t assume profile fields persist across owners.
* Always check `tokenId != 0` and non‑expiry before trusting data.
* Prefer reading resolved addresses via `resolver.getAddress(tokenId)` to benefit from built‑in owner fallback and expiry checks.
