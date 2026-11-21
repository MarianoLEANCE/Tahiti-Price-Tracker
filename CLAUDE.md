# Tahiti Price Tracker

Personal log of shelf prices seen in stores in Tahiti (prices in XPF / "f").
Claude is the interface: the user sends photos, Claude records them; the user asks questions, Claude answers from the data.

## Data
- `data/prices.csv` — one row per item observed.
  - `date`: YYYY-MM-DD only (no time). Use the photo's EXIF date if present, otherwise the day it was uploaded, unless the user says otherwise.
  - `store`: must match a name in `data/stores.csv`.
  - `brand`, `product` (as on the shelf label, keep original French), `category` (English, e.g. Coffee, Dairy, Meat, Produce, Drinks, Household, Hygiene, Snacks, Pantry, Frozen, Baby, Pet, Alcohol).
  - `size` + `unit` (kg, g, L, mL, unit, pack...), `price_xpf` (integer).
  - `unit_price_xpf` + `unit_price_basis`: normalized price (per kg, per L, or per unit), rounded to integer.
  - `comments`: promo, best-before date, variant, origin, anything notable.
- `data/stores.csv` — known stores (`store`, `location`, `notes`).

## Upload workflow (user sends photo(s))
1. Read each photo: extract brand, product, size, price, promo info.
2. Store: use the name the user gives. If none and the store is not obvious from context, ASK before saving. If it's a new store, add it to `stores.csv` (ask for the location/branch if unknown, but don't block on it).
3. Skip duplicate photos (same image sent twice) and avoid duplicate rows (same date + store + product + price).
4. Append rows to `prices.csv`, show the user a short summary of what was recorded, flag anything uncertain (blurry price, unclear size).
5. Delete the uploaded photo files after processing. Photos are never committed to the repo.
6. Commit and push the CSV changes.

## Query workflow
Answer from `prices.csv`: latest price per store, cheapest store, price history over time, unit-price comparisons between sizes/brands. Always mention the date of the observation.
