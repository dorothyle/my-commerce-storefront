# Promo Banner Block

## Overview

The Promo Banner block displays a heading and a responsive grid of featured products pulled from a Commerce category via GraphQL. It's intended for merchandising callouts (e.g. "Featured Products", seasonal promos) authored directly on a page.

## Integration

### Block Configuration

Configuration is read via `readBlockConfig()`:

- `category-id` - The Commerce category ID to fetch products from. Defaults to `''` (no category, returns no products).
- `heading` - Heading text displayed above the product grid. Defaults to `Featured Products`.
- `max-products` - Maximum number of products to fetch and display. Defaults to `4`. Falls back to `4` if the value isn't a valid number.

<!-- ### Local Storage

No localStorage keys are used by this block. -->

<!-- ### Events

No events are listened for or emitted by this block. -->

## Behavior Patterns

### Data Fetching

- On decoration, the block immediately renders the heading and a "Loading products..." placeholder.
- It queries Commerce Storefront GraphQL (`productSearch`) filtered by `category-id`, capped at `max-products`, requesting name, SKU, URL key, primary image, and final/regular price.
- Once resolved, the placeholder is replaced with a grid of product cards; each card links to the product detail page via `getProductLink(urlKey, sku)`.

### User Interaction Flows

1. **Initialization**: Block renders heading and loading state, then fetches category products.
2. **Product Display**: Each product renders as a card with image, name, and price, linking to its PDP.
3. **Navigation**: Clicking a product card navigates to that product's detail page.

### Error Handling

- **No Products Found**: If the category returns no products, shows "No products found."
- **API Errors**: If the GraphQL request fails, logs the error to the console and shows "Unable to load products."
