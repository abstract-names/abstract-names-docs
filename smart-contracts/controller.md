# Controller

### Purpose

The Controller orchestrates minting, pricing, phases, renewals, and reserved names. It sits between user interfaces and the Registry: validating inputs, enforcing allowlists and limits, calculating costs by tier and duration, then minting through the Registry. Only accounts with the Controller’s admin roles can configure phases and pricing; only the Registry’s controller role can mint.

### Tiers by length

| Tier | Length | Label    |
| ---- | ------ | -------- |
| 0    | 3      | Diamond  |
| 1    | 4      | Platinum |
| 2    | 5      | Gold     |
| 3    | 6+     | Normal   |

Tier selection is derived from the normalized name length:

```solidity
function _getTierFromLength(uint256 length) internal pure returns (uint8) {
	if (length == 3) return 0; // Diamond
	if (length == 4) return 1; // Platinum
	if (length == 5) return 2; // Gold
	return 3; // Normal (6+)
}
```

### Phase gating and access control

```solidity
function getCurrentPhase() public view returns (Phase) {
	uint64 whitelistStart = phaseConfig.whitelistStart;
	if (whitelistStart == 0 || block.timestamp < whitelistStart) return Phase.NONE;
	uint256 elapsed = block.timestamp - whitelistStart;
	if (elapsed < phaseConfig.whitelistDuration) return Phase.WHITELIST;
	if (elapsed < phaseConfig.whitelistDuration + phaseConfig.waitlistDuration) return Phase.WAITLIST;
	return Phase.PUBLIC;
}
```

Whitelist and waitlist phases use Merkle proofs. In the waitlist phase, the leaf includes an optional discount basis points value, enabling the Controller to verify and apply a one‑time discount for eligible users on Gold and Normal tiers. Per‑phase mint limits and phase timings (start and durations) are configurable.

### Registration and pricing

```solidity
function register(
	string calldata name,
	bytes32[] calldata proof,
	uint256 yearsPaid,
	uint16 discountBps
) external payable {
	string memory normalized = registry.validator().validateName(name);
	address reservedOwner = reservedNames[normalized];
	if (reservedOwner != address(0) && reservedOwner != msg.sender) revert NameReserved();

	Phase phase = getCurrentPhase();
	// ... verify phase access + update counts ...

	uint8 tier = _getTierFromLength(bytes(normalized).length);
	uint256 totalCost = tierPrices[tier] * yearsPaid;

	if (phase == Phase.WAITLIST && discountBps > 0) {
		if (tier < 2) revert InvalidDiscount(); // only Gold/Normal
		if (waitlistDiscountUsed[msg.sender]) revert DiscountAlreadyUsed();
		totalCost = totalCost * (10000 - discountBps) / 10000;
		waitlistDiscountUsed[msg.sender] = true;
	}

	if (msg.value != totalCost) revert InsufficientPayment();
	uint64 expiry = uint64(block.timestamp + yearsPaid * YEAR);
	registry.mint(msg.sender, normalized, expiry, tier);
}
```

### Renewals

Renewals use the stored tier to compute a new cost and extend the expiration from the later of now or the current expiry:

```solidity
function renew(uint256 tokenId, uint256 yearsPaid) external payable {
	IAbstractNamesRegistry.NameData memory data = registry.getNameData(tokenId);
	uint256 totalCost = tierPrices[data.tier] * yearsPaid;
	if (msg.value != totalCost) revert InsufficientPayment();
	uint64 currentTime = uint64(block.timestamp);
	uint64 baseTime = data.expiresAt > currentTime ? data.expiresAt : currentTime;
	uint64 newExpiry = baseTime + uint64(yearsPaid * YEAR);
	registry.setExpiry(tokenId, newExpiry);
}
```

### Reserved names

```solidity
function setReservedName(string calldata name, address owner) external onlyRole(ADMIN_ROLE) {
	string memory normalized = registry.validator().validateName(name);
	reservedNames[normalized] = owner;
}
```

### Admin configuration

Administrators can configure prices per tier, per‑phase mint limits, Merkle roots, and phase timings. They can also pause/unpause and withdraw funds held by the Controller to the treasury. The Controller records batch minting for operations and exposes views for tier pricing and phase status.
