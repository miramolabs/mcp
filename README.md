<picture>
  <source media="(prefers-color-scheme: dark)" srcset="assets/miramo-logo-cream.svg">
  <img alt="Miramo" src="assets/miramo-logo-green.svg" width="220">
</picture>

# Miramo MCP server

**En français** : [documentation du serveur sur miramo.fr](https://www.miramo.fr/outils/mcp/developpeurs)

French rental law and calculators for tenants and landlords: rent caps, notices, deposits, leases.

Miramo is a hosted, read-only MCP server. It answers rental questions in France from the official texts: the prefectoral orders that cap rents, the official lists of tense and ABC zones, the airport noise maps of the French civil aviation authority, the official lease model, the 1989 rental law, the decency decree and its energy performance calendar, the civil and tax codes, Action Logement's Visale rules, and the Banque de France average loan rate. It also finds the articles of Miramo's blog that answer a question. It is free, needs no account or API key, and has nothing to install: you add one address to your agent.

```
https://mcp.miramo.fr/mcp
```

Transport: Streamable HTTP. Authentication: none.

## What it does

- **Rent caps**: the reference rent at an address, with its upper and lower limits and, while a prefectoral order is in force, the monthly cap for a given surface.
- **Rent control**: whether rents are capped at an address (Paris, Lyon, Lille, Bordeaux, Montpellier, Grenoble, the Basque Country, Plaine Commune, Est Ensemble), in the whole town or in part.
- **Tense zone**: a town's category and what it changes: the tenant's notice, the rent rise between two tenants, the vacancy tax.
- **ABC zone**: a town's zone and what it changes for the investment schemes: Loc'Avantages and its rent ceilings, intermediate housing, the zero-rate loan.
- **Energy performance**: from when a dwelling of a given DPE class no longer reaches the energy performance of a decent dwelling, in mainland France and the overseas departments.
- **Living area**: the living area of a dwelling room by room, its Carrez area in a co-ownership, and the gap with the area stated in the lease.
- **Airport noise zone**: whether an address sits in an airport's noise exposure plan, and so whether a noise report goes with the lease.
- **Lease draft**: a link to Miramo's lease generator (unfurnished, furnished or mobility lease), filled in with the dwelling, the rent and the dates given. Nothing is stored: the draft travels in the link.
- **Notice and deposit**: the notice of a tenant or a landlord, the deposit cap, the deadline and penalty to give it back, the wear and tear deducted, the documents to join to a lease, whether an applicant can get Visale or a rent-guarantee insurance.
- **Rent and charges**: the highest rent between two tenants, the rent of an incomplete month, the rent an income allows, the rent revision freeze of F and G dwellings, the recoverable charges, the yearly regularization, the waste collection tax.
- **Rent index and revision**: the rent reference index (IRL) of a quarter, and how much a rent may rise at its yearly revision.
- **Advertised rent**: the estimated advertised rent of a typical dwelling in a town.
- **Housing benefit**: an estimate of the monthly housing benefit (APL, ALS or ALF) for a rent and a household, computed with OpenFisca on Miramo's own server.
- **Tenant file**: the documents a landlord may ask an applicant and a guarantor for, from the decree's list.
- **The address**: the urban plan zone at an address, whether it sits in a principal residence sector, the housing contacts of the town and its département, the local tax rates of the town.
- **DPE lookup**: what ADEME publishes under a DPE number.
- **Investing and managing**: rental yield, micro-foncier or réel, loan payments, borrowing capacity, self-management or an agency.
- **Blog**: the articles of Miramo's blog that answer a question, with their publication day.

Every answer gives its source, the date it came into force and its end date when there is one. A calculation also lists the rules it applied with the day Miramo checked them in the official text, the figures Miramo assumed (each with the input that replaces it), and the caveats to tell the person.

## Tools

All 37 tools are read-only. Their texts are in French, and each answer also comes as structured content.

### Rent

| Tool | Input | Output |
| --- | --- | --- |
| `lookup_reference_rent` | `address`, `rooms`, `furnished`, `constructionPeriod`, `propertyType`, `surface` | The reference rent and its upper and lower limits; while an order is in force, the monthly cap and the order with its dates; the rents of both zones (`boundaryZones`) for an address on the line between two zones. |
| `check_rent_control_perimeter` | `address`, `commune`, `inseeCode` | Whether rents are capped there (`full`, `partial`, `none`), the territory and the order in force today when there is one, or `not_settled` where Miramo does not settle it. |
| `calculate_prorated_rent` | `move`, `leaseType`, `monthlyRent`, `monthlyCharges`, `leaseStartDate`, `paymentTerm`, `exitReason`, `noticeEndDate`, `relocationMoveInDate`, `keyHandoverDate`, `leaseEndDate` | The rent and charges owed for the incomplete month of a move in or out. |
| `check_rent_revision_freeze` | `dpeClass`, `leaseType`, `leaseEffectiveDate`, `address`, `commune`, `inseeCode` | Whether the rent of a dwelling rated F or G can still be revised, and since when. |
| `calculate_reletting_rent_cap` | `leaseType`, `address`, `commune`, `inseeCode`, `dpeClass`, `hasTenantLeftWithin18Months`, `previousRent`, `previousRentLastRevisedOn`, `plannedRent`, `works`, `plannedSignatureOn` | The highest rent of a new lease when the dwelling changes tenant, the reason when there is no cap, and the excess of a planned rent. |
| `calculate_affordable_rent` | `householdMonthlyNetIncome`, `plannedRentIncludingCharges`, `incomeMultiplier` | The highest rent an income allows and the effort rate of a planned rent. |
| `lookup_irl` | `year`, `quarter` | The rent reference index of a quarter, or the last one published, for mainland France, Corsica and the overseas départements. |
| `calculate_rent_revision` | `leaseType`, `hasRevisionClause`, `monthlyRentExcludingCharges`, `leaseSignedOn`, `revisionDate`, `address`, `commune`, `inseeCode`, `referenceQuarter`, `landlordRequestDate`, `dpeClass`, `leaseEffectiveDate`, `housingRegime` | The revised rent, the index pair used, and the reason when the rent may not rise. |
| `lookup_advertised_rent` | `address`, `commune`, `inseeCode` | The estimated advertised rent per m² of a typical dwelling in the town, from the State's rent map. |
| `calculate_housing_benefit` | `address`, `commune`, `inseeCode`, `monthlyRent`, `isCouple`, `incomes`, `partnerIncomes`, `dependentChildren`, `isSocialHousing`, `isFlatshare`, `isFurnished`, `studentStatus`, `isRoom`, `alimonyPaidLastYear`, `alimonyReceivedLastYear` | An estimate of the monthly housing benefit, the benefit's kind and zone, or `not_settled` where the estimate does not apply. |

### Lease, notice and deposit

| Tool | Input | Output |
| --- | --- | --- |
| `prepare_lease_draft` | `leaseType`, `annexes`, `dates`, `rent`, `property` | A link to the lease generator on miramo.fr (unfurnished, furnished or mobility lease), filled in with the draft. |
| `calculate_notice_period` | `party`, `leaseType`, `hasReducedNoticeReason`, `address`, `commune`, `inseeCode`, `receptionDate` | The notice of a tenant or a landlord on the three leases, and its end date for a tenant, or `not_settled` where Miramo does not settle it. |
| `calculate_deposit_return` | `leaseType`, `keyHandoverDate`, `exitInventory`, `monthlyRentExcludingCharges`, `depositPaid`, `hasLandlordNewAddress`, `justifiedRetentions`, `isCollectiveBuilding`, `condominiumProvision`, `asOfDate` | The deadline to give the deposit back, the sum due and the late penalty. |
| `calculate_wear_and_tear` | `items`, `depositPaid` | The tenant's share of each damage once wear and tear is deducted, and the deposit balance. |
| `check_rent_guarantee_eligibility` | `address`, `commune`, `inseeCode`, `rentIncludingCharges`, `leaseType`, `applicants`, `gliIncomeMultiplier` | Whether one or two applicants can get Visale or a rent-guarantee insurance, with the reason for each. |
| `list_required_lease_annexes` | `leaseType`, `isBuiltBefore1949`, `isInCoOwnership`, `isGasInstallationOver15Years`, `isElectricalInstallationOver15Years`, `isInRiskZone`, `isInAirportNoiseZone` | The diagnostics and documents to join to the lease, each with its reason. |
| `calculate_deposit_cap` | `leaseType`, `monthlyRentExcludingCharges`, `isRentPaidInAdvanceOverTwoMonths`, `requestedDeposit` | The highest deposit a landlord may ask, and the excess of a deposit asked. |
| `list_tenant_file_documents` | `activities`, `guarantorKind` | The documents a landlord may ask an applicant and a guarantor for, from the decree's list. |
| `lookup_housing_contacts` | `address`, `commune`, `inseeCode` | The housing contacts of the town and its département, from service-public.fr's directory. |

### The dwelling and its address

| Tool | Input | Output |
| --- | --- | --- |
| `check_dpe_rental_ban` | `dpeClass`, `address`, `commune`, `inseeCode` | The day a dwelling of that DPE class no longer reaches the energy performance of a decent dwelling, and whether that day has passed. |
| `check_tense_zone` | `address`, `commune`, `inseeCode` | The category (`1`, `2`, `none`) and its effects, with the official list and its last update, or `not_settled` where Miramo does not settle it. |
| `check_airport_noise_zone` | `address` | The noise zone (`A` to `D`), the airport and its plan, and whether a noise report is required. |
| `check_abc_zone` | `address`, `commune`, `inseeCode` | The zone (`Abis`, `A`, `B1`, `B2`, `C`), the schemes it opens and the Loc'Avantages ceilings. |
| `calculate_living_area` | `isInCoproperty`, `hasDecentSizeMainRoom`, `rooms`, `lease` | The living area room by room, the Carrez area in a co-ownership, and the gap with the area stated in the lease. |
| `lookup_urban_plan_zone` | `address` | The zone of the local urban plan at the address, with its document on the Géoportail de l'urbanisme. |
| `check_principal_residence_sector` | `address` | Whether the address sits in a sector the urban plan keeps for principal residences. |
| `lookup_dpe` | `dpeNumber` | What ADEME publishes under a DPE number: class, consumption, emissions, dates and address, without its free-text fields. |
| `lookup_local_tax_rates` | `address`, `commune`, `inseeCode` | The local tax rates of the town, from the Ministry of the Economy's open data. |

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

23 prompts, for the agents that list them as shortcuts, each writing a question for one tool:

- `prepare_lease` (Préparer un bail)
- `calculate_waste_tax_share` (Calculer la taxe des ordures)
- `check_rent` (Vérifier un loyer)
- `calculate_notice_period` (Calculer un préavis)
- `calculate_wear_and_tear` (Calculer la retenue après vétusté)
- `calculate_recoverable_charges` (Calculer les charges récupérables)
- `calculate_rental_yield` (Calculer la rentabilité d'un achat)
- `calculate_charges_regularization` (Régulariser les charges)
- `list_required_lease_annexes` (Lister les annexes du bail)
- `calculate_reletting_rent_cap` (Calculer le loyer du nouveau locataire)
- `check_rent_revision_freeze` (Vérifier le gel du loyer (DPE F ou G))
- `calculate_prorated_rent` (Calculer le loyer d'un mois incomplet)
- `calculate_affordable_rent` (Calculer le loyer selon ses revenus)
- `calculate_deposit_cap` (Calculer le dépôt maximum)
- `calculate_deposit_return` (Calculer le délai pour rendre le dépôt)
- `calculate_living_area` (Calculer une surface habitable)
- `check_dpe_rental_ban` (Vérifier si le DPE interdit la location)
- `check_rent_guarantee_eligibility` (Vérifier un dossier pour Visale ou GLI)
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

- France only. Miramo holds no official rent table for Grenoble: the server gives no figure there. For Montpellier, `isStale` flags a rent table older than the latest order.
- When no order is in force at an address on the day of the question, the answer says so: it keeps the figures of the last rent table, gives no monthly cap, and these figures are not a legal cap.
- In Bordeaux and the Basque Country, the orders set different rents for a house and for a flat: without `propertyType` (`house` or `apartment`), `lookup_reference_rent` gives no rent there. In Bordeaux, Miramo holds the rent table for flats only: for a house, the answer gives no figure.
- For a few Basque Country addresses on the line between two zones, the answer gives the rents of both zones and no single cap (`zone_boundary`), and points to annex 2 of the order.
- In the French overseas collectivities other than Guadeloupe, Martinique, French Guiana, La Réunion and Mayotte, the tense zone, rent control perimeter and notice tools answer `not_settled`: Miramo has not checked the rules that apply there.
- It gives general information from the official texts, not legal advice.
- The lease tool prepares a draft and nothing more, and every calculation is an estimate from the figures given: the server signs nothing, sends nothing and touches no account.
- Up to 60 tool calls a minute per IP address, and 10 a minute for `calculate_housing_benefit`. Past that, the server answers `429` with `Retry-After`.
- The housing benefit is an estimate, not a decision: only the Caf or the MSA decides.

## Privacy

The server reads and never writes. It sends a public service only what its reading needs (an address to IGN's geocoder, a DPE number to ADEME, a town code to the tax and contact directories), never the caller's IP address. No tool asks for a name, an email address or a phone number, and the server does not keep what it receives. Details, in French: section 3.7 of [Miramo's privacy policy](https://www.miramo.fr/politique-de-confidentialite#3-7).

## Links

- Documentation, in French: https://www.miramo.fr/outils/mcp/developpeurs
- Official MCP Registry: `fr.miramo/mcp`
- Claude connectors directory: https://claude.ai/directory/miramo
- Website: https://www.miramo.fr
- Contact: contact@miramo.fr

## About this repository

The server's source code is not published. This repository describes the hosted server for the MCP Registry and GitHub's MCP catalogue.
