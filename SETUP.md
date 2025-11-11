# Photon CP-Swap Setup Guide

This guide will help you rebrand and deploy the Photon CP-Swap program to Solana devnet.

## Prerequisites

1. **Install Rust**
   ```bash
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   rustup default 1.81.0
   ```

2. **Install Solana CLI**
   ```bash
   sh -c "$(curl -sSfL https://release.anza.xyz/v2.1.0/install)"
   ```

3. **Install Anchor**
   ```bash
   # Install Anchor version manager (avm)
   cargo install --git https://github.com/coral-xyz/anchor avm --locked --force
   # Install Anchor 0.31.0
   avm install 0.31.0
   avm use 0.31.0
   ```

4. **Install Node.js dependencies**
   ```bash
   yarn install
   # or
   npm install
   ```

## Step 1: Configure Solana for Devnet

1. **Set cluster to devnet**
   ```bash
   solana config set --url devnet
   ```

2. **Create or configure your keypair**
   ```bash
   # Create a new keypair (if you don't have one)
   solana-keygen new -o ~/.config/solana/id.json
   
   # Or use existing keypair
   solana config set --keypair ~/.config/solana/id.json
   ```

3. **Airdrop SOL for deployment** (Devnet only)
   ```bash
   solana airdrop 5
   # Run multiple times if you need more SOL
   solana airdrop 5
   ```

4. **Check your balance**
   ```bash
   solana balance
   ```

## Step 2: Generate a New Program ID

1. **Generate a new keypair for your program**
   ```bash
   solana-keygen new -o target/deploy/photon_cp_swap-keypair.json
   ```

2. **Get the program ID**
   ```bash
   solana-keygen pubkey target/deploy/photon_cp_swap-keypair.json
   ```
   
   Save this program ID - you'll need it for the next steps.

## Step 3: Update Program Configuration

The rebranding process has already updated:
- ✅ `Anchor.toml` - Changed to devnet cluster and photon_cp_swap
- ✅ `programs/cp-swap/Cargo.toml` - Updated package name
- ✅ `programs/cp-swap/src/lib.rs` - Updated module name and declare_id
- ✅ `README.md` - Updated branding
- ✅ `client_config.ini` - Updated configuration

**Important:** You need to update the `declare_id!` in `programs/cp-swap/src/lib.rs`:

1. Open `programs/cp-swap/src/lib.rs`
2. Find the line with `#[cfg(feature = "devnet")]` and `declare_id!`
3. Replace the existing devnet program ID with your new program ID from Step 2:
   ```rust
   #[cfg(feature = "devnet")]
   declare_id!("YOUR_NEW_PROGRAM_ID_HERE");
   ```

4. Also update the `Anchor.toml` file:
   ```toml
   [programs.devnet]
   photon_cp_swap = "YOUR_NEW_PROGRAM_ID_HERE"
   ```

## Step 4: Build the Program

1. **Build with devnet feature**
   ```bash
   anchor build -- --features devnet
   ```

2. **Verify the build**
   ```bash
   ls -la target/deploy/
   ```
   
   You should see `photon_cp_swap.so` file.

## Step 5: Deploy to Devnet

1. **Deploy the program**
   ```bash
   anchor deploy --provider.cluster devnet
   ```

2. **Verify deployment**
   ```bash
   solana program show YOUR_PROGRAM_ID --url devnet
   ```

## Step 6: Run Tests on Devnet

1. **Update test configuration** (if needed)
   The `Anchor.toml` is already configured for devnet.

2. **Run tests**
   ```bash
   anchor test --provider.cluster devnet -- --features devnet
   ```

## Step 7: Initialize Your AMM Config

Before creating pools, you need to create an AMM configuration:

1. **Use the client or write a script to create config**
   ```bash
   cd client
   cargo run -- create-config
   ```

   Or use the TypeScript tests as a reference to create your config with:
   - Trade fee rate
   - Protocol fee rate
   - Fund fee rate
   - Creator fee rate (optional)

## Common Issues and Solutions

### Issue: Insufficient SOL
```bash
# Request more airdrops
solana airdrop 5
```

### Issue: Program deployment fails
```bash
# Check your balance
solana balance

# Increase compute units if needed
solana program deploy target/deploy/photon_cp_swap.so --upgrade-authority ~/.config/solana/id.json --max-len 500000
```

### Issue: Program ID mismatch
- Make sure the program ID in `lib.rs` matches the keypair in `target/deploy/photon_cp_swap-keypair.json`
- Rebuild after changing the program ID: `anchor build -- --features devnet`

## Program Architecture

### Key Components:
- **AMM Config**: Defines fee structure and protocol settings
- **Pool State**: Tracks liquidity pool information
- **Oracle**: Built-in price oracle for each pool
- **Permissions**: Optional permission system for restricted pools

### Main Instructions:
- `create_amm_config`: Initialize AMM configuration
- `initialize`: Create a new liquidity pool
- `deposit`: Add liquidity to a pool
- `withdraw`: Remove liquidity from a pool
- `swap_base_input`: Swap with exact input amount
- `swap_base_output`: Swap with exact output amount

## Next Steps

1. **Create AMM Config**: Set up your fee structure
2. **Create Pools**: Initialize liquidity pools for token pairs
3. **Add Liquidity**: Deposit tokens into pools
4. **Test Swaps**: Perform test swaps on devnet
5. **Monitor**: Use Solana Explorer (devnet) to monitor transactions

## Useful Commands

```bash
# Check program account
solana program show YOUR_PROGRAM_ID --url devnet

# Get program logs
solana logs YOUR_PROGRAM_ID --url devnet

# Check account info
solana account YOUR_ACCOUNT_ADDRESS --url devnet

# Get recent transactions
solana transaction-history YOUR_PROGRAM_ID --url devnet
```

## Resources

- [Anchor Documentation](https://www.anchor-lang.com/)
- [Solana Documentation](https://docs.solana.com/)
- [Solana Devnet Explorer](https://explorer.solana.com/?cluster=devnet)
- [Photon CP-Swap Repository](https://github.com/yourusername/photon-cp-swap)

## Support

For issues and questions, please refer to:
- Solana Stack Exchange
- Anchor Discord
- Solana Discord

---

**Note**: This is a devnet deployment. For mainnet deployment, additional security audits and testing are strongly recommended.
