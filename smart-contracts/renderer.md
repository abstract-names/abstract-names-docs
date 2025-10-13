# Renderer

### Purpose

The Name Renderer generates on‑chain SVG and JSON metadata for each name. By keeping metadata on‑chain, the Registry can serve `tokenURI` without external servers, ensuring availability and verifiability. Admins can upgrade the renderer address on the Registry when visuals or attributes evolve.

### How the Registry calls the Renderer

```solidity
function tokenURI(uint256 tokenId) public view override returns (string memory) {
	NameData storage data = _nameData[tokenId];
	bool expired = data.expiresAt < block.timestamp;
	return INamesRendererSVG(renderer).getTokenURI(data.name, tokenId, data.tier, expired);
}
```

When the renderer is updated, the Registry emits a batch metadata update so marketplaces refresh images and attributes. Visuals can reflect tier and expiry, enabling dynamic badges or states without off‑chain infrastructure.
