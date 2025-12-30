## Types of NoSQL Database
### JSON-tree like structure(Real time Database) 
```
{
  "name": "Alice",
  "children": [
    {
      "name": "Bob"
    },
    {
      "name": "Carol"
    }
  ]
}
```

**Features** : 
- Have hierarchy
- Hard to query
- Complex relationship between data
- fast to get result

### Collection-document sub-Collection Model( Fire-Store Database )
```
// Collection: products
{
  "id": "12345",
  "name": "Product 1"
}

// Subcollection: product-images
{
  "id": "67890",
  "url": "https://example.com/product-1.png"
}
```

__Features__ : 
- No hierarchy
- easy to query
- Simple relationship between data
- fast to get result
