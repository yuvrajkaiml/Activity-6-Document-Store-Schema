# Activity 6 – Designing a Simple Document Store Schema

**Module:** Module 3 – NoSQL  
**Duration:** 55 minutes  
**Database:** MongoDB-style document database

## 1. Aim

To design a simple document-oriented database for an e-commerce product catalogue, using embedded documents and references where appropriate.

## 2. Learning Objective

To gain hands-on experience modelling semi-structured data, including varying product attributes and nested reviews, and explain the modelling decisions.

## 3. Problem Scenario

An imaginary online store, **EverydayCart**, sells mobile phones, laptops, shoes, clothing, and home appliances. Customers need product details, prices, specifications, and reviews. Suppliers provide the products, and registered users write reviews.

A single fixed list of specifications would not suit all categories. For example, a phone has battery capacity, while shoes have a size and upper material. The design therefore combines common product fields with flexible category-specific specifications.

**Scope and assumptions:**

- All products are stored in one `products` collection.
- Each product represents one sellable item or variant and has one supplier.
- A user can review several products.
- Each product has only a small number of reviews in this practical.
- Orders, payments, authentication, and delivery tracking are outside the scope.
- All names, prices, specifications, and records are fictional academic examples.

## 4. E-commerce Product Catalogue

| Category | Example product | Distinguishing information |
|---|---|---|
| Mobile phones | Nova X1 Smartphone | RAM, storage, battery capacity, 5G support |
| Laptops | WorkMate Laptop | Processor, RAM, storage type, screen size |
| Shoes | Stride Running Shoes | Size, size system, upper material, shoe type |
| Clothing | Everyday Cotton T-shirt | Size, fabric, fit, sleeve type |
| Home appliances | HomeEase Microwave | Capacity, power, voltage, warranty |

These categories share basic product information but have different nested specifications. The two complete documents below demonstrate this difference using a phone and shoes.

## 5. Common Product Attributes

| Attribute | Data type in these examples | Purpose |
|---|---|---|
| `_id` | String | Unique product identifier |
| `sku` | String | Unique stock-keeping code |
| `name` | String | Product name |
| `category` | String | Category label |
| `brand` | String | Product brand |
| `description` | String | Short description |
| `price` | Embedded document | Amount and currency |
| `stock_quantity` | Integer | Available units of this variant |
| `supplier_id` | String reference | Link to the supplier |
| `specifications` | Embedded document | Category-specific attributes |
| `reviews` | Array of embedded documents | Product reviews |
| `created_at` | ISO-formatted string | Product creation date |

All products have a `specifications` document, but its internal fields vary. A product without reviews uses an empty `reviews` array. Product images are an optional extension described in Section 13, not a required field in the examples.

## 6. Category-Specific Attributes

| Category | Fields inside `specifications` | Illustrative values |
|---|---|---|
| Mobile phones | `operating_system`, `ram_gb`, `storage_gb`, `screen_size_inches`, `battery_capacity_mah`, `supports_5g` | Android, 8, 128, 6.5, 5000, true |
| Laptops | `processor`, `ram_gb`, `storage_gb`, `storage_type`, `screen_size_inches`, `operating_system` | Core i5, 16, 512, SSD, 15.6, Windows |
| Shoes | `size`, `size_system`, `color`, `upper_material`, `sole_material`, `shoe_type` | 9, US, Black, Mesh, Rubber, Running |
| Clothing | `size`, `color`, `fabric`, `fit`, `sleeve_type` | M, Blue, Cotton, Regular, Short |
| Home appliances | `appliance_type`, `capacity_litres`, `power_watts`, `voltage_volts`, `warranty_months` | Microwave, 25, 900, 120, 12 |

Only relevant fields are included. For example, shoes do not need `battery_capacity_mah`. Units are included in field names where useful to reduce ambiguity.

## 7. MongoDB Product Document

Each product is a document in the `products` collection. Its structure has three parts:

1. **Common information:** identifiers, name, category, brand, description, price, and stock.
2. **Embedded information:** price, category-specific specifications, and a small review array.
3. **Referenced information:** a supplier ID and a user ID inside each review.

The next two sections provide complete, valid JSON instances of this design. They are example documents, not formal JSON Schema validation definitions.

Readable string IDs are used instead of `ObjectId` constructors to keep the examples valid standard JSON. Dates are strings in ISO format. In an implemented database, date fields could instead be stored as BSON dates. Prices are JSON numbers for readability; exact monetary storage such as Decimal128 would need consideration in a real application.

## 8. Example Product 1 – Mobile Phone

