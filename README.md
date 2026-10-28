# POS

A point-of-sale app built with HTML, CSS, browser JavaScript, Node.js, Express REST endpoints, and MongoDB/Mongoose.

## Start

1. Start the MongoDB Community Server service.
2. In PowerShell, go to this folder and run `npm install`.
3. Copy `.env.example` to `.env`; set `MONGODB_URI=mongodb://127.0.0.1:27017/northstar_pos`.
4. Run `npm start`, then open http://localhost:3000.

The POS seeds 30 products when MongoDB connects. Sales use guarded stock updates and do not require a replica set. Each transaction stores customer name and normalized phone number when provided.

## Postman: connect and call the API

1. Keep `npm start` running. In Postman create a request and use base URL `http://localhost:3000`.
2. For GET requests, choose **GET**, enter the URL, and click **Send**.
3. For POST requests choose **POST**, open **Body → raw → JSON**, enter the JSON shown below, and click **Send**. No authentication is configured for this local demo.

### Requests

- `GET http://localhost:3000/api/health` — confirm the app and database mode.
- `GET http://localhost:3000/api/products` — list products. Add `?category=Produce&q=berry` to filter.
- `GET http://localhost:3000/api/products/NS-0001` — get a product by SKU (MongoDB `_id` also works).
- `POST http://localhost:3000/api/transactions` — record a sale and deduct stock. Body:

```json
{
  "items": [{ "productId": "NS-0001", "quantity": 2 }],
  "paymentMethod": "Paid at register",
  "customerName": "Alex Morgan",
  "customerPhone": "5551234567"
}
```

Save the returned `transactionId` to retrieve it with `GET http://localhost:3000/api/transactions/TRANSACTION_ID`.

- `GET http://localhost:3000/api/transactions` — list recent sales.
- `GET http://localhost:3000/api/customers/5551234567/transactions` — show that customer's sales, products, total items, and total spent. Use the same phone number on each sale; spaces and punctuation are ignored.

The web UI also lets you add a customer to the current sale and review a customer’s product purchase history from **Customers**.


## Manage products with Postman

For every request, keep the POS server running at `http://localhost:3000`. For POST and PUT, choose **Body → raw → JSON**. Product fields are `sku`, `name`, `category`, `price`, `emoji`, `stock`, and `description`. SKU, name, category, price, and stock are required when creating a product. PUT updates only fields included in the request.

### Create a product — POST

`POST http://localhost:3000/api/products`

```json
{
  "sku": "POS-0031",
  "name": "Green tea",
  "category": "Drinks",
  "price": 4.25,
  "emoji": "🍵",
  "stock": 20,
  "description": "Loose leaf green tea"
}
```

### Update product fields — PUT

`PUT http://localhost:3000/api/products/POS-0031`

Use the product SKU or MongoDB `_id` in the URL. Send only the fields to change, for example:

```json
{
  "price": 4.75,
  "stock": 25
}
```

### Delete a product — DELETE

`DELETE http://localhost:3000/api/products/POS-0031`

Use the SKU or MongoDB `_id`. A successful response includes the deleted product. Deleting it from the catalog does not remove its saved details from past transactions.
