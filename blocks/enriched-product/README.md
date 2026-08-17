# Enriched Product Block

## Overview

The Enriched Product block displays a single product card that combines Adobe Commerce catalog data (name, image, price) with supplemental enrichment data (sustainability score, estimated delivery) fetched from a custom API Mesh endpoint. It is useful for spotlighting a specific SKU with additional merchandising context that isn't part of the standard catalog.

## Integration

### Block Configuration

| Configuration Key | Type | Default | Description | Required | Side Effects |
|-------------------|------|---------|-------------|----------|--------------|
| `sku` | string | `''` | The SKU of the product to fetch and display | Yes | If missing, the block renders a "No SKU configured" message and stops |

### Data Source

- Queries a single GraphQL endpoint (`MESH_ENDPOINT` in [enriched-product.js](enriched-product.js)) that combines:
  - `products(skus: [$sku])` — standard catalog product data (name, images, price/priceRange)
  - `Enrichment_getProductEnrichment(sku: $sku)` — custom enrichment data (`sustainabilityScore`, `estimatedDelivery`, `enrichedAt`)

<!-- ### URL Parameters

No URL parameters directly affect this block's behavior. -->

<!-- ### Local Storage

No localStorage keys are used by this block. -->

<!-- ### Events

No events are listened for or emitted by this block. -->

## Behavior Patterns

### Rendering States

- **Loading**: Shows "Loading product details..." while the mesh request is in flight
- **Missing SKU**: Shows "No SKU configured for this block." when the `sku` config key is absent
- **Product Not Found**: Shows "Product not found." when the catalog query returns no matching product
- **Success**: Renders a card with the product image, name, SKU, price, and (if available) an enrichment section with a sustainability badge and estimated delivery
- **Error**: Shows "Unable to load product data." if the mesh request fails or returns GraphQL errors (details are logged to the console)

### Sustainability Badge

The sustainability score returned by the enrichment API is mapped to a badge label and style:

- `80–100`: "Excellent" (`badge--excellent`)
- `60–79`: "Good" (`badge--good`)
- `0–59`: "Fair" (`badge--fair`)

### Price Formatting

- Simple products use `price.final.amount`
- Complex products (e.g. configurable) use `priceRange.minimum.final.amount`
- Amounts are formatted as `<CURRENCY> <value>` with two decimal places

### Error Handling

- **Missing SKU**: Renders a fallback message instead of attempting a request
- **API/Network Errors**: Caught and logged to the console; the block falls back to an "Unable to load product data." message
- **GraphQL Errors**: Collected from `payload.errors` and thrown/logged before falling back to the error message
- **Missing Enrichment Data**: The enrichment section is omitted entirely if no enrichment data is returned, while catalog data still renders
