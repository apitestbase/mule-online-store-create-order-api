# Create Order API — Contract

**System:** Online Store

> **Note:** By *contract* we mean everything the API promises observably: the HTTP request and response (what an API spec covers), plus the side effects (what it doesn't). In a real project, this information lives in your API specification and integration design documents — no separate contract document is needed. This file stands in for those documents so that the demo is self-contained.

The Create Order API accepts a customer's order, saves it to the database, and notifies the fulfillment system by publishing an order-created event.

## HTTP

### Request

`POST /api/orders`

Headers: `Content-Type: application/json`

| Field | Type | Notes |
| --- | --- | --- |
| customerCode | string | required |
| productCode | string | required |
| quantity | integer | required, positive |

Example:

```json
{
  "customerCode": "C001",
  "productCode": "P100",
  "quantity": 2
}
```

### Response

Status: `201 Created`

| Field | Type | Notes |
| --- | --- | --- |
| orderId | number | the created order's ID |
| status | string | `PENDING` |

Example:

```json
{
  "orderId": 1,
  "status": "PENDING"
}
```

## Side Effect 1: Database Record

One row is inserted into the `orders` table (PostgreSQL). Columns affected by this API:

| Column | Type | Value |
| --- | --- | --- |
| order_id | BIGINT GENERATED ALWAYS AS IDENTITY, primary key | set by the database |
| customer_code | VARCHAR(20) | request `customerCode` |
| product_code | VARCHAR(20) | request `productCode` |
| quantity | INTEGER | request `quantity` |
| status | VARCHAR(20) | `PENDING` |
| created_at | TIMESTAMP NOT NULL DEFAULT now() | set by the database |

Example row:

| order_id | customer_code | product_code | quantity | status | created_at |
| --- | --- | --- | --- | --- | --- |
| 1 | C001 | P100 | 2 | PENDING | 2026-09-13 09:14:22.417 |

*The production `orders` table would additionally carry columns not touched by this API (for example `modified_at`, maintained by other processes), plus indexes and constraints. They are not part of this API's promise, and are therefore not part of the contract.*

## Side Effect 2: Order-Created Event

One JMS message is published to queue `order.created` (ActiveMQ), with a JSON body:

| Field | Type | Value |
| --- | --- | --- |
| orderId | number | the created order's ID |
| customerCode | string | request `customerCode` |
| productCode | string | request `productCode` |
| quantity | integer | request `quantity` |
| status | string | `PENDING` |
| createdAt | string | the order row's `created_at`, formatted `yyyy-MM-dd'T'HH:mm:ss.SSS` |

Example:

```json
{
  "orderId": 1,
  "customerCode": "C001",
  "productCode": "P100",
  "quantity": 2,
  "status": "PENDING",
  "createdAt": "2026-09-13T09:14:22.417"
}
```

## Out of Scope

* Payment processing
* Input validation and error responses
* Duplicate order handling
* Authentication and authorization
