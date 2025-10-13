---
description: >-
  Resolution is how Abstract Names connects your readable name — like alice.abs
  — to what it represents on-chain: your Abstract Global Wallet, identity, or
  content.
---

# Resolution

### Resolve

Resolution is how a name answers the question “where should this point?” By default, a name points to its current owner’s address. You can set a specific address if you prefer. When someone looks up your name, the resolver returns the address you’ve set. If you haven’t set one, it falls back to the owner’s address.

Reverse resolution does the opposite: it lets apps display a name when they only have an address. By setting your name as your primary, supported apps can show “yourname.abs” instead of the address, which makes it easier for people to recognize you.

Expired names do not resolve.

### Profiles

Profiles add context to your name. You can attach text records like an avatar URL, a website, or a link to your social account. These records help others confirm they’re viewing the right account. You can also attach a content hash, which points your name to content on decentralized storage such as IPFS or Arweave.

When a name transfers to a new owner, data is cleared for safety. A custom address and content hash are reset, and older text records are treated as outdated unless the new owner sets them again. This avoids resolving to information from a previous owner.

### Text record types

| Key            | Example                       | Purpose                   |
| -------------- | ----------------------------- | ------------------------- |
| `avatar`       | `ipfs://Qm...`                | Profile image or PFP      |
| `description`  | `Full-stack dev on Abstract`  | Short bio                 |
| `com.x`        | `https://x.com/alice`         | Social link (X / Twitter) |
| `com.discord`  | `https://discord.gg/alice#12` | Discord server link       |
| `com.telegram` | `https://t.me/alice`          | Telegram link             |
| `com.github`   | `https://github.com/alice`    | Github link               |
| `url`          | `https://alice.dev`           | Personal website          |
| `header`       | `ipfs://Qr...`                | Banner image              |

