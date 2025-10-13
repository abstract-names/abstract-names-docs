# Validator

### Purpose

The Name Validator ensures names are well‑formed before they’re minted or checked. It enforces length and character rules, lower‑cases ASCII, and allows curated Unicode ranges. The Registry and Controller both rely on it to normalize and validate user input.

### Constants and supported ranges

```solidity
uint256 public constant MIN_LENGTH = 3;
uint256 public constant MAX_LENGTH = 63;
```

The contract initializes curated Unicode ranges covering Latin‑1 and extensions, CJK, Hangul, Hiragana, Katakana, and basic CJK punctuation.

### ASCII validation and normalization

```solidity
function _validateASCII(bytes memory nameBytes) internal pure returns (string memory) {
	// A-Z → a-z; allow 0-9, a-z, '-', '_'
	// Rules: no leading/trailing '-', no '--' at positions 3-4, no trailing '_'
}

function _validateASCIIRules(bytes memory normalized) internal pure {
	if (normalized[normalized.length - 1] == 0x5F) revert InvalidCharacter(0x5F);
	if (normalized[0] == 0x2D) revert InvalidCharacter(0x2D);
	if (normalized[normalized.length - 1] == 0x2D) revert InvalidCharacter(0x2D);
	if (normalized.length >= 4 && normalized[2] == 0x2D && normalized[3] == 0x2D) revert InvalidCharacter(0x2D);
}
```

### Unicode validation

```solidity
function _validateUnicode(bytes memory nameBytes) internal view returns (string memory) {
	uint256 i = 0;
	while (i < nameBytes.length) {
		(uint32 codepoint, uint256 bytesRead) = _readUTF8Codepoint(nameBytes, i);
		if (bytesRead == 0) revert InvalidCharacter(0x00);
		if (!_isAllowedCharacter(codepoint)) revert InvalidCharacter(bytes1(uint8(codepoint & 0xFF)));
		i += bytesRead;
	}
	return string(nameBytes);
}
```

### Public API

```solidity
function validateName(string calldata name) external view returns (string memory normalized) {
	bytes memory nameBytes = bytes(name);
	if (nameBytes.length < MIN_LENGTH || nameBytes.length > MAX_LENGTH) revert InvalidLength(nameBytes.length);
	// Fast path for ASCII, otherwise process UTF‑8 codepoints
}
```

Admins can extend or prune Unicode ranges as needed using `addUnicodeRange` and `removeUnicodeRange`.