```json
{
  "_id": "PROD001",
  "sku": "MOB-NOVA-128-BLK",
  "name": "Nova X1 Smartphone",
  "category": "Mobile phones",
  "brand": "NovaTech",
  "description": "A black smartphone with 128 GB storage and 5G support.",
  "price": {
    "amount": 299.99,
    "currency": "USD"
  },
  "stock_quantity": 40,
  "supplier_id": "SUP001",
  "specifications": {
    "operating_system": "Android",
    "ram_gb": 8,
    "storage_gb": 128,
    "screen_size_inches": 6.5,
    "battery_capacity_mah": 5000,
    "supports_5g": true,
    "color": "Black"
  },
  "reviews": [
    {
      "review_id": "REV001",
      "user_id": "USER001",
      "rating": 5,
      "comment": "The battery lasts well and the screen is clear.",
      "reviewed_at": "2026-09-12T10:30:00Z"
    },
    {
      "review_id": "REV002",
      "user_id": "USER002",
      "rating": 4,
      "comment": "Good everyday performance, but the camera could be better.",
      "reviewed_at": "2026-09-13T14:15:00Z"
    }
  ],
  "created_at": "2026-09-01T09:00:00Z"
}
```

The phone's specifications are embedded. Its supplier is referenced using `SUP001`. Each embedded review references its author through `user_id`.

## 9. Example Product 2 – Shoes

```json
{
  "_id": "PROD002",
  "sku": "SHO-STRIDE-US9-BLK",
  "name": "Stride Running Shoes",
  "category": "Shoes",
  "brand": "StrideWorks",
  "description": "Black mesh running shoes in US size 9.",
  "price": {
    "amount": 59.99,
    "currency": "USD"
  },
  "stock_quantity": 25,
  "supplier_id": "SUP002",
  "specifications": {
    "size": 9,
    "size_system": "US",
    "color": "Black",
    "upper_material": "Mesh",
    "sole_material": "Rubber",
    "shoe_type": "Running"
  },
  "reviews": [
    {
      "review_id": "REV003",
      "user_id": "USER002",
      "rating": 5,
      "comment": "Comfortable and lightweight for daily running.",
      "reviewed_at": "2026-09-15T08:45:00Z"
    }
  ],
  "created_at": "2026-09-02T09:00:00Z"
}
```

The shoes use the same common fields but different specifications. This document represents one size and colour. Other sizes would have separate product documents, SKUs, and stock quantities in this simple design.

## 10. Nested Reviews

Each element of `reviews` is an embedded review document. The following is one review from the phone example, not an additional collection:

```json
{
  "review_id": "REV001",
  "user_id": "USER001",
  "rating": 5,
  "comment": "The battery lasts well and the screen is clear.",
  "reviewed_at": "2026-09-12T10:30:00Z"
}
```

| Field | Meaning |
|---|---|
| `review_id` | Identifier for the review |
| `user_id` | Reference to `users._id` |
| `rating` | Whole-number score from 1 to 5 |
| `comment` | Review text |
| `reviewed_at` | Submission date |

**Decision:** Embed review content because it belongs to the product and is usually viewed with it. Reference the author because the user profile is shared across reviews.

**Limitation:** Reviews must remain few in this activity. If reviews grow substantially, a separate collection would be more suitable than an indefinitely growing product array. Average rating is not stored separately; it can be calculated from the ratings.

## 11. Users Collection

Each object in this JSON array represents a separate document in `users`:

```json
[
  {
    "_id": "USER001",
    "name": "Alex Morgan",
    "email": "alex.morgan@example.com",
    "created_at": "2026-08-20T10:00:00Z"
  },
  {
    "_id": "USER002",
    "name": "Priya Shah",
    "email": "priya.shah@example.com",
    "created_at": "2026-08-21T11:00:00Z"
  }
]
```

**Decision:** Reference users from reviews. One user can review several products, so copying their name and email into every review would duplicate information. Keeping the profile in one place simplifies updates.

For example, `USER002` writes reviews for both products but has only one user document.

## 12. Suppliers Collection

Each object in this JSON array represents a separate document in `suppliers`:

```json
[
  {
    "_id": "SUP001",
    "name": "Bright Electronics Supply",
    "contact": {
      "email": "sales@bright-electronics.example",
      "phone": "+1-202-555-0101"
    },
    "address": {
      "street": "100 Market Street",
      "city": "Austin",
      "state": "Texas",
      "postal_code": "78701",
      "country": "USA"
    }
  },
  {
    "_id": "SUP002",
    "name": "Everyday Apparel Supply",
    "contact": {
      "email": "sales@everyday-apparel.example",
      "phone": "+1-202-555-0102"
    },
    "address": {
      "street": "200 Commerce Avenue",
      "city": "Denver",
      "state": "Colorado",
      "postal_code": "80202",
      "country": "USA"
    }
  }
]
```

**Decision:** Products reference suppliers because one supplier may supply many products. Supplier contact information can then be updated in one place.

The supplier's `contact` and `address` are embedded because they belong to that supplier and are normally read with its details.

## 13. Embedding vs Referencing

