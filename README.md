# One Source Inventory (PWA)

A customer-facing inventory app for One Source (ONEPH), hosted on GitHub Pages.
It replaces the Apps Script web app, which customer networks block.

- **Front end:** this repo (static files on GitHub Pages)
- **Back end:** Supabase Edge Function `oneph-inventory` on the LWH Companion project (already deployed)
- **Data:** `oneph_inventory_export()`, the same source as the daily email, so the numbers always match

## Deploy
1. Create a GitHub repo named `LWH-OneSource-Inventory` and upload these files at the root.
2. Go to Settings > Pages, set Source to "Deploy from a branch", choose `main` and `/ (root)`, and save.
3. The app will be at `https://nbr1hawgfan.github.io/LWH-OneSource-Inventory/`.

The Edge Function only accepts requests from `https://nbr1hawgfan.github.io`. Any repo name
under that account works.

## Security
- Each PIN is checked inside the Edge Function. A valid PIN returns an 8-hour session token,
  and no data is returned without one.
- No database keys are in this repo. The function uses the service key from its own server environment.
- PINs are stored as salted hashes in `oneph_app_users`.
  - 10 wrong PINs within 15 minutes pauses sign-in for 15 minutes.
  - Removing a user ends their sessions immediately.
- Sign-ins and exports are logged in `oneph_app_access_log`, which you can view from the sheet's admin menu.

## Managing users
Use the Google Sheet's **One Source Admin** menu. It now writes to the Supabase user table.

## Adding a warehouse name
New warehouses show up automatically. To give one a friendly name, run this in Supabase:

    insert into oneph_app_locations (code, name, sort_order) values ('WHSE30', 'Name Here', 40);

## Updating the app
Edit the files and bump `APP_VERSION` in index.html and `CACHE` in sw.js so installed copies refresh.

## Excel export
The workbook is built in the browser with SheetJS. IDs stay as text, so Excel won't convert them
to scientific notation. Each export gets one tab per location plus autofilters. The free SheetJS
edition can't do frozen or colored header rows; the daily email attachment still has those.
