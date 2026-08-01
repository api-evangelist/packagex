---
name: Create and purchase a PackageX shipment
description: Create a shipment, review rates, and purchase a label using the PackageX Shipments API.
api: openapi/packagex-openapi-original.yml
operations:
  - shipments.create
  - shipments.retrieve
  - shipments.purchase
  - shipments.list
---

# Create and purchase a PackageX shipment

Use the PackageX Shipments API to create a shipment, inspect its rates, and buy a label.

## Auth
- Send your key in the `PX-API-KEY` header (the `X-API-KEY` header is also accepted).
- All requests must be over HTTPS. Use the sandbox host `https://sandbox--api.packagex.io` with a Sandbox key to test (identical behavior, fake payments); use `https://api.packagex.io` in production.

## Conventions
- Every response uses one envelope: `status`, `message`, `data`, `error_code`, `errors[]`, `events[]`, `pagination`, `endpoint`.
- On errors, `status` is a 4XX/5XX code, `data` is empty, and `errors[]` lists the properties that failed validation.
- No idempotency key is supported; do not blindly retry a `shipments.create`.
- List endpoints paginate with `?page=` and `?limit=`; `pagination.has_more` signals more pages.

## Steps
1. `shipments.create` — `POST /v1/shipments` with the from/to addresses and parcel details. The returned shipment includes available `rates` and a shipment `id`.
2. `shipments.retrieve` — `GET /v1/shipments/{shipment}` to re-read the shipment and its rates by id.
3. `shipments.purchase` — `POST /v1/shipments/{shipment}` (POST to the singular endpoint updates/purchases) selecting a rate to buy the label; the shipment's `provider`, `amount` and tracking are then populated.
4. `shipments.list` — `GET /v1/shipments` to page through existing shipments.

## Errors
See `errors/packagex-problem-types.yml`. Read `error_code` and `errors[]` for the specific failure; a 401 usually means a missing/invalid `PX-API-KEY` or a non-HTTPS request, a 403 means the key is not scoped to this API.
