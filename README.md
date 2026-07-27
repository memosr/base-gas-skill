# base-gas-skill

Agent skill for [base-gas-x402](https://github.com/memosr/base-gas-x402): live and historical Base mainnet gas data, paid per call in USDC over [x402](https://www.x402.org).

Gives your agent gas awareness. It can check what gas costs right now, price a specific operation, tell you whether gas is currently high or low, compare Base against other L2s, and find the cheapest hours to transact.

## Installation

### CLI mode (recommended)

```bash
npx skills add memosr/base-gas-skill --all --yes
```

Restart your agent, then start a new chat and ask:

```
What is gas on Base right now?
```

### MCP mode

Install the AgentCash MCP server first:

```bash
npx agentcash@latest install -y
```

Then install the skill:

```bash
npx skills add memosr/base-gas-skill/mcp --all --yes
```

Both directories contain the same skill. Pick the mode that matches your setup.

## What it covers

| Task | Route | Price |
|------|-------|-------|
| Current gas, cost estimate for any gas limit | `GET /gas` | $0.005 |
| Compare Base, OP Mainnet, Arbitrum, Ethereum | `GET /gas/compare` | $0.01 |
| Historical trend with a cheap/normal/expensive verdict | `GET /gas/history` | $0.012 |
| Cheapest hours of day, ranked, with savings percent | `GET /gas/cheapest-window` | $0.02 |
| How much history has been collected | `GET /health` | free |

Prices are set by the endpoint and published in its [OpenAPI document](https://base-gas-x402-production.up.railway.app/openapi.json), which is the source of truth if this table ever drifts.

## Requirements

A funded AgentCash wallet. If you do not have one:

```bash
npx agentcash@latest onboard
npx agentcash@latest balance
```

Payment settles in USDC on Base mainnet (`eip155:8453`). No API key, no account, no subscription.

## Try it without installing anything

The free health route needs no wallet:

```bash
curl -s https://base-gas-x402-production.up.railway.app/health
```

A paid call, once you have a balance:

```bash
npx agentcash@latest fetch "https://base-gas-x402-production.up.railway.app/gas?gasLimit=180000"
```

## Notes on cost

The skill includes [`rules/choosing-an-endpoint.md`](base-gas/rules/choosing-an-endpoint.md), which steers agents away from the two expensive mistakes:

- Looping `/gas` to build a time series, when one `/gas/history` call already has it
- Calling `/gas` once per chain, when `/gas/compare` reads four chains in a single request

It also documents `/health` as a free pre-check, so an agent can confirm there is enough history before paying for a history route.

## Links

- API source: [memosr/base-gas-x402](https://github.com/memosr/base-gas-x402)
- MCP server: [`base-gas-mcp`](https://www.npmjs.com/package/base-gas-mcp)
- Live service: https://base-gas-x402-production.up.railway.app

## License

MIT
