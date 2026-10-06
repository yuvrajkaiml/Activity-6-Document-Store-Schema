MongoDB Database: everydaycart
│
├── products
│   ├── Product information
│   │   └── _id, sku, name, category, brand, price, stock_quantity
│   │
│   ├── specifications                 [EMBEDDED DOCUMENT]
│   │   └── Category-specific attributes
│   │
│   ├── reviews                        [EMBEDDED ARRAY]
│   │   └── Each review
│   │       ├── review_id, rating, comment, reviewed_at
│   │       └── user_id                 [REFERENCE → users._id]
│   │
│   └── supplier_id                    [REFERENCE → suppliers._id]
│
├── users
│   └── User information
│       └── _id, name, email, created_at
│
└── suppliers
    └── Supplier information
        ├── _id, name
        ├── contact                    [EMBEDDED DOCUMENT]
        └── address                    [EMBEDDED DOCUMENT]