| Data | Embed or reference | Reason | Advantages | Possible limitation |
|---|---|---|---|---|
| Product specifications | Embed in `specifications` | Belong to one product and vary by category | Read details together; support different attributes | Field names and units need consistency |
| Product reviews | Embed in `reviews` | Belong to the product; few reviews are assumed | Product and reviews can be read together | Large review arrays may require a separate collection |
| User information | Reference through `reviews.user_id` | One user can review many products | Avoid repeated profiles; update centrally | Profile details require an additional lookup |
| Supplier information | Reference through `supplier_id` | One supplier can supply many products | Avoid duplication; maintain details centrally | Supplier details require an additional lookup |
| Product category | Embed as a string | Five simple labels are sufficient | Simple storage; no category lookup | Spelling must be consistent; richer categories may need separate records |
| Product images | Embed URLs and metadata; reference image files | Keep links with the product and files outside it | Links are readily available without large files in the document | Links may break; file storage needs separate management |

**Image scope:** An optional `images` array could hold URLs and alternative text. Actual image files would not be embedded in this practical's product documents.

**Simple rule:** Embed small product-owned details; reference shared, independently maintained entities.

## 14. Schema Design Diagram

```text
MongoDB Database: everydaycart
|
|-- products
|   |-- Product information
|   |   |-- _id, sku, name, category, brand, description
|   |   |-- price                         [EMBEDDED DOCUMENT]
|   |   |-- stock_quantity, created_at
|   |
|   |-- specifications                    [EMBEDDED DOCUMENT]
|   |   |-- Category-specific attributes
|   |
|   |-- reviews                           [EMBEDDED ARRAY]
|   |   |-- Each review
|   |       |-- review_id, rating, comment, reviewed_at
|   |       |-- user_id                   [REFERENCE -> users._id]
|   |
|   |-- supplier_id                       [REFERENCE -> suppliers._id]
|
|-- users
|   |-- User information
|       |-- _id, name, email, created_at
|
|-- suppliers
    |-- Supplier information
        |-- _id, name
        |-- contact                       [EMBEDDED DOCUMENT]
        |-- address                       [EMBEDDED DOCUMENT]
```

**Embedded** means stored inside the parent document. **Referenced** means an ID links to a document stored separately.

| Relationship | Cardinality | Representation |
|---|---|---|
| Supplier to products | One-to-many | `products.supplier_id` |
| Product to reviews | One-to-many | Embedded `reviews` array |
| User to reviews across products | One-to-many | `reviews.user_id` |
| Product to specifications | One-to-one | Embedded `specifications` document |

References are ID values, not automatically included profiles. The application must retrieve the matching user or supplier when those details are needed.

## 15. Advantages of the Document-Oriented Model

1. **Flexible attributes:** Different categories can have different specifications in one collection.
2. **Natural grouping:** Nested documents group related values, such as price amount and currency.
3. **Convenient product retrieval:** Basic information, specifications, and embedded reviews can be retrieved together.
4. **Simple evolution:** A new specification can be added without introducing irrelevant fields into other categories.
5. **Balanced relationships:** Embedding supports product-owned details, while references reduce duplication of shared profiles.

These benefits suit this catalogue, but they do not remove the need for validation or guarantee that every operation is faster than a relational alternative.

## 16. Design Justification

The schema uses only three collections to remain suitable for a classroom activity.

- **One product collection:** Common fields stay consistent while specifications vary by category.
- **Embedded price:** Amount and currency form one related piece of information.
- **Embedded specifications:** Specifications belong to their product and are needed when viewing it.
- **Embedded reviews:** Suitable for the assumed small number of reviews, not unlimited growth.
- **Referenced users and suppliers:** Shared profiles are maintained once rather than copied into many products.
- **Embedded supplier contact and address:** These are parts of the supplier, not separate entities in this design.
- **Category string:** A separate category collection is unnecessary for five simple labels.
- **No stored average rating:** Avoids maintaining a duplicated, derived value.
- **One variant per product:** Keeps inventory handling simple.

### Proposed validation rules

- IDs must be unique within their collections, and product SKUs must be unique.
- Price must be non-negative and include a currency.
- Stock must be a non-negative integer.
- Ratings must be integers from 1 to 5.
- Category names must use the agreed labels.
- Specifications must match the category and use consistent units.
- Referenced users and suppliers must exist.
- Products without reviews must use an empty array.

These rules are design requirements; writing the JSON does not automatically enforce them. Reference existence should be checked by the application. Database validation and indexes can enforce suitable field and uniqueness rules when implemented.

### Example consistency check

Both product IDs and SKUs are distinct. Every supplier and user reference matches a supplied document. All ratings are between 1 and 5, and prices and stock quantities are non-negative. The phone and shoes correctly use different specification fields.

## 17. Final Conclusion

This activity designed a simple MongoDB-style product catalogue using `products`, `users`, and `suppliers` collections. Common product fields provide consistency, while embedded specifications represent the different attributes of each category.

A small review array is embedded in each product. Users and suppliers are referenced because their information is shared and should be maintained separately. The design therefore demonstrates both embedding and referencing without introducing a complicated production system.

**Learning outcome achieved:** A reasonable document schema was produced for semi-structured data, with clear explanations of its modelling decisions and limitations.
