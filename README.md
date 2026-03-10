# Shopify → QuickBooks IIF Converter

Web app for the Perfumora Group — converts Shopify order exports into
QuickBooks Enterprise `.iif` files and saves them to Google Drive automatically.

---

## One-time Setup (takes ~10 minutes)

### Step 1 — Create a Google Cloud Project

1. Go to https://console.cloud.google.com
2. Click **Select a project → New Project**
3. Name it `IIF Converter` → **Create**

### Step 2 — Enable the Google Drive API

1. Go to **APIs & Services → Library**
2. Search for `Google Drive API` → click it → **Enable**

### Step 3 — Configure OAuth Consent Screen

1. Go to **APIs & Services → OAuth consent screen**
2. Choose **External** → **Create**
3. Fill in:
   - App name: `IIF Converter`
   - User support email: your email
   - Developer contact: your email
4. Click **Save and Continue** through all steps
5. On the **Test users** page — add the Gmail accounts of your 2–5 team members
6. Click **Save and Continue**

> ⚠️ You do NOT need to publish the app. Keep it in "Testing" mode.
> Only the test users you add can sign in.

### Step 4 — Create OAuth Client ID

1. Go to **APIs & Services → Credentials**
2. Click **+ Create Credentials → OAuth client ID**
3. Application type: **Web application**
4. Name: `IIF Converter Web`
5. Under **Authorized JavaScript origins**, add:
   - `http://localhost:3000` (for local testing)
   - `https://your-app-name.vercel.app` (your Vercel URL — add after deploying)
6. Click **Create**
7. Copy the **Client ID** (looks like `123456789-abc.apps.googleusercontent.com`)

### Step 5 — Paste Client ID into the app

Open `index.html` and find line ~11:

```js
const GOOGLE_CLIENT_ID = 'YOUR_CLIENT_ID.apps.googleusercontent.com';
```

Replace with your actual Client ID:

```js
const GOOGLE_CLIENT_ID = '123456789-abcdef.apps.googleusercontent.com';
```

---

## Deploy to Vercel

### Option A — Vercel CLI (fastest)

```bash
npm i -g vercel
cd shopify-iif-app
vercel
```

Follow the prompts. Your app will be live at `https://your-app-name.vercel.app`.

### Option B — GitHub + Vercel Dashboard

1. Push this folder to a GitHub repo
2. Go to https://vercel.com → **Add New Project**
3. Import your GitHub repo
4. Framework: **Other** (it's plain HTML)
5. Click **Deploy**

### After deploying — update Google Cloud

1. Go back to **APIs & Services → Credentials → your OAuth client**
2. Add your Vercel URL to **Authorized JavaScript origins**:
   `https://your-app-name.vercel.app`
3. Click **Save**

---

## How to Use

| Step | What to do |
|------|-----------|
| **1. QB Items** | Upload your QB Items export (only needed once — cached in browser) |
| **2. Upload Files** | Drop 1 CSV per brand — brand auto-detected from Vendor column |
| **3. Configure** | Pick the month/year — Drive folders are created automatically |
| **4. Convert** | Click Convert → IIF saved to Drive + downloaded to your computer |

### Google Drive folder structure (auto-created)

```
QB IIF Output/
└── 2026/
    ├── January/
    │   └── All_Brands_01-2026.iif
    ├── February/
    │   └── All_Brands_02-2026.iif
    └── March/
        └── All_Brands_03-2026.iif
```

No more manual folder creation. No more path changes in code.

---

## Source Column Routing (automatic)

Each row in the Shopify export is routed based on the `Source` column:

| Source value | Brand | → QB Customer |
|---|---|---|
| `tiktok` | Kumbra | Tiktok Lux (prefix: TL) |
| `tiktok` | Arabian Crest | Tiktok AC (prefix: TAC) |
| `faire` | Kumbra | Faire (prefix: K) |
| `faire` | Arabian Crest | Faire (prefix: F) |
| *(blank)* | Kumbra | Shopify - Kumbra (prefix: KP) |
| *(blank)* | Perfumora | Detected from Notes field |

Multi-line-item orders (same order split across rows) are handled — Source is
forward-filled automatically.

---

## QB Account Names

Defaults match standard QB Enterprise setup. To change, click
**⚙️ QuickBooks Account Names** in Step 3.

| Field | Default |
|---|---|
| A/R Account | `Accounts Receivable` |
| Sales Account | `Income Account` |
| Shipping Account | `Shipping Income` |
| Tax Account | `Sales Tax Payable` |
| Discount Account | `Discounts Given` |
| Tax Vendor | `TaxAgencyVendor` |

---

## Troubleshooting

| Problem | Fix |
|---|---|
| "Sign in with Google" does nothing | Check Client ID is correct in `index.html` |
| Sign-in works but Drive save fails | Add your Vercel URL to Authorized Origins in Google Cloud |
| Team member can't sign in | Add their Gmail to Test Users in OAuth consent screen |
| Wrong brand detected | Change the brand dropdown in Step 2 before converting |
| Unmatched SKUs warning | Add missing SKUs to your QB Items export file |
| QB import error | Verify account names match your Chart of Accounts exactly |
