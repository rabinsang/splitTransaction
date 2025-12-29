# PeerSplit (Personal Fork)
**PeerSplit (Personal Fork)** is my private, local-first expense tracker for splitting and tracking expenses with friends — inspired by Splitwise, but designed for **personal use** with **offline storage** and a clean UI.
This fork is intended for **single-user / personal tracking**. No cloud account, no public hosting required.
---
## Features
- 💯 **Free & Local** — No sign-up required, data stays on your device
- 🌐 **Offline-First** — Works fully offline once loaded
- 📱 **Cross-Platform PWA** — Use on mobile, desktop, or laptop
- ⚡ **Simple UX** — Minimal interface for quick entry and easy review
- 🌙 **Dark / Light Mode** — Theme toggle supported
- 🔄 **Import/Export** — Import from Splitwise and export ledger data to CSV
- 🕒 **Full Timestamps in Activity** — Activity log shows complete date + time
- 👤 **Member Dashboard (Per Person)**
- Net balance: how much they owe you / you owe them
- Filtered transaction history with that person
- **Monthly + Yearly summaries** shown above the transactions list
---
## How I Use It
- Track group expenses (who paid, who owes, settlements)
- Click a person’s name in the balance summary to open their dashboard
- Review monthly/yearly totals per person
- Export data to CSV for backup or additional analysis
---
## Running Locally (Dev)
```bash
bun install
bun run dev
````
Then open the local URL shown in the terminal (usually `http://localhost:3000`).
---
## Data & Privacy Notes
* This project is **local-first**. Data is stored locally on the device/browser profile.
* Use export/backup regularly if you care about long-term history (device loss = data loss).
---
## Roadmap (Personal)
* 📄 **PDF Export** — Export member dashboards and group ledgers as shareable PDFs
* 🏷️ **Tags/Categories** — Add categories for better reporting
* 🔁 **Recurring Expenses** — Monthly rent, subscriptions, etc.
* 🔐 **Backup/Restore** — One-click JSON backup + restore (optional encryption)
---
## License
PeerSplit is licensed under the **Fair Source License**. See [LICENSE.md](./LICENSE.md).



