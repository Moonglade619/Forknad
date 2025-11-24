# Forknad
A Solana native fork connecting Monad and Solana
# Solana ↔ Monad Native Bridge

A Solana-native bridge for a Monad chain fork that connects **Solana** and **Monad** at the protocol level.

This repository contains:

- 🧱 An on-chain Solana program (`monad_bridge`) written in Rust (Anchor).
- 🌉 A chain-agnostic bridge design for a Monad fork that is “Solana-native” in its data model.
- 🛰 A lightweight TypeScript relayer that listens to Solana events and submits them to Monad (and vice versa).
- 📦 A small JS/TS SDK for dApps to interact with the Solana side.

> ⚠️ **Disclaimer**: This is a reference implementation / template.  
> You MUST add your own production-grade verification, proofs, and security hardening before using it with real value.

---

## Architecture

### High-level

1. **Solana program (`monad_bridge`)**
   - Holds configuration and state for the Monad fork.
   - Emits events/logs for outbound messages.
   - Verifies and processes inbound messages coming from Monad (via relayer).

2. **Monad fork**
   - Implements a symmetric bridge contract/module that understands the same message format.
   - Verifies messages that originated from Solana and updates its own state.

3. **Relayer**
   - Off-chain service that:
     - Listens to Solana logs for `OutboundMessage` events.
     - Submits them to Monad’s bridge contract (or native module).
     - Listens to Monad events and submits them to Solana via `receive_from_monad`.

4. **SDK**
   - Simple JS/TS wrapper around the Solana program.
   - Provides helpers for:
     - Sending messages to Monad (`send_to_monad`).
     - Querying bridge state & config.

---

## Message Format

Messages are chain-agnostic and encoded as a simple struct:

```rust
pub struct BridgeMessage {
    pub nonce: u64,
    pub source_chain: u16,
    pub target_chain: u16,
    pub sender: [u8; 32],
    pub recipient: [u8; 32],
    pub payload: Vec<u8>, // arbitrary data (e.g. token transfer, governance call, etc.)
}
