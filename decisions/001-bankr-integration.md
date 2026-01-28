# Decision 001: Use Bankr for Trade Execution

**Date:** 2026-01-28  
**Status:** Accepted  
**Context:** lgi-mm architecture

## Summary

lgi-mm will use Bankr as the execution layer for all trades, rather than direct blockchain interaction via ethers.js.

## Context

The original `clawdia-mm` implementation used:
- ethers.js for blockchain interaction
- Direct Uniswap V3 router calls
- Custom wallet management via .env
- Base chain only

This approach required significant infrastructure:
- RPC endpoint management
- Private key handling
- Gas estimation and management
- Chain-specific contract ABIs
- Error handling for blockchain edge cases

## Decision

Delegate all trade execution to Bankr via natural language API.

### Before (clawdia-mm)
```typescript
const router = new ethers.Contract(UNISWAP_ROUTER, ABI, wallet);
await router.exactInputSingle({
  tokenIn: WETH,
  tokenOut: CLAWDIA,
  fee: 10000,
  recipient: wallet.address,
  amountIn: ethers.parseEther("0.01"),
  amountOutMinimum: 0,
  sqrtPriceLimitX96: 0
});
```

### After (lgi-mm)
```bash
scripts/bankr.sh "Buy $50 of TOKEN on Base"
```

## Consequences

### Positive
- **Simpler code**: No blockchain plumbing
- **Multi-chain**: Bankr supports Base, ETH, Polygon, Solana, Unichain
- **Managed wallets**: No private key handling
- **Maintained**: Bankr team handles DEX integrations
- **Composable**: Other Clawdbot skills can reuse

### Negative
- **Dependency**: Requires Bankr skill and API key
- **Latency**: Extra API hop vs direct calls
- **Control**: Less fine-grained execution control
- **Cost**: Bankr may have fees (TBD)

### Neutral
- **Learning curve**: Different mental model for trades

## Alternatives Considered

1. **Keep ethers.js**: More control, but more maintenance
2. **Build our own execution layer**: Maximum control, massive effort
3. **Use multiple DEX aggregators**: Complex, fragmented

## Notes

This decision prioritizes:
- Developer experience over raw performance
- Composability over independence
- Shipping fast over building everything

We can always add direct execution later if needed.

---

*First architectural decision. Embrace simplicity.* 🦞
