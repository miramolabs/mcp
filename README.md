# Miramo MCP server

French rental law for tenants and landlords: rent caps, rental zones and unfurnished lease drafts.

Miramo is a hosted, read-only MCP server. It answers rental questions in France from the official texts: the prefectoral orders that cap rents, the official lists of tense and ABC zones, the airport noise maps of the French civil aviation authority, and the official unfurnished lease model. It is free, needs no account or API key, and has nothing to install: you add one address to your agent.

```
https://mcp.miramo.fr/mcp
```

Transport: Streamable HTTP. Authentication: none.

## What it does

- **Rent caps**: the reference rent at an address, with its upper and lower limits and the monthly cap for a given surface, under the prefectoral order in force today.
- **Rent control**: whether rents are capped at an address (Paris, Lyon, Lille, Bordeaux, Montpellier, Grenoble, the Basque Country, Plaine Commune, Est Ensemble), in the whole town or in part.
- **Tense zone**: a town's category and what it changes: the tenant's notice, the rent rise between two tenants, the vacancy tax.
- **ABC zone**: a town's zone and what it changes for the investment schemes: Loc'Avantages and its rent ceilings, intermediate housing, the zero-rate loan.
- **Airport noise zone**: whether an address sits in an airport's noise exposure plan, and so whether a noise report goes with the lease.
- **Unfurnished lease draft**: a link to Miramo's lease generator, filled in with the dwelling, the rent and the dates given. Nothing is stored: the draft travels in the link.

Every answer gives its source, the date it came into force and its end date when there is one.

## Tools

All six tools are read-only. Their texts are in French, and each answer also comes as structured content.

| Tool                           | Input                                                         | Output                                                                                         |
| ------------------------------ | ------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| `lookup_reference_rent`        | `address`, `rooms`, `furnished`, `constructionPeriod`, `surface` | The reference rent, its upper and lower limits, the monthly cap, the order in force and its dates. |
| `check_rent_control_perimeter` | `address`, `commune` or `inseeCode`                           | Whether rents are capped there (`full`, `partial`, `none`), the territory and its order.       |
| `check_tense_zone`             | `address`, `commune` or `inseeCode`                           | The category (`1`, `2`, `none`) and its effects, with the official list and its last update.   |
| `check_abc_zone`               | `address`, `commune` or `inseeCode`                           | The zone (`Abis`, `A`, `B1`, `B2`, `C`), the schemes it opens and the Loc'Avantages ceilings.  |
| `check_airport_noise_zone`     | `address`                                                     | The noise zone (`A` to `D`), the airport and its plan, and whether a noise report is required. |
| `prepare_lease_draft`          | `property`, `rent`, `dates`, `annexes`, all optional          | A link to the lease generator on miramo.fr, filled in with the draft.                          |

Two prompts, for the agents that list them as shortcuts: `prepare_lease` (prepare a lease) and `check_rent` (check a rent).

## Add it to your agent

### VS Code (GitHub Copilot)

[Install in VS Code](https://vscode.dev/redirect/mcp/install?name=miramo&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fmcp.miramo.fr%2Fmcp%22%7D), or add this to `.vscode/mcp.json`:

```json
{
  "servers": {
    "miramo": {
      "type": "http",
      "url": "https://mcp.miramo.fr/mcp"
    }
  }
}
```

### Claude

On claude.ai and Claude Desktop, open the [Miramo listing](https://claude.ai/directory/miramo) in Claude's connectors directory and connect it.

In Claude Code:

```sh
claude mcp add --transport http miramo https://mcp.miramo.fr/mcp
```

Add `--scope user` to have it in every project.

### Cursor

[Install in Cursor](https://cursor.com/install-mcp?name=miramo&config=eyJ1cmwiOiJodHRwczovL21jcC5taXJhbW8uZnIvbWNwIn0=), or add this to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "miramo": {
      "url": "https://mcp.miramo.fr/mcp"
    }
  }
}
```

### Codex

```sh
codex mcp add miramo --url https://mcp.miramo.fr/mcp
```

### ChatGPT

On paid plans, on the web: turn on developer mode in ChatGPT's settings, then create an app with the address `https://mcp.miramo.fr/mcp`.

### Any other agent

Add a remote MCP server at `https://mcp.miramo.fr/mcp`, over Streamable HTTP, with no authentication. Most agents read the same configuration as Cursor.

## Try it

- "What is the maximum rent for an unfurnished two-room flat of 45 m² at 14 rue Oberkampf in Paris, built in 1975?"
- "Is Montreuil (93) in a tense zone? What does it change for my tenant's notice?"
- "Prepare an unfurnished lease for that flat, at 1,200 € a month plus 80 € of charges, starting on 1 December."

## Limits

- France only. Where rents are capped but Miramo holds no official rent table (Grenoble, the Basque Country), the server names the order and gives no figure. For Lille, Bordeaux and Montpellier, `isStale` flags a rent table older than the order in force.
- It gives general information from the official texts, not legal advice.
- The lease tool prepares a draft and nothing more: the server signs nothing, sends nothing and touches no account.
- Up to 60 tool calls a minute per IP address. Past that, the server answers `429` with `Retry-After`.

## Privacy

The server reads and never writes. No tool asks for a name, an email address or a phone number, and the server does not keep what it receives. Details, in French: section 3.7 of [Miramo's privacy policy](https://www.miramo.fr/politique-de-confidentialite#3-7).

## Links

- Documentation, in French: https://www.miramo.fr/outils/mcp/developpeurs
- Official MCP Registry: `fr.miramo/mcp`
- Claude connectors directory: https://claude.ai/directory/miramo
- Website: https://www.miramo.fr
- Contact: contact@miramo.fr

## About this repository

The server's source code is not published. This repository describes the hosted server for the MCP Registry and GitHub's MCP catalogue.
