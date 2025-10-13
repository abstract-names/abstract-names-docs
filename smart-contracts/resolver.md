# Resolver

### Purpose

The Resolver answers “what does this name point to?” It maps names to addresses, profile text records, and content hashes. It also supports reverse resolution, allowing apps to map an address back to a chosen primary name. The Registry calls into the Resolver on transfers to clear sensitive records and keep data safe across owners.

### Forward resolution with owner fallback

```solidity
function resolve(string calldata name) external view returns (address) {
	uint256 tokenId = registry.getTokenId(name);
	if (tokenId == 0) return address(0);
	if (registry.isExpired(tokenId)) return address(0);

	address resolved = _addresses[tokenId];
	return resolved != address(0) ? resolved : registry.ownerOf(tokenId);
}
```

If a custom address has been set, the resolver returns it. Otherwise, it falls back to the current owner’s address. Expired names resolve to the zero address.

### Reverse resolution (primary names)

```solidity
function setPrimaryName(uint256 tokenId)
	external
	payable
	onlyTokenOwner(tokenId)
	notExpired(tokenId)
{
	if (msg.value != primaryFee) revert InsufficientPayment();
	_primaryNames[msg.sender] = tokenId;
}

function reverseResolve(address addr) external view returns (string memory) {
	uint256 tokenId = _primaryNames[addr];
	if (tokenId == 0) return "";
	if (registry.isExpired(tokenId)) return "";
	IAbstractNamesRegistry.NameData memory data = registry.getNameData(tokenId);
	return string.concat(data.name, ".", tld);
}
```

Setting a primary name lets interfaces display “name.abs” when they only have an address. A small fee deters spam. If the underlying name expires, the reverse record resolves to an empty string.

### Profiles via text records

```solidity
function setText(uint256 tokenId, string calldata key, string calldata value)
	external
	onlyTokenOwner(tokenId)
	notExpired(tokenId)
	validTextKey(key)
{
	_textRecords[tokenId][key] = value;
}

function getText(uint256 tokenId, string calldata key)
	external
	view
	returns (string memory)
{
	if (registry.isExpired(tokenId)) return "";
	uint256 recordTimestamp = _textRecordTimestamps[tokenId][key];
	uint256 transferTimestamp = _transferredAt[tokenId];
	if (transferTimestamp > 0 && recordTimestamp <= transferTimestamp) {
		return "";
	}
	return _textRecords[tokenId][key];
}
```

Only allowed keys can be set (e.g., avatar, url, com.x, com.discord, com.telegram, com.github, header). Records set before a transfer are considered outdated and won’t be returned unless reset by the new owner.

### Content hashes and transfer safety

```solidity
function setContentHash(uint256 tokenId, bytes calldata hash)
	external
	onlyTokenOwner(tokenId)
	notExpired(tokenId)
{
	_contentHashes[tokenId] = hash;
}

function clearRecordsOnTransfer(uint256 tokenId, address previousOwner) external {
	if (msg.sender != address(registry)) revert Unauthorized();
	delete _addresses[tokenId];
	delete _contentHashes[tokenId];
	if (_primaryNames[previousOwner] == tokenId) {
		delete _primaryNames[previousOwner];
	}
	_transferredAt[tokenId] = block.timestamp;
}
```

When a name transfers, the resolver clears the explicit address and content hash and removes the previous owner’s primary mapping if it pointed to the transferred token. A timestamp marks the transfer so older text records won’t be returned.
