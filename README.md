# badnonce

A from-scratch implementation of ECDSA on secp256k1, together with a working
demonstration of the nonce-reuse attack that broke the PlayStation 3 and has
drained real Bitcoin wallets.

## What this is

ECDSA — the digital signature scheme behind Bitcoin, Ethereum, and most of
TLS — is secure only if the per-signature nonce `k` is generated freshly and
uniformly at random. Sign two different messages with the same `k` and the
private key falls out of two subtractions and a modular inverse.

This project builds the cryptosystem from finite-field arithmetic up, then
carries out the attack end-to-end on a key it generated itself, and finally
replays the attack against historical reused-nonce transactions on the
Bitcoin blockchain.

## Status

🚧 In progress. Currently in the reading and derivation phase. See
`notes/` for ongoing study notes and `STUDY-PLAN.md` for the roadmap.

## Planned components

- **`src/field.py`** — Finite field arithmetic over the secp256k1 prime,
  including modular inverse via extended Euclidean algorithm.
- **`src/curve.py`** — Elliptic curve group operations on y² = x³ + 7:
  point addition, doubling, and scalar multiplication via double-and-add.
- **`src/ecdsa.py`** — Key generation, signing, and verification, validated
  against a reference implementation.
- **`src/attack.py`** — Private-key recovery from two signatures sharing
  a nonce.
- **`demo/`** — End-to-end attack on a self-generated keypair, plus
  historical replay against real reused-nonce Bitcoin transactions.

## Why it matters

Nonce reuse isn't a hypothetical bug. It's how Sony's PS3 firmware signing
key was extracted in 2010, and reused-nonce Bitcoin transactions have leaked
keys controlling nontrivial sums. Implementing the attack makes concrete
why crypto engineers treat RNG quality as a first-class concern and why
deterministic nonces (RFC 6979) became standard.

## Mathematical background

The project draws on elliptic curves over finite fields, group theory (the
curve points form a cyclic group of prime order n), and modular arithmetic.
The attack itself reduces to solving a 2×2 linear system mod n.

Primary references:
- Boneh & Shoup, *A Graduate Course in Applied Cryptography*
- Washington, *Elliptic Curves: Number Theory and Cryptography*
- SEC 1: Elliptic Curve Cryptography (the ECDSA spec)
- RFC 6979: Deterministic Usage of DSA and ECDSA

## Roadmap

- [ ] Field arithmetic over the secp256k1 prime
- [ ] Elliptic curve group operations
- [ ] ECDSA sign and verify
- [ ] Nonce-reuse key recovery
- [ ] Historical replay on Bitcoin testnet
- [ ] **Stretch:** Lattice-based recovery from biased nonces (LLL reduction)
- [ ] **Stretch:** Constant-time scalar multiplication via Montgomery ladder
- [ ] **Stretch:** BIP-340 Schnorr signatures

## Disclaimer

This is a learning project. Do not use this code for anything real. Use
[libsecp256k1](https://github.com/bitcoin-core/secp256k1) or a similar
audited library.

## License

MIT
