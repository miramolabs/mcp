<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/miramo-logo-cream.svg">
  <img alt="Miramo" src="assets/miramo-logo-green.svg" width="220">
</picture>

# Miramo MCP server

**En français** : [documentation du serveur sur miramo.fr](https://www.miramo.fr/outils/mcp/developpeurs)

French rental law and calculators for tenants and landlords: rent caps, notices, deposits, leases.

Miramo is a hosted, read-only MCP server. It answers rental questions in France from the official texts: the prefectoral orders that cap rents, the official lists of tense and ABC zones, the airport noise maps of the French civil aviation authority, the official lease model, the 1989 rental law, the civil and tax codes, and the Banque de France average loan rate. It also finds the articles of Miramo's blog that answer a question. It is free, needs no account or API key, and has nothing to install: you add one address to your agent.

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
- **Lease draft**: a link to Miramo's lease generator (unfurnished, furnished or mobility lease), filled in with the dwelling, the rent and the dates given. Nothing is stored: the draft travels in the link.
- **Notice and deposit**: the notice of a tenant or a landlord, the deposit cap, the deadline and penalty to give it back, the wear and tear deducted, the documents to join to a lease.
- **Rent and charges**: the rent of an incomplete month, the rent an income allows, the rent revision freeze of F and G dwellings, the recoverable charges, the yearly regularization, the waste collection tax.
- **Investing and managing**: rental yield, micro-foncier or réel, loan payments, borrowing capacity, self-management or an agency.
- **Blog**: the articles of Miramo's blog that answer a question, with their publication day.

Every answer gives its source, the date it came into force and its end date when there is one. A calculation also lists the rules it applied with the day Miramo checked them in the official text, the figures Miramo assumed (each with the input that replaces it), and the caveats to tell the person.

## Tools

All 23 tools are read-only. Their texts are in French, and each answer also comes as structured content.

### Rent

| Tool | Input | Output |
| --- | --- | --- |
| `lookup_reference_rent` | `address`, `rooms`, `furnished`, `constructionPeriod`, `surface` | The reference rent, its upper and lower limits, the monthly cap, the order in force and its dates. |
| `check_rent_control_perimeter` | `address`, `commune`, `inseeCode` | Whether rents are capped there (`full`, `partial`, `none`), the territory and its order. |
| `calculate_prorated_rent` | `move`, `leaseType`, `monthlyRent`, `monthlyCharges`, `leaseStartDate`, `paymentTerm`, `exitReason`, `noticeEndDate`, `relocationMoveInDate`, `keyHandoverDate`, `leaseEndDate` | The rent and charges owed for the incomplete month of a move in or out. |
| `check_rent_revision_freeze` | `dpeClass`, `leaseType`, `leaseEffectiveDate`, `address`, `commune`, `inseeCode` | Whether the rent of a dwelling rated F or G can still be revised, and since when. |
| `calculate_affordable_rent` | `householdMonthlyNetIncome`, `plannedRentIncludingCharges`, `incomeMultiplier` | The highest rent an income allows and the effort rate of a planned rent. |

### Lease, notice and deposit

| Tool | Input | Output |
| --- | --- | --- |
| `prepare_lease_draft` | `leaseType`, `annexes`, `dates`, `rent`, `property` | A link to the lease generator on miramo.fr (unfurnished, furnished or mobility lease), filled in with the draft. |
| `calculate_notice_period` | `party`, `leaseType`, `hasReducedNoticeReason`, `address`, `commune`, `inseeCode`, `receptionDate` | The notice of a tenant or a landlord on the three leases, and its end date for a tenant. |
| `calculate_deposit_return` | `leaseType`, `keyHandoverDate`, `exitInventory`, `monthlyRentExcludingCharges`, `depositPaid`, `hasLandlordNewAddress`, `justifiedRetentions`, `isCollectiveBuilding`, `condominiumProvision`, `asOfDate` | The deadline to give the deposit back, the sum due and the late penalty. |
| `calculate_wear_and_tear` | `items`, `depositPaid` | The tenant's share of each damage once wear and tear is deducted, and the deposit balance. |
| `list_required_lease_annexes` | `leaseType`, `isBuiltBefore1949`, `isInCoOwnership`, `isGasInstallationOver15Years`, `isElectricalInstallationOver15Years`, `isInRiskZone`, `isInAirportNoiseZone` | The diagnostics and documents to join to the lease, each with its reason. |
| `calculate_deposit_cap` | `leaseType`, `monthlyRentExcludingCharges`, `isRentPaidInAdvanceOverTwoMonths`, `requestedDeposit` | The highest deposit a landlord may ask, and the excess of a deposit asked. |

### The dwelling and its address

| Tool | Input | Output |
| --- | --- | --- |
| `check_tense_zone` | `address`, `commune`, `inseeCode` | The category (`1`, `2`, `none`) and its effects, with the official list and its last update. |
| `check_airport_noise_zone` | `address` | The noise zone (`A` to `D`), the airport and its plan, and whether a noise report is required. |
| `check_abc_zone` | `address`, `commune`, `inseeCode` | The zone (`Abis`, `A`, `B1`, `B2`, `C`), the schemes it opens and the Loc'Avantages ceilings. |

### Charges

| Tool | Input | Output |
| --- | --- | --- |
| `calculate_recoverable_charges` | `chargesMode`, `charges`, `occupancyStartDate`, `occupancyEndDate` | The charges a landlord may recover from the tenant, line by line, prorated to the occupancy. |
| `calculate_charges_regularization` | `chargesMode`, `periodStartDate`, `periodEndDate`, `monthlyProvision`, `actualCharges`, `regularizationDate` | The balance of the yearly charges regularization, and who owes it. |
| `calculate_waste_tax_share` | `chargesMode`, `taxYear`, `taxAmount`, `moveInDate`, `moveOutDate` | The tenant's share of the waste collection tax, prorated by the day. |

### Investing and managing

| Tool | Input | Output |
| --- | --- | --- |
| `calculate_rental_yield` | `purchasePrice`, `monthlyRentExcludingCharges`, `notaryFees`, `works`, `agencyFees`, `furniture`, `vacancyMonthsPerYear`, `nonRecoverableChargesYearly`, `propertyTaxYearly`, `landlordInsuranceYearly`, `managementFeesPct`, `maintenanceProvisionPct`, `downPayment`, `loanRatePct`, `loanYears`, `loanInsuranceRatePct`, `bankFees`, `holdingYears`, `appreciationPctPerYear`, `resaleFeesPct` | The gross and net yield, the monthly cash flow and the internal rate of return of a rental purchase. |
| `compare_property_income_tax_regimes` | `leaseType`, `annualRentExcludingCharges`, `dwellingCount`, `marginalTaxRatePct`, `loanInterest`, `works`, `energyWorks`, `worksRecurrence`, `propertyTax`, `insurance`, `managementFees`, `condominiumCharges`, `otherCharges`, `priorDeficits`, `currentRegime` | The tax and social charges under the micro-foncier and the réel, and the regime that costs less. |
| `calculate_loan_payment` | `principal`, `years`, `annualRatePct`, `insuranceAnnualRatePct` | The monthly payment, the insurance, and the total interest and cost of a loan. |
| `calculate_borrowing_capacity` | `netMonthlyIncome`, `futureMonthlyRent`, `currentLoanPayments`, `existingMonthlyRents`, `downPayment`, `insuranceAnnualRatePct`, `dwellingKind`, `years`, `annualRatePct`, `rentSharePct`, `purchaseFeeRatePct` | What a bank could lend for a dwelling bought to let, and the price it reaches. |
| `compare_self_management_and_agency` | `monthlyRentExcludingCharges`, `monthlyCharges`, `yearsBetweenTenants`, `leaseType`, `taxRegime`, `marginalTaxRatePct`, `agency`, `direct` | The net cost of self-management and of an agency over 1, 5 and 10 years. |

### Guides

| Tool | Input | Output |
| --- | --- | --- |
| `search_blog_articles` | `query` | Up to ten articles of Miramo's blog on the question, with their publication day and link. |

19 prompts, for the agents that list them as shortcuts, each writing a question for one tool:

- `prepare_lease` (Préparer un bail)
- `calculate_waste_tax_share` (Calculer la taxe des ordures)
- `check_rent` (Vérifier un loyer)
- `calculate_notice_period` (Calculer un préavis)
- `calculate_wear_and_tear` (Calculer la retenue après vétusté)
- `calculate_recoverable_charges` (Calculer les charges récupérables)
- `calculate_rental_yield` (Calculer la rentabilité d'un achat)
- `calculate_charges_regularization` (Régulariser les charges)
- `list_required_lease_annexes` (Lister les annexes du bail)
- `check_rent_revision_freeze` (Vérifier le gel du loyer (DPE F ou G))
- `calculate_prorated_rent` (Calculer le loyer d'un mois incomplet)
- `calculate_affordable_rent` (Calculer le loyer selon ses revenus)
- `calculate_deposit_cap` (Calculer le dépôt maximum)
- `calculate_deposit_return` (Calculer le délai pour rendre le dépôt)
- `calculate_loan_payment` (Calculer les mensualités d'un prêt)
- `calculate_borrowing_capacity` (Calculer sa capacité d'emprunt)
- `compare_property_income_tax_regimes` (Comparer micro-foncier et réel)
- `compare_self_management_and_agency` (Comparer gestion directe et agence)
- `search_blog_articles` (Chercher un article du blog)

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
- The lease tool prepares a draft and nothing more, and every calculation is an estimate from the figures given: the server signs nothing, sends nothing and touches no account.
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
