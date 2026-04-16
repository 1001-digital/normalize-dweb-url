# @1001-digital/normalize-dweb-url

Normalize IPFS, IPNS, and Arweave URIs -- gateway detection, redundant path removal, raw CID handling.

```
npm install @1001-digital/normalize-dweb-url
```

## Usage

```ts
import { normalizeUri, isResolvableUri } from '@1001-digital/normalize-dweb-url'

normalizeUri('https://gateway.pinata.cloud/ipfs/QmXxx')  // 'ipfs://QmXxx'
normalizeUri('https://bafyABC.ipfs.nftstorage.link')     // 'ipfs://bafyABC'
normalizeUri('https://arweave.net/txId123')               // 'ar://txId123'
normalizeUri('QmYwAPJzv5CZsnA625s3Xf2nemtYgPpHdWEz79ojWnPbdG')
// 'ipfs://QmYwAPJzv5CZsnA625s3Xf2nemtYgPpHdWEz79ojWnPbdG'

isResolvableUri('ipfs://QmXxx')  // true
isResolvableUri('ar://txId123')  // true
isResolvableUri('hello world')   // false
```

## `normalizeUri(uri: string): string`

Converts gateway URLs and raw CIDs into their canonical protocol form.

| Input | Output |
| --- | --- |
| `https://ipfs.io/ipfs/QmXxx` | `ipfs://QmXxx` |
| `https://bafyABC.ipfs.w3s.link/meta.json` | `ipfs://bafyABC/meta.json` |
| `https://ipfs.io/ipns/example.eth` | `ipns://example.eth` |
| `https://arweave.net/txId123` | `ar://txId123` |
| `ipfs://ipfs/QmXxx` | `ipfs://QmXxx` |
| `ipfs://https://gateway.pinata.cloud/ipfs/QmXxx` | `ipfs://QmXxx` |
| `QmYwAPJzv5CZsnA625s3Xf2nemtYgPpHdWEz79ojWnPbdG` | `ipfs://QmYwAPJzv5...` |
| `data:application/json;base64,...` | passed through |
| `https://example.com/image.png` | passed through |

**Handles:**
- Path-based IPFS/IPNS gateways (`/ipfs/...`, `/ipns/...`)
- Subdomain IPFS/IPNS gateways (`*.ipfs.nftstorage.link`, `*.ipns.dweb.link`, etc.)
- Arweave gateways (`arweave.net`, `ar-io.dev`, `ar-io.net`, `arweave.dev`)
- Redundant path segments (`ipfs://ipfs/Qm...` -> `ipfs://Qm...`)
- Embedded gateway URLs (`ipfs://https://gateway.pinata.cloud/ipfs/Qm...`)
- Raw CIDv0 (`Qm...`) and CIDv1 (`bafy...`) strings
- Whitespace trimming

## `isResolvableUri(value: unknown): value is string`

Type guard that returns `true` when the value is a string that looks like a resolvable URI: `ipfs://`, `ipns://`, `ar://`, `http(s)://`, `data:`, or a raw CID.

Rejects non-strings, strings shorter than 5 characters, and non-`data:` URIs longer than 2048 characters.

## License

MIT
