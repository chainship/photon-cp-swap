# photon-cp-swap

A revamped constant product AMM program optimized for straightforward pool deployment along with additional features and integrations:
- No Openbook market ID is required for pool creation
- Token22 is supported
- Built-in price oracle
- Optimized in Anchor

This project is a rebranded version originally based on Raydium's CP-Swap implementation.

## Environment Setup

1. Install `Rust`

   ```shell
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   rustup default 1.81.0
   ```

2. Install `Solana `

   ```shell
   sh -c "$(curl -sSfL https://release.anza.xyz/v2.1.0/install)"
   ```

   then run `solana-keygen new` to create a keypair at the default location.

3. install `Anchor`

   ```shell
   # Installing using Anchor version manager (avm) 
   cargo install --git https://github.com/coral-xyz/anchor avm --locked --force
   # Install anchor
   avm install 0.31.0
   ```

## Quickstart

Clone the repository and test the program.

```shell
git clone https://github.com/yourusername/photon-cp-swap
cd photon-cp-swap && yarn && anchor test
```

## Devnet Deployment

For a complete guide on deploying to devnet and rebranding, see [SETUP.md](./SETUP.md).

Quick steps:
```shell
# Set to devnet
solana config set --url devnet

# Airdrop SOL
solana airdrop 5

# Build with devnet feature
anchor build -- --features devnet

# Deploy
anchor deploy --provider.cluster devnet
```

## License

Photon constant product swap is licensed under the Apache License, Version 2.0.

