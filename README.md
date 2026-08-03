<div align="center">

<img src="assets/icon-numetric.png" alt="NuMetric" width="96" height="96">

# NuMetric MCP Server

**Read your NuMetric accounting, reports & documents from any MCP client.**

[![MCP](https://img.shields.io/badge/MCP-remote%20server-blue)](https://modelcontextprotocol.io)
[![Auth](https://img.shields.io/badge/auth-OAuth%202.1%20%2B%20PKCE-green)](#authentication)
[![Read-only](https://img.shields.io/badge/access-read--only-brightgreen)](#read-only-by-design)
[![License](https://img.shields.io/badge/license-MIT-lightgrey)](LICENSE)

[Website](https://numetric.work) · [Documentation](https://help.numetric.work/en/articles/16012930-numetric-connector-for-claude) · [Privacy](https://help.numetric.work/en/articles/12888838-privacy-policy) · [Support](https://help.numetric.work)

</div>

---

A [Model Context Protocol](https://modelcontextprotocol.io) server for the
[NuMetric.work](https://numetric.work) POS / ERP / accounting platform. It exposes your live business
data as **39 read-only tools**, so an AI assistant answers from your actual books instead of guessing.

It never creates, edits, or deletes anything.

```
https://numetric-mcp.virifi.xyz/mcp
```

## What you can ask

- *"What's my balance sheet as of last month?"* / *"Show my P&L for Q2."*
- *"What's my cash runway and current ratio?"*
- *"List overdue invoices and total receivables."*
- *"How much sales tax did I collect vs. pay this quarter?"*
- *"Break down profit by project."*
- *"What documents are attached to invoice INV-2026-0007?"*

## Install

### Claude (web, Desktop, Code)

Settings → **Connectors** → **Add custom connector** → paste:

```
https://numetric-mcp.virifi.xyz/mcp
```

Then click **Connect** and sign in with your NuMetric email, password, and region. You're redirected
back when authorized.

### Claude Code (CLI)

```bash
claude mcp add --transport http numetric https://numetric-mcp.virifi.xyz/mcp
```

### Cursor / Windsurf / VS Code

```json
{
  "mcpServers": {
    "numetric": {
      "url": "https://numetric-mcp.virifi.xyz/mcp"
    }
  }
}
```

### Any other MCP client

Point it at the Streamable-HTTP endpoint above. The server advertises OAuth 2.1 discovery
(RFC 9728 / RFC 8414), so compliant clients start the sign-in flow automatically.

## Requirements

- A **NuMetric.work** account (any plan).
- Your **region** (e.g. `JO`, `SA`, or Global).
- An MCP client that supports remote servers with OAuth.

Access follows your existing NuMetric permissions — you only ever see the modules you already have
rights to. A tool you lack permission for returns `SCOPE_REQUIRED` rather than data.

## Choosing a business

If your account has more than one business, ask your assistant to **list your businesses**, then
**select one** (*"list my businesses"* → *"use Acme Trading"*). The choice sticks for the session and
you can switch anytime. Single-business accounts are selected automatically.

## Tools

<details>
<summary><b>Accounting spine</b> (6)</summary>

| Tool | Returns |
| --- | --- |
| `list_chart_of_accounts` | Chart of accounts with live computed balances and classification. Balances are ledger-signed (`debit − credit`); each row carries `magnitude`/`side`/`isNormalSide` |
| `search_transactions` | Ledger search by date, type, category, reviewed status |
| `get_transaction` | One transaction with its full journal lines |
| `get_daily_transactions` | The general journal for a period (debits = credits) |
| `get_financial_statement` | P&L, balance sheet, trial balance, cash flow |
| `get_account_transactions_report` | Per-account transaction detail for a period |

</details>

<details>
<summary><b>Analysis & KPIs</b> (2)</summary>

| Tool | Returns |
| --- | --- |
| `get_kpis` | Cash runway, current ratio, DSO, DPO, revenue concentration, unreviewed count |
| `get_cash_position` | Cash and bank balances across accounts |

</details>

<details>
<summary><b>Sales, receivables & payables</b> (9)</summary>

| Tool | Returns |
| --- | --- |
| `list_invoices` | Sales invoices with filters |
| `list_cash_invoices` | Cash invoices with filters |
| `list_estimates` | Quotes / estimates |
| `get_invoice_status` | AR status of one invoice by id or number |
| `get_receivables` | AR aging with overdue confidence |
| `list_bills` | Purchase bills |
| `get_bill_status` | AP status of one bill |
| `get_payables` | Total owed **plus** AP aging. `outstanding.outstandingTotal` is what you owe; `aging` covers only the aged subset |
| `list_customers` | Customers, with duplicate detection |

</details>

<details>
<summary><b>Tax</b> (2)</summary>

| Tool | Returns |
| --- | --- |
| `get_sales_tax` | Sales tax / VAT summary for a period |
| `get_sales_tax_transactions` | Per-transaction tax detail |

</details>

<details>
<summary><b>Projects</b> (2)</summary>

| Tool | Returns |
| --- | --- |
| `list_projects` | Projects with status |
| `get_multi_project_profit_loss` | P&L across one or many projects |

</details>

<details>
<summary><b>Retail & cashier reporting</b> (5)</summary>

| Tool | Returns |
| --- | --- |
| `list_cashiers` | Cashiers / staff |
| `get_cashier_sales_invoices` | Per-cashier sales by invoice |
| `get_cashier_sales_items` | Per-cashier sales by item |
| `get_cashier_attendance` | Cashier attendance |
| `get_invoice_source_report` | Sales grouped by invoice source |

</details>

<details>
<summary><b>Inventory</b> (3)</summary>

| Tool | Returns |
| --- | --- |
| `list_products` | Product list, including low-stock filtering |
| `get_item_summary` | Per-item stock card / movement |
| `get_source_quantity_report` | Quantity by source |

</details>

<details>
<summary><b>Fixed assets</b> (2)</summary>

| Tool | Returns |
| --- | --- |
| `get_asset_report` | Fixed-asset register with depreciation |
| `get_asset_custody_report` | Asset custody by holder |

</details>

<details>
<summary><b>Documents</b> (4)</summary>

| Tool | Returns |
| --- | --- |
| `get_storage_overview` | Document storage overview |
| `list_documents` | Attachments on invoices, bills, and other records |
| `get_document` | Download one document's content |
| `get_document_bundle` | Download all documents on a record as a ZIP |

</details>

<details>
<summary><b>Account &amp; session</b> (3)</summary>

| Tool | Returns |
| --- | --- |
| `list_businesses` | Businesses on your account |
| `select_business` | Choose which business to work on |
| `get_plan` | Your subscription plan and its limits, which add-ons are enabled (and why any are not), per-business feature toggles, and which tools your permissions allow |

</details>

Machine-readable schema for all 39: [`mcp-schema.json`](mcp-schema.json).

## Read-only by design

Every tool carries `readOnlyHint: true`. There is no payment, transfer, trade, create, edit, or delete
tool in this server — not gated, not present. Read-only tools run without per-call confirmation in
clients that honor the hint.

## Verified vs. Guarded

Every computed figure carries a `confidence` value:

- **Verified** — the figure comes straight from NuMetric's accounting engine and reconciles.
- **Guarded** — NuMetric flagged it as possibly stale or unbalanced, or it is a snapshot rather than
  a live read. Your plan and add-on entitlements are always Guarded: NuMetric provides no way to
  re-read them, so they reflect the moment you connected and refresh when you reconnect.

The server instructs connecting assistants to hedge on Guarded figures rather than present them as
final. Claude reports what your books say; it does not recompute your accounting itself.

## Authentication

OAuth 2.1 with PKCE (S256). You sign in on NuMetric's own page — your credentials are exchanged for a
token server-side and held in an encrypted vault. **The assistant only ever holds a short-lived token
scoped to this server; your NuMetric password and API token never reach it.**

Your business identity and region are bound to the authenticated principal and are never passed by the
AI caller. Disconnect anytime from your client's connector settings.

## Privacy & security

- Read-only — no tool can move money or change your data.
- Credentials never reach the AI client.
- Encrypted in transit and at rest.
- Every read is rate-limited per principal and written to an audit log.

See the [Privacy Policy](https://help.numetric.work/en/articles/12888838-privacy-policy) and
[Terms & Conditions](https://help.numetric.work/en/articles/12888765-terms-and-conditions).

## Troubleshooting

| Message | Meaning |
| --- | --- |
| `NO_BUSINESS_SELECTED` | Run *"list my businesses"*, then select one. |
| `SCOPE_REQUIRED` | Your NuMetric user lacks access to that module — ask your account admin. |
| A figure marked **Guarded** | Treat it as unconfirmed rather than final. |
| `STATEMENT_UNAVAILABLE` | That statement type isn't available yet for your account. |

## Support

Via the in-app chat at [help.numetric.work](https://help.numetric.work), or your usual NuMetric
support channel.

## About

Built and operated by **VIRIFI Technologies Ltd** (20–22 Wenlock Rd, London, UK), the maker of
NuMetric. This is a first-party connector: NuMetric is our own platform and backend.

This repository holds the connector's public manifest and documentation. Licensed [MIT](LICENSE).
