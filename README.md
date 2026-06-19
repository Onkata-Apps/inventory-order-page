# Public Inventory Order Page

A single-file, static web page (`index.html`) that lets anyone:

1. Import an inventory spreadsheet (`.xlsx` or `.csv`)
2. Filter products by manufacturer
3. Enter order quantities
4. Email the assembled order request to the order picker

No build step and no backend — it runs entirely in the browser and is meant to be served from **GitHub Pages** (static hosting).

## Files

| File | Purpose |
|------|---------|
| `index.html` | The whole app (inline CSS + JS). This is the deliverable. |
| `sample-inventory.csv` | Sample data with all seven columns, for testing. |

## Spreadsheet format

First row must be headers, with these exact columns (extra whitespace is trimmed on import):

```
Manufacturer, Our SKU, Manufacturer SKU, Location, Short Description, QOH, Sell Price
```

`QOH` and `Sell Price` are coerced to numbers. Only rows with `QOH >= 1` for the selected manufacturer are shown.

## EmailJS setup (required for real sending)

A static page can't send email on its own, so this uses [EmailJS](https://www.emailjs.com), which sends email client-side.

1. Create a free account at **emailjs.com**.
2. Add an email service (e.g. Gmail) → note the **Service ID**.
3. Create an email template that uses these variables:
   - `{{to_email}}` — recipient (the picker)
   - `{{subject}}` — `Booth Order Request`
   - `{{order_body}}` — the assembled order text
   Set the template's "To" field to `{{to_email}}`.
4. Copy your account **Public Key**.
5. Fill in the constants near the top of the `<script>` in `index.html`:

```js
const EMAILJS_PUBLIC_KEY  = "your_public_key";
const EMAILJS_SERVICE_ID  = "your_service_id";
const EMAILJS_TEMPLATE_ID = "your_template_id";
const PICKER_EMAIL        = "picker@ourcompany.com";
```

### Security note
The public key is visible in page source — that's expected for EmailJS. To stop strangers from triggering sends:
- In the EmailJS dashboard, restrict sending to your **GitHub Pages domain** (allowed-domain setting).
- Keep the free-tier rate limits in mind.

### Fallback
If the EmailJS constants are left as `"..."` (or a send fails), the page falls back to a `mailto:` link to `PICKER_EMAIL` with the same subject and body, opening the user's email client.

## Email format

**Subject:** `Booth Order Request`

**Body** (one block per line item):

```
Manufacturer: <value>
SKU: <Our SKU>
Location: <value>
Quantity: <entered qty>
---
```

## Deploy to GitHub Pages

1. Commit `index.html` to the repo (root or a `/docs` folder).
2. Repo **Settings → Pages** → set the branch/folder to serve.
3. Visit the published URL.

## Local testing

Open `index.html` in a browser and import `sample-inventory.csv`. Pick a manufacturer
(e.g. *Acme Tools*), enter quantities, and confirm the order summary and email body look correct.
With EmailJS unconfigured you'll get the `mailto:` fallback, which is enough to verify the order body.
