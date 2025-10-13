# Registry

### Purpose

The Registry is the core ERC‑721 ledger for names. It stores each name as an NFT, tracks its length tier and expiration, and exposes metadata via an on‑chain SVG renderer. Only addresses with the `CONTROLLER_ROLE` (i.e., the Controller) can mint. The Registry also coordinates with the Resolver on transfers to keep records safe.

The Registry implements ERC‑4906 events so apps and marketplaces can refresh metadata when important fields change. Names are unique by their normalized string; the contract hashes the normalized name to prevent duplicates.

### NameData structure

| Field        | Type   | Description                                           |
| ------------ | ------ | ----------------------------------------------------- |
| name         | string | The normalized name string.                           |
| tier         | uint8  | Length tier (0: 3‑char, 1: 4‑char, 2: 5‑char, 3: 6+). |
| registeredAt | uint64 | Block timestamp when the name was minted.             |
| expiresAt    | uint64 | Expiration timestamp for the name.                    |

### Minting and uniqueness

```solidity
function mint(
	address to,
	string calldata name,
	uint64 expiry,
	uint8 tier
) external onlyController returns (uint256 tokenId) {
	if (to == address(0)) revert ZeroAddress();

	string memory normalized = validator.validateName(name);
	bytes32 hash = _hashName(normalized);
	if (_nameHash[hash] != 0) revert NameTaken();

	tokenId = _nextTokenId;
	_nextTokenId = tokenId + 1;

	_nameData[tokenId] = NameData({
		name: normalized,
		tier: tier,
		registeredAt: uint64(block.timestamp),
		expiresAt: expiry,
		_pad: 0
	});

	_nameHash[hash] = tokenId;
	_mint(to, tokenId);
}
```

### Availability and lookup

```solidity
function isAvailable(string calldata name) external view returns (bool) {
	try validator.validateName(name) returns (string memory normalized) {
		bytes32 hash = _hashName(normalized);
		return _nameHash[hash] == 0;
	} catch {
		return false;
	}
}

function getTokenId(string calldata name) external view returns (uint256) {
	try validator.validateName(name) returns (string memory normalized) {
		bytes32 hash = _hashName(normalized);
		return _nameHash[hash];
	} catch {
		return 0;
	}
}
```

### Expiry and updates

```solidity
function isExpired(uint256 tokenId) external view returns (bool) {
	return _nameData[tokenId].expiresAt < block.timestamp;
}

function setExpiry(uint256 tokenId, uint64 newExpiry) external onlyController {
	NameData storage nameData = _nameData[tokenId];
	uint64 oldExpiry = nameData.expiresAt;
	nameData.expiresAt = newExpiry;
	emit ExpiryUpdated(tokenId, oldExpiry, newExpiry);
	emit IERC4906.MetadataUpdate(tokenId);
}
```

### Metadata rendering

```solidity
function tokenURI(uint256 tokenId) public view override returns (string memory) {
	NameData storage data = _nameData[tokenId];
	bool expired = data.expiresAt < block.timestamp;
	return INamesRendererSVG(renderer).getTokenURI(data.name, tokenId, data.tier, expired);
}
```

### Transfers and resolver safety

When a token transfers, the Registry calls the Resolver to clear sensitive records for the previous owner. This resets explicit address resolution and content hashes, and it also clears the previous owner’s primary mapping if it pointed to this token. The Registry can also emit a batch metadata update if the renderer address changes, so marketplaces refresh visuals and attributes.
