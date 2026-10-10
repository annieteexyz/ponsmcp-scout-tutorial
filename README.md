# Pay an AI Agent with PonsMCP and x402

A beginner-friendly guide to discovering and paying an AI agent through PonsMCP using the x402 payment protocol on Robinhood Chain.

## 1. What this tutorial demonstrates

Traditional APIs often require an account, an API key, or a subscription. With x402, an API can return HTTP `402 Payment Required` when a resource requires payment.

PonsMCP provides tools that can help an agent discover paid resources, handle supported x402 payment requirements, and retry a request with payment proof.

The flow is:

1. Discover a paid resource.
2. Request the resource using `x402_fetch`.
3. If the server returns HTTP 402, inspect the payment requirement.
4. PonsMCP checks the payment against its configured policy.
5. For a supported and approved requirement, it settles the payment and retries with the required proof.
6. Receive the resource response.

**Important:** A successful installation does not prove that a paid request has completed. This tutorial documents the setup and workflow; a live paid PonsMCP request has not been performed as part of this submission.

## 2. Requirements

- Node.js and npm
- A compatible MCP client/runtime
- The PonsMCP SDK
- A wallet configured for the supported payment network
- Sufficient native-token gas funds and the payment token required by the resource

PonsMCP's setup wizard identifies Robinhood Chain (chain ID `4663`) and recommends at least `0.005 ETH` for gas, plus USDG for payments. These are setup recommendations, not a guarantee that every transaction will succeed.

Do not fund a wallet unless you can afford the expense. You can read and prepare this tutorial without making a payment.

## 3. Install the SDK

Check that Node.js and npm are installed:

```bash
node --version
npm --version
```

Install the package:

```bash
npm install @ponsmcp/sdk dotenv
```

Check the installed SDK version:

```bash
npm list @ponsmcp/sdk
```

This project used PonsMCP SDK version `2.3.1` during setup.

## 4. Configure PonsMCP

Run the setup wizard:

```bash
npx ponsmcp-setup
```

Follow the prompts to configure the wallet and network. The setup wizard used in this walkthrough saved its environment configuration to:

```text
~/.ponsmcp.env
```

The wizard may offer an RPC selection, an optional Alchemy key, and a maximum payment amount. Choose a limit you can afford. For a demonstration, a low transaction cap such as `0.01 USDG` reduces the maximum permitted payment per transaction, but it does not eliminate gas costs or all risk.

The setup wizard reported Robinhood Chain's public RPC as:

```text
https://rpc.nodeflare.app/robinhood/public
```

Explorer:

```text
https://robinhoodchain.blockscout.com
```

Configuration and available options may change between SDK versions, so consult the official documentation if your prompts differ.

### Keep your wallet safe

- Never paste a private key or recovery phrase into a public repository, chat, screenshot, or issue.
- Never commit `~/.ponsmcp.env`.
- Do not print the contents of your environment file in a terminal screenshot.
- Only make payments to resources and recipients you trust.
- Check the quoted amount and network before approving a payment.

## 5. Discover a paid resource

PonsMCP exposes an MCP tool named `x402_discover`. It can check a domain for discoverable x402 resources and their advertised requirements without paying for the resource.

Example MCP tool arguments:

```json
{
  "name": "x402_discover",
  "arguments": {
    "domain": "api.a2a.family"
  }
}
```

This is an illustrative tool call for an MCP client, not a shell command. The actual tool-call interface depends on your MCP client.

Review the returned resource URLs, descriptions, prices, and payment requirements before proceeding. Discovery does not guarantee that a resource will remain available or that its price will remain unchanged.

## 6. Request an agent resource with x402_fetch

PonsMCP exposes an MCP tool named `x402_fetch`. It can fetch a URL and, when a supported HTTP 402 payment requirement is returned, attempt policy-checked payment and retry with the signed proof header.

Illustrative MCP tool arguments:

```json
{
  "name": "x402_fetch",
  "arguments": {
    "url": "https://api.a2a.family/a/scout/v1/grade_launches?n=1"
  }
}
```

**Do not execute this example just to complete the documentation.** It is a paid-resource example and may incur a payment and network gas fees. First discover the resource, confirm the current price and payment terms, and make sure your wallet has sufficient funds. If you cannot afford the cost, stop after discovery.

The example endpoint and its availability should be verified against the current a2a.family documentation before a real request.

## 7. What to verify after a real request

If you later choose to perform a paid request, verify all of the following:

- The tool returned the expected resource payload.
- The payment was within the configured policy limit.
- The transaction receipt indicates success.
- The payment token transfer and amount match the quoted requirement.
- The transaction is visible on the correct network's block explorer.

A tool response alone should not be treated as proof of on-chain settlement. Save a sanitized response and transaction reference as evidence, but never include secrets or private keys.

## 8. Troubleshooting

**The setup wizard completes, but I cannot call an MCP tool.**  
The SDK package provides MCP tooling, but a compatible MCP client/runtime must be configured to launch and communicate with the server. Follow the official setup instructions for your specific client.

**The request fails for insufficient funds.**  
Do not repeatedly retry. Check the required payment token, native-token gas balance, network, and configured policy. Wait until you can safely fund the wallet.

**The request is rejected by policy.**  
Review the configured maximum payment and the resource's quoted requirement. Do not increase the limit unless you understand and can afford the expense.

**The endpoint does not return a paid resource.**  
Use `x402_discover` and the provider's current documentation to confirm the correct endpoint and requirements.

## 9. Verification status for this walkthrough

| Item | Status |
| --- | --- |
| Node.js and npm available | Verified |
| PonsMCP SDK installed | Verified |
| Setup wizard run | Verified |
| Payment workflow documented | Complete |
| Live PonsMCP paid request | Not performed |
| On-chain PonsMCP payment evidence | Not available |

This status is intentionally transparent: the installation and documentation steps were completed, but no live PonsMCP payment is claimed.

## 10. Official references

- PonsMCP documentation: https://ponsmcp.com/docs
- x402 compatibility guide: https://ponsmcp.com/docs/x402-compatibility
- PonsMCP SDK package: https://www.npmjs.com/package/@ponsmcp/sdk
- Robinhood Chain explorer: https://robinhoodchain.blockscout.com

---

**Tutorial goal:** Show how a developer can install PonsMCP, discover an x402 resource, understand the payment flow, and make a safe decision before authorizing an agent payment.
