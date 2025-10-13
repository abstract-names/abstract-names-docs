---
icon: hand-wave
cover: .gitbook/assets/gitbookbanner.png
coverY: 0
layout:
  width: default
  cover:
    visible: true
    size: full
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
---

# Introduction

Welcome to Abstract Names. A naming service for your Abstract Global Wallet.

Abstract Names helps people use the blockchain without juggling long hexadecimal strings. Instead of sending to an address, you can send value to a name. A name can also power a profile—linking an avatar, website, or social handle—and even point to content stored on networks like IPFS. Names are NFTs with an expiration date, so you can renew them over time and transfer them just like any ERC‑721 token.

Under the hood, the system is modular. A Registry mints and stores names as NFTs, a Controller manages pricing and sale phases, a Resolver answers “who and what does this name point to?”, and a Validator ensures names are well‑formed and safe.

### Jump right in

<table data-view="cards"><thead><tr><th></th><th></th><th></th><th data-hidden data-card-cover data-type="image">Cover image</th><th data-hidden></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td><h4><i class="fa-lightbulb">:lightbulb:</i></h4></td><td><strong>Learn</strong></td><td>Discover Abstract Names</td><td><a href=".gitbook/assets/docs.png">docs.png</a></td><td></td><td><a href="broken-reference">Broken link</a></td></tr><tr><td><h4><i class="fa-square-code">:square-code:</i></h4></td><td><strong>Build</strong></td><td>Get started in 5-min.</td><td><a href=".gitbook/assets/code.png">code.png</a></td><td></td><td><a href="https://github.com/GitbookIO/gitbook-templates/blob/main/product-docs/broken-reference/README.md">https://github.com/GitbookIO/gitbook-templates/blob/main/product-docs/broken-reference/README.md</a></td></tr><tr><td><h4><i class="fa-globe-pointer">:globe-pointer:</i></h4></td><td><strong>Join</strong></td><td>Mint a name</td><td><a href=".gitbook/assets/names.png">names.png</a></td><td></td><td><a href="broken-reference">Broken link</a></td></tr></tbody></table>

### Features at a glance

| Capability              | Summary                                                            |
| ----------------------- | ------------------------------------------------------------------ |
| Human‑readable names    | Register unique names between 3 and 63 characters.                 |
| Broad character support | ASCII plus curated Unicode ranges (CJK, Hangul, etc.).             |
| Address resolution      | Names resolve to wallet addresses, with owner fallback.            |
| Profiles                | Store text records like avatar, URL, and social links.             |
| Content hashes          | Point a name at IPFS/Arweave content.                              |
| Reverse resolution      | Set a primary name so apps can display your name for your address. |
| Renewals                | Keep ownership active by renewing for one or more years.           |
| Secure transfers        | Resolver data is cleared when a name transfers.                    |

If you’re new to Abstract, start with the basics to understand global wallets and why names make them easier to use. Developers can jump to the contracts and build sections for integration details.

