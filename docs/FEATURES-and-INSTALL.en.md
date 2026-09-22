# Pazarova — Features & Installation Guide

> Multi-vendor, escrow-protected marketplace software for game items & e-pin
> Live demo: **https://pazarova.epinsoft.com.tr**

---

## 1. Product Overview

**Pazarova** is a **commission-based**, multi-vendor marketplace where users open their own stores and post listings, with every transaction protected by **escrow**. It is built for selling in-game items, accounts, e-pin, and digital goods.

- **Who is it for?** Entrepreneurs who want to launch a game-item / e-pin / digital-goods marketplace, and businesses looking to move existing e-pin resellers into a multi-vendor model.
- **Positioning:** Comparable to multi-vendor game marketplaces like itemavm and itemsatis; differentiates with escrow, commission tiers, and gamification.
- **Model:** Buyer pays → funds are held in **escrow** → seller delivers → buyer confirms → escrow is **released** to the seller. In a dispute, the admin decides on refund/release.

---

## 2. Features

### 2.1 Marketplace Core
| Feature | Description |
|---|---|
| Store creation | Members open their own store — store page + cover/avatar + verification badges |
| Listing wizard | 4-step listing flow (Category → Details → Boost/promotion → Agreement), rich text editor |
| Escrow | Payment held in escrow; released to the seller on delivery confirmation. Escrow period configurable per product type |
| Dispute management | Buyer raises an objection via "Report a Problem"; admin refunds buyer or releases to seller from the panel |
| Commission tiers | Commission rate by seller tier; automatic upgrade based on sales volume; commission frozen at invoice time |
| Coupons | Platform coupons + seller coupons; scoped (product/category/seller), with usage limits |
| Wallet / dual balance | Spendable balance + withdrawable earnings balance; wallet transaction ledger |
| Withdrawals | Seller withdrawal request + admin approval flow |

### 2.2 Delivery
- **Automatic delivery:** From a stock-code pool, instantly after payment (atomic stock claim prevents double-selling).
- **Manual (C2C) delivery:** Seller delivers from the panel after payment; buyer confirms via "Received". Auto-refund on manual-delivery timeout.

### 2.3 Messaging / CRM
- In-order buyer–seller messaging.
- Customer-relationship tools in the seller panel (order/customer management).

### 2.4 Integration (Seller API + Webhooks)
- **Seller API:** Token-protected REST; sellers manage their own product / stock / order / balance data. Authorization isolation (a seller can only access their own data).
- **Outbound webhooks:** **HMAC-SHA256 signed** POST notifications for `order.paid / delivered / confirmed / disputed / refunded` events; queue + retry + SSRF protection.

### 2.5 Gamification
- **Wheel of Fortune:** Server-side weighted draw; daily limit; points/coupon rewards (real credit).
- **Night Market:** Time-limited, locked "stall" mechanic; lineup fixed throughout the day, refreshed nightly at 00:00.

### 2.6 Admin Panel
- Store curation (verification, identity approval, store tier, cover upload).
- Seller tier management and commission rates.
- Gamification management (reward CRUD, wheel / night-market settings).
- Dispute panel.
- Product / category / coupon / campaign / dealership / referral management.
- Reports (revenue, profit/margin).

### 2.7 Other
- SEO-friendly structure + blog engine.
- Mobile-responsive UI.
- Cloudflare-compatible.

---

## 3. Technical Architecture

| Component | Technology |
|---|---|
| Language / Framework | PHP — CodeIgniter 3 (HMVC) |
| Database | MySQL / MariaDB |
| Web server | Apache or LiteSpeed (via `.htaccess`) |
| Scheduled tasks | Cron (escrow release, auto-refund, webhook delivery) |
| CDN / Proxy | Cloudflare-compatible |
| Recommended PHP | PHP 7.4+ (8.x compatible) |

---

## 4. Installation & Configuration

### 4.1 Requirements
- PHP 7.4 or later (recommended), extensions: `mysqli/pdo_mysql`, `curl`, `mbstring`, `openssl`, `gd`.
- MySQL 5.7+ / MariaDB 10.3+.
- Apache (`mod_rewrite`) or LiteSpeed; `.htaccess` enabled.
- Cron access (or an external HTTP-cron service such as cron-job.org).

### 4.2 Uploading the Files
```bash
# Upload the source to your web root (FTP/SFTP or file manager)
# e.g. public_html/ or the domain's root directory
```
Make sure the `.htaccess` files (root + `assets/`) are uploaded — URL rewriting and security rules depend on them.

### 4.3 Importing the Database
```bash
# Import the schema via phpMyAdmin or CLI
mysql -u USER -p DATABASE < pazarova_schema.sql
```

### 4.4 Configuration
Under `application/config/`:
- **Database** (`database.php`): host, user, password, database name.
- **Base URL** (`config.php`): `$config['base_url']` = your domain.
- **Encryption key** (`config.php`): `$config['encryption_key']` — set a strong, unique value (stock codes are stored encrypted).

> Actual credentials (DB password, admin login, keys) are provided separately during setup; they are not included in this document.

### 4.5 Cron Setup
Two jobs are needed: escrow release + auto-refund + tier upgrade, and webhook delivery. Example (keys provided at setup):
```cron
# Escrow release / auto-refund / tier — every 10 minutes
*/10 * * * * curl -s "https://YOURDOMAIN/cron/pay_sellers?key=CRON_KEY" >/dev/null 2>&1

# Webhook delivery — every 5 minutes
*/5 * * * * curl -s "https://YOURDOMAIN/cron/run_webhooks?key=CRON_KEY" >/dev/null 2>&1
```
If you don't have cron access, an external HTTP-cron service (e.g. cron-job.org) works too.

### 4.6 Payment Gateways (IMPORTANT — a Selling Point)
> **Payment gateways ship as placeholders.** The software is designed so the payment infrastructure is yours to connect.

- You connect **your own POS / payment provider** (iyzico, PayTR, Stripe, Shopier, etc.) from the admin panel.
- Until you enter your own credentials (API key / merchant info), the system runs in **demo mode**.
- This means the software is not locked to any payment provider — you use your own agreement.

### 4.7 First Run
1. **Admin login:** Sign in with the admin address and credentials provided at setup.
2. **Add categories:** Create your first categories under Admin → Categories.
3. **First store / listing:** Open a store with a member account and publish the first listing via the wizard.
4. **Payments:** Enter your own payment-provider details (see 4.6).
5. **Cron:** Set up the escrow and webhook jobs (see 4.5).

---

## 5. Delivery Package

- Full **source code**.
- **Database schema** (SQL).
- **Installation support** (configuration + initial deployment).

---

## 6. Contact

| | |
|---|---|
| Web | [epinsoft.com.tr](https://epinsoft.com.tr) |
| Email | pazarlama@epinsoft.com.tr |
| Phone | +90 850 255 18 01 |

> Explore the live demo: **https://pazarova.epinsoft.com.tr** — reach out for purchase and custom requests.
