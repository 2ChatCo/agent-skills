---
name: 2chat-whatsapp-catalog
description: Use this skill when the user wants to manage a WhatsApp Business product catalog via the 2Chat API — list, add, edit, or delete products and collections, manage product images. Trigger phrases include "list products", "add product to catalog", "edit product", "delete product", "product image", "list collections", "create collection", "WhatsApp catalog".
---

# 2Chat — WhatsApp Catalog

Manage WhatsApp Business product catalogs and collections via the [2Chat API](https://developers.2chat.co/).

Requires the free version of WhatsApp Business. Does not require a WhatsApp Business API (WABA) account.

## Authentication

All requests require the header:

```
X-User-API-Key: <api_key>
```

API keys are generated at [app.2chat.io/developers](https://app.2chat.io/developers). Base URL: `https://api.p.2chat.io/open`

---

## Products

### List products
```http
GET /whatsapp/catalog/products?from_number={your_number}
```
To list products from another WhatsApp Business account:
```http
GET /whatsapp/catalog/products/{target_number}?from_number={your_number}
```
Returns products with `id`, `name`, `description`, `price`, `currency`, `availability`, `images`, `approval_status` (`PENDING`, `OUTDATED`, `REJECTED`, `APPROVED`).

### Add product
```http
POST /whatsapp/catalog/product
```
```json
{
  "from_number": "+595981461442",
  "product": {
    "name": "Cool T-Shirt",
    "image_url": "https://example.com/tshirt.jpg",
    "description": "A very cool t-shirt",
    "price": "29.99",
    "currency": "USD",
    "is_hidden": false,
    "url": "https://mystore.com/tshirt",
    "retailer_id": "SKU-001"
  }
}
```
`name` and `image_url` are required. Currency defaults to the number's country currency. At least one product must already exist in the catalog. WhatsApp verification takes minutes to hours.

### Edit product
```http
PUT /whatsapp/catalog/product/{product_id}
```
```json
{
  "from_number": "+595981461442",
  "product": {
    "name": "Updated T-Shirt",
    "image_url": "https://example.com/new-tshirt.jpg",
    "description": "Updated description",
    "price": "34.99",
    "is_hidden": false,
    "url": "https://mystore.com/tshirt",
    "retailer_id": "SKU-001"
  }
}
```
`name` and `image_url` cannot be set to null. Edits undergo WhatsApp verification.

### Delete product
```http
DELETE /whatsapp/catalog/product/{product_id}
```
```json
{ "from_number": "+595981461442" }
```

### Add product images
```http
POST /whatsapp/catalog/product/image/{product_id}
```
```json
{
  "from_number": "+595981461442",
  "product": {
    "image_urls": [
      "https://example.com/image1.png",
      "https://example.com/image2.png"
    ]
  }
}
```
Max 2 image URLs per request. Does not replace the default product image.

### Delete product image
```http
DELETE /whatsapp/catalog/product/image/{product_id}/{image_index}
```
```json
{ "from_number": "+595981461442" }
```
`image_index` is zero-based. The default product image cannot be deleted.

---

## Collections

### List collections
```http
GET /whatsapp/catalog/collections?from_number={your_number}
```
To list collections from another account:
```http
GET /whatsapp/catalog/collections/{target_number}?from_number={your_number}
```
Returns collections with `id`, `name`, `approval_status`, and nested `products`.

### Add collection
```http
POST /whatsapp/catalog/collection
```
```json
{
  "from_number": "+595981461442",
  "collection": {
    "name": "Shoes",
    "product_ids": ["9846351425439378"]
  }
}
```
Collection names cannot be changed after creation — delete and recreate instead. Product IDs come from the list products endpoint.

### Edit collection
```http
PUT /whatsapp/catalog/collection/{collection_id}
```
```json
{
  "from_number": "+595981461442",
  "collection": {
    "remove_product_ids": ["product_id_1"],
    "add_product_ids": ["product_id_3"]
  }
}
```
The collection ID changes every time you edit it — update subsequent requests with the new ID.

### Delete collection
```http
DELETE /whatsapp/catalog/collection/{collection_id}
```
```json
{ "from_number": "+595981461442" }
```
