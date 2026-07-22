# Al Farasha Al Khadhra — Sales Ledger

A self-contained, offline-first Progressive Web App for sales and credit-ledger management, built for a building materials trading company in Sharjah, UAE.

## Overview

Al Farasha Sales Ledger manages the full sales side of a **dispatch-and-shop hybrid** business — salesmen carrying goods out for delivery, alongside walk-in retail sales. The entire app is a single HTML file: no backend server, no build step, no npm install. Open it in a browser and it works; installed as a PWA, it behaves like a native app on phone or desktop.

## Features

- Customer accounts with credit ledgers and credit limits
- Sales invoices with optional 5% VAT
- Payment collection tracking
- Daily dispatch (van loading / stock tracking)
- Bilingual (English/Urdu) customer account statements
- Overdue-account and credit-limit notifications
- Global search across customers, invoices, and payments
- App lock (PIN + biometric)
- Local backup & restore
- Invoice print / PDF preview

## Tech Stack

- **Core:** Pure HTML5, CSS3, vanilla JavaScript — no frameworks, no build tools
- **Sync/backend:** Firebase Realtime Database (optional — the app runs fully offline without it)
- **Storage:** `localStorage` as the primary data store and offline cache
- **PDF export:** jsPDF + jsPDF-AutoTable
- **PWA:** Service worker, manifest, installable on mobile and desktop

## Architecture

### System Architecture

```mermaid
flowchart TD
    subgraph Clients["Client Devices"]
        A[Phone]
        B[Laptop]
        C[Tablet]
    end
    A --> D[Al Farasha Sales Ledger<br/>Offline-First PWA]
    B --> D
    C --> D
    D --> E[localStorage<br/>Primary Store + Cache]
    D <--> F[(Firebase Realtime Database<br/>Optional Cloud Sync)]
```

### Offline Sync Flow

```mermaid
flowchart TD
    A[User performs action<br/>e.g. new invoice] --> B[Update localStorage<br/>afkSalesLedgerDB_v1]
    B --> C{Firebase reachable?<br/>.info/connected}
    C -->|Yes| D[Write directly to Firebase]
    C -->|No| E[Queue write<br/>afkSyncQueueV1]
    E --> F["online" event fires]
    F --> G[flushSyncQueue replays<br/>queued operations]
    G --> D
    D --> H[(Firebase Realtime Database)]
```

One deliberate design decision worth knowing: dispatch quantities sold (`qtySold`) are recalculated from invoices on every load rather than stored and synced — an earlier version stored this value directly, which let a stale offline-queued write silently overwrite a newer one after reconnect. See [`docs/DOCUMENTATION.md`](docs/DOCUMENTATION.md) for the full writeup.

See [`docs/DOCUMENTATION.md`](docs/DOCUMENTATION.md) for the full data architecture, Firebase sync model, and offline-write flow.

## Screenshots

<table>
<tr>
<td align="center"><img src="Screenshot/dashboard.png" width="280"/><br/><sub>Dashboard</sub></td>
<td align="center"><img src="Screenshot/new-invoice-empty.png" width="280"/><br/><sub>New Invoice — Dispatch/Shop toggle</sub></td>
<td align="center"><img src="Screenshot/new-invoice-filled.png" width="280"/><br/><sub>New Invoice — filled, VAT applied</sub></td>
</tr>
<tr>
<td align="center"><img src="Screenshot/invoice-preview.png" width="280"/><br/><sub>Invoice Preview / Print</sub></td>
<td align="center"><img src="Screenshot/daily-dispatch.png" width="280"/><br/><sub>Daily Dispatch</sub></td>
<td align="center"><img src="Screenshot/payments-received.png" width="280"/><br/><sub>Payments Received</sub></td>
</tr>
<tr>
<td align="center"><img src="Screenshot/customer-statement.png" width="280"/><br/><sub>Customer Statement — generate</sub></td>
<td align="center"><img src="Screenshot/customer-statement-print.png" width="280"/><br/><sub>Customer Statement — print view</sub></td>
<td align="center"><img src="Screenshot/items.png" width="280"/><br/><sub>Item Catalog</sub></td>
</tr>
<tr>
<td align="center"><img src="Screenshot/notifications.png" width="280"/><br/><sub>Notifications</sub></td>
<td align="center"><img src="Screenshot/sidebar-menu.png" width="280"/><br/><sub>Drawer — backup, sync, PIN lock</sub></td>
<td></td>
</tr>
</table>

## Documentation

Full technical and feature documentation (data models, every screen, print system, security notes, known design decisions): [`docs/DOCUMENTATION.md`](docs/DOCUMENTATION.md)
