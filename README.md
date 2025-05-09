# BTC-Bridge Payment Channels

**A Bitcoin-compatible Layer 2 payment channel implementation for the Stacks blockchain.**

## Overview

**BTC-Bridge** enables scalable, off-chain transactions on the Stacks blockchain with on-chain settlement guarantees. Inspired by the Bitcoin Lightning Network, this smart contract facilitates secure, high-throughput microtransactions using Bitcoin-style principles while leveraging Stacks smart contract capabilities.

## Features

* **Bidirectional Payment Channels**
  Supports cooperative and unilateral opening, funding, and closing of payment channels between two participants.

* **Bitcoin-Compatible Cryptography**
  Uses secp256k1 ECDSA signature format and BIP32-derived identifiers to maintain compatibility with the Bitcoin Lightning Network.

* **Off-chain Transactions with On-chain Guarantees**
  Channel balances can be updated off-chain, with secure on-chain settlement and dispute resolution.

* **Stacks Smart Contract Integration**
  Leverages Clarity's predictable execution for state tracking, fund management, and secure contract logic.

* **Dispute Resolution System**
  Unilateral closure with a 144-block (\~24-hour) dispute window to prevent malicious settlement.

* **Interoperability Ready**
  Designed with cross-chain compatibility in mind for future Lightning Network integration.

## Contract Architecture

### Channel Lifecycle

1. **Create Channel**:
   One participant opens a channel and deposits funds.

2. **Fund Channel**:
   Channels can be funded incrementally by the initiator.

3. **Off-chain Updates**:
   Participants exchange updated balance commitments signed off-chain.

4. **Close Channel (Cooperative)**:
   Both parties submit signatures for agreed balances to close the channel.

5. **Close Channel (Unilateral)**:
   One party can initiate closure; funds are locked for 144 blocks before release.

### Data Model

Channel data is stored in a map with:

* `channel-id`: 256-bit identifier
* `participant-a` / `participant-b`: Stacks principals
* `total-deposited`, `balance-a`, `balance-b`: STX balances
* `is-open`: Boolean flag
* `dispute-deadline`: Block height for timeout
* `nonce`: BIP32-based versioning

## Public Functions

| Function                    | Description                                                           |
| --------------------------- | --------------------------------------------------------------------- |
| `create-channel`            | Initializes a new payment channel.                                    |
| `fund-channel`              | Adds funds to an existing open channel.                               |
| `close-channel-cooperative` | Closes the channel with mutual consent and signatures.                |
| `initiate-unilateral-close` | Starts a unilateral closure with proposed balances and a lock period. |
| `resolve-unilateral-close`  | Finalizes a unilateral closure after the dispute period.              |
| `get-channel-info`          | Returns current state of a specific channel.                          |
| `emergency-withdraw`        | Allows the contract owner to withdraw all funds (restricted).         |

## Security Considerations

* ✅ **Signature Verification**: Ensures both parties authorize balance changes.
* ⛔ **Unauthorized Access Prevention**: Role checks for channel operations and emergency withdrawal.
* 🕒 **Dispute Handling**: Enforces a mandatory waiting period for unilateral closures.

## Requirements

* **Stacks Blockchain**: Deploys on Stacks mainnet/testnet.
* **Clarity Smart Contract Language**: Written in Clarity with Clarinet compatibility.
* **Bitcoin Compatibility**: Expects Bitcoin-style 65-byte signatures and BIP32 identifiers.

---

## Development & Testing

Use [Clarinet](https://docs.hiro.so/clarinet/overview) to test and deploy the contract locally:

```bash
clarinet check        # Validate contract syntax
npm test         # Run unit tests
clarinet deployment       # Deploy to local network
```

## Future Work

* **HTLC Support**: Enable time-locked cross-chain atomic swaps.
* **Watchtower Integration**: Add support for third-party dispute monitoring.
* **Lightning Network Integration**: Direct compatibility with LN clients.
