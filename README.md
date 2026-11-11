# UP Perfume — Website + Google Sheets + GitHub CRM

## Important status
This is a starter implementation. Configure the real BDT prices, Google Sheet, Apps Script deployment and admin access before accepting live orders. The sample bottle illustrations are CSS placeholders; replace them with licensed product photography.

## 1. Create Google Sheet
Create a new Google Spreadsheet. Copy its ID from its URL.

## 2. Configure Apps Script
1. Open Extensions → Apps Script from the spreadsheet.
2. Paste `apps-script/Code.gs` into `Code.gs`.
3. In Project Settings → Script Properties add:
   - `SPREADSHEET_ID` = spreadsheet ID
   - `ADMIN_TOKEN` = a long random secret (at least 32 random characters)
4. Run `setup()` once and approve permissions.
5. In the Products sheet, set real selling prices in BDT and correct stock.
6. Deploy → New deployment → Web app.
   - Execute as: Me
   - Who has access: Anyone (needed for public order submission)
7. Copy the `/exec` URL.

Security note: because public customers submit orders, the endpoint is public. Admin actions check the token, but a static GitHub CRM with a token is a basic gate, not strong identity security. For a real business, add Google Identity/OAuth verification or a server-side proxy with rate limiting and audit controls. Do not reuse this demo token elsewhere.

## 3. Configure website and CRM
Replace `PASTE_YOUR_GOOGLE_APPS_SCRIPT_WEB_APP_URL` in:
- `index.html`
- `crm/index.html`

Set your WhatsApp number by replacing `8801XXXXXXXXX`.
Set real BDT product prices in the Apps Script Products sheet. Prices are intentionally zero until you configure them; the frontend prevents checkout for unpriced items.

## 4. Publish on GitHub Pages
1. Create a GitHub repository, e.g. `up-perfume`.
2. Upload `index.html`, `crm/`, and `apps-script/` (Apps Script code can also stay in a private repository).
3. Repository Settings → Pages → Deploy from branch → main / root.
4. Website: `https://USERNAME.github.io/REPOSITORY/`
5. CRM: `https://USERNAME.github.io/REPOSITORY/crm/`

Keep the repository free of API secrets, spreadsheet IDs, and private customer data.

## 5. CORS / browser deployment check
Apps Script ContentService redirects responses. Browser cross-origin behavior can vary. Test from the deployed GitHub Pages origin using a real test order. If browser fetch is blocked by CORS, do not switch to `no-cors` (the response cannot be read). Add a small authenticated server-side proxy (Cloudflare Worker or another backend) or host the CRM/API UI through a compatible Google Apps Script HTML service. Re-test create order, read orders and update status end-to-end.

## 6. Before going live
- Replace CSS bottle illustrations with your actual Elma, Mavi and Serin photos.
- Configure BDT prices, stock and delivery charge.
- Add verified payment gateway (SSLCommerz / bKash merchant integration etc.) before enabling online payment.
- Configure Facebook Pixel and GA4 IDs; do not invent IDs.
- Add privacy notice and cookie consent.
- Add server-side order rate limiting, stronger admin authentication, and customer data retention rules.
- Test order creation, duplicate submission, stock deduction, cancellation/restock policy, CRM status update, and mobile layout.

## API actions implemented
GET: `health`, `products`, `orders` (admin token), `order` (admin token), `stats` (admin token)
POST: `createOrder` (public with validation/idempotency), `updateStatus` (admin token), `updateProduct` (admin token)

The sample CRM implements login, dashboard statistics, order search/filter and order status updates. Extend it with payment status, product editor, customer directory, delivery tracking, export and invoice workflows before production.
