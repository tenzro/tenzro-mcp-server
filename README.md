# Tenzro MCP Server

The official [Model Context Protocol](https://modelcontextprotocol.io) server for [Tenzro Network](https://tenzro.com) — giving AI agents direct access to blockchain operations, token management, cross-chain bridges, NFTs, identity, compliance, event streaming, and more.

[![Python](https://img.shields.io/badge/python-3.10+-blue)](https://python.org)
[![MCP](https://img.shields.io/badge/MCP-2024--11--05-blue)](https://modelcontextprotocol.io)
[![License](https://img.shields.io/badge/license-Apache--2.0-green)](LICENSE)

## Overview

The Tenzro MCP server is an installable Python package that exposes **146 blockchain tools** across 26 categories to any MCP-compatible AI agent (Claude, GPT, Cursor, Windsurf, etc.) via **stdio** or **Streamable HTTP** transport. Install with `pip install tenzro-mcp-server` and run locally, or connect directly to the live testnet endpoint. Agents can query balances, send transactions, mint NFTs, bridge tokens across 58+ chains, check compliance, subscribe to events, and interact with AI models — all through the standard MCP tool interface.

**Testnet endpoint:** `https://mcp.tenzro.network/mcp`
**Local:** `http://localhost:3001/mcp`

## Installation

```bash
pip install tenzro-mcp-server
```

Or from source:

```bash
git clone https://github.com/tenzro/tenzro-mcp-server.git
cd tenzro-mcp-server
pip install .
```

## Quick Start

### Claude Desktop

Add to `~/Library/Application Support/Claude/claude_desktop_config.json`:

**Option A: Connect to live testnet**

```json
{
  "mcpServers": {
    "tenzro": {
      "command": "npx", "args": ["-y", "mcp-remote", "https://mcp.tenzro.network/mcp"]
    }
  }
}
```

**Option B: Run locally**

```json
{
  "mcpServers": {
    "tenzro": {
      "command": "tenzro-mcp-server"
    }
  }
}
```

### Claude Code

Add to your project's `.mcp.json`:

**Option A: Connect to live testnet**

```json
{
  "mcpServers": {
    "tenzro": {
      "type": "url",
      "command": "npx", "args": ["-y", "mcp-remote", "https://mcp.tenzro.network/mcp"]
    }
  }
}
```

**Option B: Run locally**

```json
{
  "mcpServers": {
    "tenzro": {
      "type": "stdio",
      "command": "tenzro-mcp-server"
    }
  }
}
```

### Cursor / Windsurf / Other MCP Clients

**Option A: Connect to live testnet**
- **Name:** tenzro
- **Transport:** Streamable HTTP
- **URL:** `https://mcp.tenzro.network/mcp`

**Option B: Run locally**
- **Name:** tenzro
- **Command:** `tenzro-mcp-server`

Or with Streamable HTTP transport:
- **URL:** `http://localhost:3001/mcp`
- Start the server first: `tenzro-mcp-server --transport http --port 3001`

## Available Tools

The server provides **146 tools** across 26 categories:

### Wallet & Ledger (4 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `create_wallet` | Generate new Ed25519 or Secp256k1 keypair | `key_type` |
| `get_balance` | Query TNZO balance by address | `address` |
| `send_transaction` | Send TNZO transfer with gas estimation | `from`, `to`, `amount`, `gas_limit` |
| `request_faucet` | Request 100 testnet TNZO (24h cooldown) | `address` |

### Network & Blocks (3 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `get_node_status` | Node health, block height, peers, uptime, role | — |
| `get_block` | Get block by height with transactions and metadata | `height` |
| `get_transaction` | Look up transaction by hash | `tx_hash` |

### Identity & Delegation (5 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `register_identity` | Register human or machine DID via TDIP | `identity_type`, `display_name`, `controller_did` |
| `resolve_did` | Resolve DID to identity info and delegation scope | `did` |
| `set_delegation_scope` | Set spending limits, allowed ops, protocols, chains for machine DID | `machine_did`, `max_transaction_value`, `max_daily_spend`, `allowed_operations`, `allowed_payment_protocols`, `allowed_chains` |
| `set_username` | Set a globally unique human-readable username for a DID | `did`, `username` |
| `resolve_username` | Resolve username to its DID | `username` |

### Payments (3 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `create_payment_challenge` | Create MPP, x402, or native payment challenge | `protocol`, `resource`, `amount`, `asset`, `recipient` |
| `verify_payment` | Verify payment credential and settle on-chain | `challenge_id`, `protocol`, `payer_did`, `payer_address`, `amount`, `asset`, `signature` |
| `list_payment_protocols` | List supported protocols (MPP, x402, native) | — |

### AI Models & Inference (10 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `list_models` | List available AI models with local/network/downloadable availability | `category`, `name` |
| `chat_completion` | Send chat completion request to a served model | `model`, `message`, `temperature`, `max_tokens` |
| `list_model_endpoints` | List model service endpoints with API/MCP URLs and status | — |
| `download_model` | Download a model from HuggingFace Hub with SHA-256 verification | `model_id` |
| `serve_model_mcp` | Start serving a downloaded model for inference | `model_id`, `max_concurrent` |
| `stop_model` | Stop serving a model (remains downloaded) | `model_id` |
| `delete_model_mcp` | Delete a downloaded model from local storage | `model_id` |
| `get_download_progress` | Check model download progress and ETA | `model_id` |
| `discover_models` | Discover models on the network by category, serving status, or max price | `category`, `serving_only`, `max_price_tnzo` |
| `list_providers` | List all discovered providers on the network (gossipsub) | `provider_type` |

### Cross-Chain Bridge (5 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `bridge_tokens` | Bridge tokens via LayerZero, CCIP, or deBridge | `source_chain`, `dest_chain`, `asset`, `amount`, `sender`, `recipient` |
| `bridge_quote` | Get a bridge quote without executing the transfer | `source_chain`, `dest_chain`, `asset`, `amount`, `sender` |
| `bridge_with_hook` | Bridge with deBridge post-fulfillment hook for composable cross-chain ops | `source_chain`, `dest_chain`, `asset`, `amount`, `sender`, `recipient`, `hook_target`, `hook_calldata` |
| `get_bridge_routes` | Get available routes between two chains with fees and timing | `source_chain`, `dest_chain` |
| `list_bridge_adapters` | List registered bridge adapters (LayerZero, CCIP, deBridge, Canton) | — |

### NFT Operations (6 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `create_nft_collection` | Create ERC-721 or ERC-1155 collection | `name`, `symbol`, `creator`, `standard` |
| `mint_nft` | Mint an NFT in a collection (unique for ERC-721, multi-copy for ERC-1155) | `collection_id`, `to`, `token_id`, `uri` |
| `transfer_nft` | Transfer NFT ownership within a collection | `collection_id`, `from`, `to`, `token_id` |
| `get_nft_info` | Query NFT collection or specific token info (owner, URI, supply) | `collection_id`, `token_id` |
| `list_nft_collections` | List all NFT collections, optionally filter by creator or standard | `creator`, `standard`, `limit` |
| `register_nft_pointer` | Register cross-VM NFT pointer (EVM/SVM/DAML) for discoverability | `collection_id`, `vm`, `address` |

### ERC-7802 Cross-Chain Tokens (3 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `crosschain_mint` | Mint tokens via authorized bridge (ERC-7802 crosschainMint) | `bridge`, `to`, `amount`, `sender` |
| `crosschain_burn` | Burn tokens for cross-chain transfer (ERC-7802 crosschainBurn) | `bridge`, `from`, `amount`, `destination` |
| `authorize_crosschain_bridge` | Authorize a bridge for crosschain mint/burn with daily rate limits | `bridge`, `name`, `daily_mint_limit`, `daily_burn_limit` |

### ERC-3643 Compliance (3 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `check_compliance` | Check if a transfer is compliant (KYC, accreditation, country, balance caps) | `token_id`, `from`, `to`, `amount` |
| `register_compliance` | Register compliance rules for a token (KYC, holder limits, country restrictions) | `token_id`, rules |
| `freeze_address` | Freeze an address for compliance (blocks send and receive) | `token_id`, `address`, `reason` |

### Events & Streaming (3 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `get_events` | Query historical events with cursor-based pagination | `filter`, `from_sequence`, `limit` |
| `subscribe_events` | Register event filter for real-time WebSocket/gRPC streaming | `filter` |
| `register_webhook` | Register webhook for event notifications (HMAC-SHA256 signed) | `url`, `filter`, `secret` |

### Staking & Providers (8 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `stake_tokens` | Stake TNZO as Validator, ModelProvider, TeeProvider, or StorageProvider | `amount`, `provider_type` |
| `unstake_tokens` | Unstake TNZO (begins 7-day unbonding) | `address` |
| `register_provider` | Register as a service provider with optional staking | `provider_type`, `name`, `stake`, `max_concurrent` |
| `get_provider_stats` | Get provider statistics (served models, inference count, earnings) | `address` |
| `set_provider_pricing` | Set inference pricing (price per 1k tokens, minimum charge) | `provider_address`, `price_per_1k_tokens`, `min_charge_tnzo` |
| `get_provider_pricing` | Get current pricing configuration | `provider_address` |
| `set_provider_schedule` | Set availability schedule (hours, days, timezone) | `provider_address`, `schedule` |
| `get_provider_schedule` | Get current availability schedule | `provider_address` |

### Governance (5 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `list_proposals` | List governance proposals by status | `status`, `limit`, `offset` |
| `create_proposal` | Create a new governance proposal | `title`, `description`, `proposal_type`, `proposer_address`, `payload` |
| `vote_on_proposal` | Vote on an active proposal (yes/no/abstain) | `proposal_id`, `vote`, `voter_address` |
| `get_voting_power` | Get voting power for an address (staked + delegated) | `address` |
| `delegate_voting_power` | Delegate voting power to another address | `from_address`, `to_address`, `amount_tnzo` |

### Token Registry (9 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `token_balance` | Get TNZO balance in atto-TNZO and human-readable decimal | `address` |
| `total_supply` | Get total TNZO supply (atto-TNZO and decimal) | — |
| `create_token` | Create a new ERC-20 token via the token factory | `name`, `symbol`, `creator`, `initial_supply`, `decimals` |
| `get_token_info` | Get token info by symbol, token ID, or EVM address | `query` |
| `list_tokens` | List all registered tokens, optionally filter by VM type or creator | `vm_type`, `creator` |
| `deploy_contract` | Deploy a smart contract (EVM bytecode, SVM BPF, or DAML DAR) | `vm_type`, `bytecode`, `deployer` |
| `cross_vm_transfer` | Transfer tokens atomically between VMs (e.g., EVM to SVM) | `from_vm`, `to_vm`, `address`, `amount` |
| `wrap_tnzo` | Wrap native TNZO to VM representation (no-op in pointer model) | `target_vm`, `address` |
| `get_token_balance` | Get TNZO balance across all VMs (native, EVM, SVM, DAML) | `address` |

### Task Marketplace (7 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `post_task` | Post a task to the marketplace | `title`, `description`, `task_type`, `poster_address`, `max_price_tnzo`, `input` |
| `list_tasks` | List tasks with filters (type, status, poster, price) | `task_type`, `status`, `poster`, `max_price_tnzo`, `limit` |
| `get_task` | Get details about a specific task | `task_id` |
| `quote_task` | Submit a price quote for a task | `task_id`, `provider_address`, `price_tnzo`, `model_id`, `estimated_secs` |
| `assign_task` | Assign a task to a specific agent | `task_id`, `agent_did` |
| `complete_task` | Mark a task as completed with result payload and optional proof | `task_id`, `agent_did`, `result`, `proof_hex` |
| `cancel_task` | Cancel a pending/active task (refunds escrowed TNZO) | `task_id`, `requester_address` |

### Agent Marketplace (9 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `register_agent_template` | Publish an agent template with system prompt, capabilities, pricing | `name`, `description`, `template_type`, `creator_address`, `system_prompt`, `tags`, `pricing` |
| `list_agent_templates` | Browse agent templates by type, tag, creator, or price | `template_type`, `tag`, `creator`, `free_only`, `limit` |
| `get_agent_template` | Get details about a specific agent template | `template_id` |
| `search_agent_templates` | Search templates by query (name, description, tags) | `query` |
| `get_agent_template_stats` | Get template stats (total spawns, average rating, rating count) | `template_id` |
| `spawn_agent_from_template` | Spawn an agent instance from a marketplace template | `template_id`, `name` |
| `download_agent_template` | Download and instantiate a template with config overrides | `template_id`, `controller_did`, `config_overrides` |
| `update_agent_template` | Update metadata for a template you own | `template_id`, fields |
| `rate_agent_template` | Rate a template 1-5 with optional review | `template_id`, `rating`, `review` |

### Agents & Swarms (9 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `register_agent` | Register an AI agent identity with auto-provisioned MPC wallet | `name`, `agent_type`, `controller_did`, `capabilities`, `endpoint` |
| `send_agent_message` | Send inter-agent message via A2A protocol | `from_did`, `to_did`, `message_type`, `payload` |
| `delegate_task` | Delegate a task from one agent to another with optional budget cap | `delegator_did`, `delegate_did`, `task`, `max_budget_tnzo` |
| `discover_agents` | Discover agents by capability or type | `capability`, `agent_type`, `limit` |
| `spawn_agent` | Spawn a child agent under a parent (max 50 children) | `parent_id`, `name`, `capabilities` |
| `run_agent_task` | Run an agentic task loop with built-in tools until completion | `agent_id`, `task`, `inference_url` |
| `create_swarm` | Create a coordinated multi-agent swarm | `orchestrator_id`, `members`, `max_members`, `task_timeout_secs`, `parallel` |
| `get_swarm_status` | Get swarm status including per-member agent statuses and results | `swarm_id` |
| `terminate_swarm` | Terminate a swarm and all its member agents | `swarm_id` |

### Settlement & Canton (8 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `settle_payment` | Execute immediate settlement between two addresses | `payer`, `payee`, `amount_tnzo`, `service_type`, `reference_id` |
| `create_escrow` | Create an escrow holding TNZO pending release conditions | `payer`, `payee`, `amount_tnzo`, `release_condition`, `timeout_secs` |
| `release_escrow` | Release escrowed funds with authorizing signature | `escrow_id`, `signer_address`, `signature_hex` |
| `open_payment_channel` | Open micropayment channel for off-chain per-token billing | `sender`, `recipient`, `deposit_tnzo` |
| `close_payment_channel` | Close payment channel with final balance and sender signature | `channel_id`, `final_balance_tnzo`, `sender_signature_hex` |
| `list_canton_domains` | List Canton synchronizer domains and connection status | — |
| `list_daml_contracts` | List active DAML contracts on a Canton domain | `domain_id`, `template_filter`, `limit` |
| `submit_daml_command` | Submit a DAML command (create, exercise, create_and_exercise) | `domain_id`, `party`, `command_type`, `template_id`, `arguments` |

### deBridge (5 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `debridge_search_tokens` | Search tokens on deBridge DLN | `query`, `chain_id` |
| `debridge_get_chains` | Supported chains for deBridge | — |
| `debridge_get_instructions` | deBridge operational guidance | `topic` |
| `debridge_create_tx` | Create cross-chain transaction via deBridge | `src_chain`, `dst_chain`, `token`, `amount`, `sender`, `recipient` |
| `debridge_same_chain_swap` | Same-chain swap via deBridge | `chain`, `token_in`, `token_out`, `amount`, `sender` |

### Crypto (9 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `sign_message` | Sign a message with Ed25519 or Secp256k1 private key | `message`, `private_key`, `key_type` |
| `verify_signature` | Verify a signature against a message and public key | `message`, `signature`, `public_key` |
| `encrypt_data` | Encrypt data with AES-256-GCM | `plaintext`, `key` |
| `decrypt_data` | Decrypt AES-256-GCM ciphertext | `ciphertext`, `key`, `nonce` |
| `derive_key` | Derive a key using HKDF-SHA256 | `input_key`, `salt`, `info` |
| `generate_keypair` | Generate Ed25519 or Secp256k1 keypair | `key_type` |
| `hash_sha256` | Compute SHA-256 hash of data | `data` |
| `hash_keccak256` | Compute Keccak-256 hash of data | `data` |
| `x25519_key_exchange` | Perform X25519 Diffie-Hellman key exchange | `private_key`, `peer_public_key` |

### TEE (6 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `detect_tee` | Detect available TEE hardware (TDX, SEV-SNP, Nitro, NVIDIA GPU) | — |
| `get_tee_attestation` | Request a TEE attestation report from local hardware | `user_data` |
| `verify_tee_attestation` | Verify a TEE attestation report and certificate chain | `attestation`, `provider` |
| `seal_data` | Seal data inside a TEE enclave (AES-256-GCM, hardware-bound key) | `data`, `key_id` |
| `unseal_data` | Unseal data previously sealed inside a TEE enclave | `sealed_data`, `key_id` |
| `list_tee_providers` | List registered TEE providers and their attestation status | — |

### ZK Proofs (3 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `create_zk_proof` | Generate a Groth16 ZK proof for a circuit (inference, settlement, identity) | `circuit`, `private_inputs`, `public_inputs` |
| `generate_proving_key` | Generate proving and verification keys for a circuit | `circuit` |
| `list_zk_circuits` | List available ZK circuits and their parameters | — |

### Key Custody (9 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `create_mpc_wallet` | Create a 2-of-3 MPC threshold wallet with auto key shares | `key_type` |
| `export_keystore` | Export encrypted keystore (Argon2id KDF) | `address`, `password` |
| `import_keystore` | Import wallet from encrypted keystore | `keystore_json`, `password` |
| `get_key_shares` | Get MPC key share metadata (not secret material) | `address` |
| `rotate_keys` | Rotate MPC key shares for a wallet | `address` |
| `set_spending_limits` | Set per-transaction and daily spending limits | `address`, `per_tx_limit`, `daily_limit` |
| `get_spending_limits` | Get current spending limits for a wallet | `address` |
| `authorize_session` | Create a time-bound session key with restricted permissions | `address`, `duration_secs`, `allowed_operations` |
| `revoke_session` | Revoke an active session key | `session_id` |

### App/Paymaster (6 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `register_app` | Register an application for managed wallet and paymaster services | `name`, `owner_address`, `callback_url` |
| `create_user_wallet` | Create a managed wallet for an app user (ERC-4337 smart account) | `app_id`, `user_id` |
| `fund_user_wallet` | Fund a managed user wallet from app treasury | `app_id`, `user_id`, `amount_tnzo` |
| `list_user_wallets` | List managed wallets for an app | `app_id`, `limit` |
| `sponsor_transaction` | Sponsor gas for a user transaction via paymaster | `app_id`, `user_operation` |
| `get_usage_stats` | Get app usage statistics (transactions, gas sponsored, users) | `app_id` |

### Contract ABI (2 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `encode_function` | ABI-encode a function call for EVM contract interaction | `function_signature`, `arguments` |
| `decode_result` | ABI-decode a return value from an EVM contract call | `function_signature`, `data` |

### Streaming (2 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `chat_stream` | Stream chat completion tokens via SSE from a served model | `model`, `message`, `temperature`, `max_tokens` |
| `subscribe_events_stream` | Subscribe to real-time blockchain events via SSE | `filter` |

### Verification & Onboarding (4 tools)

| Tool | Description | Key Parameters |
|------|-------------|----------------|
| `verify_zk_proof` | Verify Groth16, PlonK, or STARK proof with public inputs | `proof`, `proof_type`, `public_inputs` |
| `join_as_participant` | Join the network as a zero-install MicroNode participant | `display_name`, `origin`, `participant_type` |
| `get_skill_usage` | Get usage statistics for a registered skill | `skill_id` |
| `get_tool_usage` | Get usage statistics for a registered tool | `tool_id` |

## Ecosystem MCP Servers

In addition to the main Tenzro MCP server, the node runs specialized servers for direct blockchain interaction:

| Server | Port | Endpoint | Description |
|--------|------|----------|-------------|
| **Tenzro** | 3001 | `/mcp` | 146 tools for Tenzro Ledger operations |
| **Solana** | 3003 | `/mcp` | Jupiter swaps, SPL tokens, Metaplex NFTs, staking |
| **Ethereum** | 3004 | `/mcp` | Gas prices, ENS, ERC-20, EAS attestations, ERC-8004 |
| **Canton** | 3005 | `/mcp` | DAML contracts, CIP-56 tokens, DvP settlement |
| **LayerZero** | 3006 | `/mcp` | V2 messaging, OFT transfers, DVN configuration |
| **Chainlink** | 3007 | `/mcp` | CCIP, data feeds, VRF, automation, Functions |
| **LI.FI** | 3008 | `/mcp` | Cross-chain aggregator, 66 chains, quotes, routes, swaps |

## Authentication

The MCP server supports **OAuth 2.1** with PKCE (S256) for authenticated access:

- **Discovery:** `GET /.well-known/oauth-authorization-server`
- **Registration:** `POST /register` (dynamic client registration, RFC 7591)
- **Authorization:** `GET /authorize` (PKCE flow with user consent UI)
- **Token:** `POST /token` (JWT access tokens with TDIP DID claims)

Unauthenticated access is available for read-only tools on the testnet.

## Programmatic Usage

### TypeScript

```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { StreamableHTTPClientTransport } from "@modelcontextprotocol/sdk/client/streamableHttp.js";

const transport = new StreamableHTTPClientTransport(
  new URL("https://mcp.tenzro.network/mcp")
);
const client = new Client({ name: "my-app", version: "1.0.0" }, {});
await client.connect(transport);

// List all tools
const tools = await client.listTools();
console.log(`${tools.tools.length} tools available`);

// Check balance
const balance = await client.callTool({
  name: "get_balance",
  arguments: { address: "0x742d35Cc6634C0532925a3b844Bc9e7595f2bD68" },
});

// Mint an NFT
const nft = await client.callTool({
  name: "mint_nft",
  arguments: {
    collection_id: "0xabc...",
    to: "0x742d...",
    token_id: 1,
    uri: "ipfs://Qm...",
  },
});

// Bridge tokens via LI.FI (58+ chains)
const bridge = await client.callTool({
  name: "bridge_quote",
  arguments: {
    from_chain: "ethereum",
    to_chain: "arbitrum",
    token: "USDC",
    amount: "1000000000",
  },
});

// Subscribe to events
const sub = await client.callTool({
  name: "subscribe_events",
  arguments: {
    filter: { event_types: ["Transfer", "Log"] },
  },
});
```

### Python

```python
from mcp import ClientSession
from mcp.client.streamable_http import streamablehttp_client

async with streamablehttp_client("https://mcp.tenzro.network/mcp") as (read, write):
    async with ClientSession(read, write) as session:
        await session.initialize()

        # List tools
        tools = await session.list_tools()
        print(f"{len(tools.tools)} tools available")

        # Check compliance before transfer
        compliance = await session.call_tool(
            "check_compliance",
            arguments={
                "token_id": "0xtoken...",
                "from": "0xsender...",
                "to": "0xrecipient...",
                "amount": "1000000",
            },
        )

        # Register a webhook
        webhook = await session.call_tool(
            "register_webhook",
            arguments={
                "url": "https://myapp.com/webhook",
                "filter": {"event_types": ["Transfer"]},
                "secret": "my-hmac-secret-key-here",
            },
        )
```

### curl

```bash
# Initialize
curl -s -X POST https://mcp.tenzro.network/mcp \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"curl","version":"1.0"}}}'

# List tools
curl -s -X POST https://mcp.tenzro.network/mcp \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"jsonrpc":"2.0","id":2,"method":"tools/list","params":{}}'

# Call a tool
curl -s -X POST https://mcp.tenzro.network/mcp \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"jsonrpc":"2.0","id":3,"method":"tools/call","params":{"name":"get_node_status","arguments":{}}}'
```

## Architecture

```
AI Agent (Claude, GPT, Cursor, etc.)
    |
    |  Streamable HTTP (POST /mcp)
    v
Tenzro MCP Server (port 3001)
    |
    +-- Wallet & Ledger ----------> Ed25519/Secp256k1 keys, TNZO transfers
    +-- Identity (TDIP) ----------> DID registration, delegation, usernames
    +-- Payments (MPP/x402) ------> HTTP 402 payment challenges, Stripe/Coinbase
    +-- AI Models (10 tools) -----> HuggingFace download, inference, provider discovery
    +-- NFTs (ERC-721/1155) ------> Collections, minting, cross-VM pointers
    +-- Bridge (LZ/CCIP/deBridge)-> Quotes, hooks, multi-chain transfers
    +-- Compliance (ERC-3643) ----> KYC checks, freeze, compliance rules
    +-- Cross-Chain (ERC-7802) ---> Authorized mint/burn with rate limits
    +-- Events & Webhooks --------> Real-time streaming, HMAC-signed callbacks
    +-- Staking & Providers ------> Validator/provider staking, pricing, scheduling
    +-- Governance (5 tools) -----> Proposals, voting, delegation
    +-- Token Registry (8 tools) -> ERC-20 factory, cross-VM, contract deployment
    +-- Task Marketplace ---------> Post, quote, assign, complete tasks
    +-- Agent Marketplace --------> Templates, search, rate, stats, spawn
    +-- Agents & Swarms ----------> Registration, A2A messaging, swarm orchestration
    +-- Settlement & Canton ------> Escrow, micropayments, DAML contracts
    +-- Crypto (9 tools) ---------> Sign, verify, encrypt, decrypt, hash, key exchange
    +-- TEE (6 tools) -----------> Hardware attestation, seal/unseal, provider listing
    +-- ZK Proofs (3 tools) -----> Proof generation, proving keys, circuit listing
    +-- Key Custody (9 tools) ---> MPC wallets, keystores, key rotation, sessions
    +-- App/Paymaster (6 tools) -> App registration, managed wallets, gas sponsorship
    +-- Contract ABI (2 tools) --> ABI encode/decode for EVM contracts
    +-- Streaming (2 tools) -----> SSE chat streaming, event streaming
    +-- Verification & Onboarding > ZK proofs, MicroNode join, skill/tool usage
    |
    v
Tenzro Ledger (HotStuff-2 BFT, EVM+SVM+DAML)
```

## Running the Server

### stdio (Claude Desktop, Cursor)

```bash
tenzro-mcp-server
```

### Streamable HTTP

```bash
tenzro-mcp-server --transport http --port 3001
```

### Test the server

```bash
curl -s -X POST http://localhost:3001/mcp \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"test","version":"1.0"}}}'
```

## Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `TENZRO_RPC_URL` | `https://rpc.tenzro.network` | Tenzro JSON-RPC endpoint |
| `TENZRO_API_URL` | `https://api.tenzro.network` | Tenzro Web API endpoint |

Command-line options:

| Flag | Default | Description |
|------|---------|-------------|
| `--transport` | `stdio` | Transport type (`stdio` or `http`) |
| `--port` | `3001` | HTTP server port (when using `http` transport) |
| `--host` | `0.0.0.0` | HTTP server bind address |

## Protocol Details

- **MCP Version:** 2024-11-05
- **Transport:** Streamable HTTP (stateless JSON mode)
- **Content Types:** `application/json`, `text/event-stream`
- **Authentication:** OAuth 2.1 with PKCE S256 (optional on testnet)
- **Framework:** [rmcp](https://crates.io/crates/rmcp) 1.2

## Related

| Resource | URL |
|----------|-----|
| Tenzro Network | [tenzro.com](https://tenzro.com) |
| A2A Server | [github.com/tenzro/tenzro-a2a-server](https://github.com/tenzro/tenzro-a2a-server) |
| TenzroClaw | [github.com/tenzro/TenzroClaw](https://github.com/tenzro/TenzroClaw) |
| LI.FI MCP | Cross-chain bridge aggregation (66 chains) |
| deBridge MCP | [agents.debridge.com/mcp](https://agents.debridge.com/mcp) |
| 1inch MCP | [api.1inch.com/mcp/protocol](https://api.1inch.com/mcp/protocol) |
| MCP Specification | [modelcontextprotocol.io](https://modelcontextprotocol.io) |

## Contact

- Website: [tenzro.com](https://tenzro.com)
- Engineering: [eng@tenzro.com](mailto:eng@tenzro.com)
- GitHub: [github.com/tenzro](https://github.com/tenzro)

## License

Apache 2.0. See [LICENSE](LICENSE).
