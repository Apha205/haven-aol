# haven-aol

**Haven-AOL** (Always Online on DFINITY ICP): upload-side encryption for
conditional token-gated access. Encrypt files and publish gate metadata
that can only be unlocked by EVM wallets holding a minimum balance of a
specific ERC-20 token.

## Installation

```bash
pip install haven-aol
```

This automatically installs `haven-aol-vetkeys` (the native VetKD extension),
`cryptography` (AES-GCM), and `icp-py-core` (principal parsing).

## Quick Start

```python
from haven_aol import (
    compute_derivation_input,
    encrypt_file,
    derive_verification_key,
    ibe_encrypt_aes_key,
    build_gate_metadata,
    serialize_gate_metadata,
)

# 1. Encrypt a file
ciphertext, aes_key, iv = encrypt_file(b"secret content")

# 2. Upload ciphertext to IPFS (caller's responsibility)
#    -> get back a CID, e.g. "QmXoypizj..."

# 3. Compute the gate identity
derivation_input = compute_derivation_input(
    chain="EthMainnet",
    token_address="0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48",
    threshold=1_000_000,
    cid="QmXoypizjW3WknFiJnKLwHCnL72vedxjQkDDP1mXWo6uco",
)

# 4. Derive the VetKD verification key (offline)
vkey = derive_verification_key(
    canister_id="dciac-uaaaa-aaaad-qlzuq-cai",
    key_name="insecure_test_key_1",
)

# 5. IBE-encrypt the AES key
encrypted_aes_key = ibe_encrypt_aes_key(aes_key, vkey, derivation_input)

# 6. Build and publish gate metadata
metadata = build_gate_metadata(
    cid="QmXoypizjW3WknFiJnKLwHCnL72vedxjQkDDP1mXWo6uco",
    chain="EthMainnet",
    token_address="0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48",
    threshold=1_000_000,
    encrypted_aes_key=encrypted_aes_key,
)
json_output = serialize_gate_metadata(metadata)
# Publish json_output via URL, QR, NFT attribute, etc.
```

## API

| Function | Description |
|----------|-------------|
| `compute_derivation_input(chain, token_address, threshold, cid)` | SHA-256 gate identity hash (32 bytes) |
| `encrypt_file(plaintext, aes_key=None)` | AES-256-GCM encrypt; returns `(ciphertext, key, iv)` |
| `derive_verification_key(canister_id, context, key_name)` | Offline VetKD public key derivation (96 bytes) |
| `ibe_encrypt_aes_key(aes_key, derived_public_key, derivation_input)` | IBE-encrypt the AES key |
| `build_gate_metadata(cid, chain, token_address, threshold, encrypted_aes_key)` | Build gate metadata dict |
| `serialize_gate_metadata(metadata)` | Serialize to compact JSON string |

## Supported Chains

- `EthMainnet`
- `EthSepolia`
- `ArbitrumOne`
- `BaseMainnet`
- `OptimismMainnet`

## License

MIT
