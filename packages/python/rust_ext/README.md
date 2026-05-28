# haven-aol-vetkeys

Native VetKD (Verifiable Threshold Key Derivation) helpers for Haven-AOL,
built as a PyO3 extension module in Rust.

## Installation

```bash
pip install haven-aol-vetkeys
```

## API

### `derive_verification_key(key_name, canister_id_bytes, context) -> bytes`

Derive the VetKD verification key offline from the ICP mainnet master public key.

**Parameters:**
- `key_name` (str): Master key name (e.g. `"key_1"` for local dev, `"insecure_test_key_1"` for mainnet v1)
- `canister_id_bytes` (bytes): Canister principal as raw bytes
- `context` (bytes): Domain separator (default: `b"accessol_v1"`)

**Returns:** 96-byte serialized DerivedPublicKey (compressed G2 point on BLS12-381)

### `deserialize_derived_public_key(bytes) -> bytes`

Validate and reserialize a DerivedPublicKey (useful for local dev mode).

### `ibe_encrypt(derived_public_key_bytes, identity_bytes, plaintext) -> bytes`

IBE-encrypt plaintext using a derived public key and identity.

**Parameters:**
- `derived_public_key_bytes` (bytes): 96-byte serialized DerivedPublicKey
- `identity_bytes` (bytes): IBE identity (e.g. the 32-byte derivation input hash)
- `plaintext` (bytes): Data to encrypt (e.g. a 32-byte AES key)

**Returns:** Serialized IbeCiphertext bytes

## Dependencies

- `ic-vetkeys` — ICP VetKD library (Rust)
- `ic-management-canister-types` — ICP management canister types (Rust)
- `pyo3` — Python/Rust bindings

## License

MIT
