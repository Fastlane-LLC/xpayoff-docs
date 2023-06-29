# Payments

## How to create
- Individual request: To create new order type `Send Payment` by itself use [Create Order(s) Request](#create-order-s-request)

- Add to existing packet: To add a new order type `Send Payment` to an existing `packet` use [Add Order to Packet](#add-order-to-packet), must include the `packetId` you want to associate the new order with in query parameters.


> Request example for "Check" payment:

```json
{
  "lenderId": "48a970ab-f4fd-49ab-b6b1-7179b8e278f5",
  "vin": "DXVHZ8CH2A5M03260",
  "types": [
    "Send Payment"
  ],
  "payment": {
    "paymentAmount": 4533.88,
    "type": "ACH",
    "achRoutingNumber": "021200339",
    "achAccountNumber": "9984344"
  },
  "titleTransfer": {
    "businessName": "Fast Cars, Inc.",
    "requesterName": "John Doe",
    "requesterPhoneNumber": "214-555-1212",
    "mailingAddress": {
      "attn": "John Doe",
      "streetAddress": "1000 Main Street",
      "streetAddress2": "Suite 900",
      "city": "Dallas",
      "state": "TX",
      "zipCode": "75204"
    }
  }
}

```

> Request example for "ACH" payment:

```json
{
  "lenderId": "48a970ab-f4fd-49ab-b6b1-7179b8e278f5",
  "vin": "DXVHZ8CH2A5M03260",
  "types": ["Send Payment"],
  "payment": {
    "paymentAmount": 3448.72,
    "type": "Check",
    "mailingAddress": {
      "attn": "John Doe",
      "streetAddress": "1000 Main Street",
      "streetAddress2": "Suite 900",
      "city": "Dallas",
      "state": "TX",
      "zipCode": "75204",
      "message": "insert message here"
    } 
  }
}

```
### Request Body

This route accepts a JSON payload of an object comprising of:

Body Parameter | Description                                                                                                                                                                                                                                                                                                                                                                       | Required? | Fulfillment Center Only
-------------- |-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------| --------- | -----------------------
lenderId | `string` : The LossExpress Lender UUID. You can receive this UUID by utilizing our lender search functionality                                                                                                                                                                                                                                                                    | Y | N
lenderName | `string` : A lender name, if a corresponding lender is not in our lender database; this will enter the lender into our queue for validation. Use lenderId whenever possible                                                                                                                                                                                                       | Y | Y
vin | `string` : Vehicle Identification Number                                                                                                                                                                                                                                                                                                                                          | Y | Y
types | `string[]` : An array of the types of orders for the request                                                                                                                                                                                                                                                                                                                      | Y | Y 
payment | `object` : An object required for 'Send Payment' order type. Includes `paymentAmount`, `type`, `speed`, `achRoutingNumber`, `achAccountNumber`, `mailingAddress` (see below)                                                                                                                                                                                                      | Y | Y
titleTransfer | `object` : An object consisting of `businessName`, `requesterName`, `requesterPhoneNumber`, `mailingAddress` (see below). Title transfer information will to be sent to the lender for who and where to send the title to. We will attempt to send the information you provide to the lender, and it may require you to contact the lender to ensure they can send you the title. | N | N 

Payment Object Key | Description                                          | Required ACH | Required Check
-------------- |------------------------------------------------------|--------------| -----------------------
paymentAmount | `number` : Amount in USD$                            | Y            | Y
type | `string` : "ACH" or "Check"                          | Y            | Y
speed | `string` : "Standard" or "ASAP" (defaults to "ASAP") | N            | N
achAccountNumber | `string` : Account Number for ACH Transfer           | Y            | N
achRoutingNumber | `string` : Routing Number for ACH Transfer           | Y            | N
mailingAddress | `object` :  Mailing Address for Checks (see below)   | N            | Y

Title Transfer Object Key | Description                                                                                                                                                   | Required
-------------- |---------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------
businessName | `string` :  If `null` we will auto-populate the company name on your account when sent to lender                                                              | N            
requesterName | `string` : Contact for the title transfer                                                                                                                     | Y            
requesterPhoneNumber | `string` : Phone number for the contact                                                                                                                       | Y            
mailingAddress | `object` :  Shipping address to mail the title to. includes our standard address nested parameters: `attn`, `streetAddress`, `streetAddress2`, `city`, `state`, `zipCode` | Y            


Mailing Address Object Key | Description                                              | Required
-------------- |----------------------------------------------------------|--------------
attn | `string` : Optional attention line                       | N            
streetAddress | `string`                                                 | Y            
streetAddress2 | `string`                                                 | N            
city | `string`                                                 | Y            
state | `string`                                                 | Y           
zipCode | `string`                                                 | Y            
message | `string` : Optional message to be included (checks only) | N            


## How to test
- See section [Testing](#testing) where order type is “Send Payment”, this will give you an automated webhook response that replicates what you would receive in production. 

## How to cancel, limits

- **Cutoff times** to cancel a send payment. After this, the money is in transit so we are unable to retrieve the funds.
  - ACH, until 16:00 CST day requested
  - Check, until 23:00 CST day requested
- Use API routes under [Cancel Order](#cancel-order-request) or [Cancel Packet](#cancel-packet-request)

## Webhook responses

- Initial creation response is exact same as current [Create Order Request](#create-order-s-request).

> Example webhook response for cancellation:

```json
{
  "packetId": "0d7a7a8b-416a-451f-8ce6-8d63aaa88c50",
  "orderId": "6bf2e359-6a49-4a76-a0f1-426629f15e61",
  "orderType": "Send Payment",
  "success": false,
  "status": "cancelled",
  "errorMessage": "Order cancelled",
  "timestamp": "2023-06-07T16:55:45.818Z"
}

```

> Example webhook response for success:

```json
{
  "packetId": "17ad5f32-4429-4bd2-b042-086ad39d2024",
  "orderId": "9aab793b-c6ac-441c-8754-25cfa408bd77",
  "orderType": "Send Payment",
  "success": true,
  "status": "fulfilled",
  "timestamp": "2023-06-14T16:05:12.141Z"
}

```

> Example response for ach “returned” order:

```json
{
  "packetId": "46cbe3dc-3b20-4691-b0c1-1ca90283dbe9",
  "orderId": "b6cb95cf-d003-4827-8237-7210fb688b4e",
  "orderType": "Send Payment",
  "success": false,
  "status": "returned",
  "errorMessage": "Transfer failed - Insufficient Funds",
  "timestamp": "2023-06-06T20:08:55.445Z"
}

```

> Example failure response (problem creating transfer):

```json
{
  "packetId": "150d4148-f1ef-4db7-83ea-a6d018bc76b9",
  "orderId": "936f720b-c169-452b-b71c-30c9bcd9e6c5",
  "orderType": "Send Payment",
  "success": false,
  "errorMessage": "Transfer failed",
  "timestamp": "2023-06-06T20:46:20.587Z"
}

```

> Example “pending” status:

```json
{
  "packetId": "2e73c586-1e43-4b9d-a635-34f7bf8bf16d",
  "orderId": "9dddb85a-2677-4364-af5b-01b4de41d76d",
  "orderType": "Send Payment",
  "success": true,
  "status": "pending",
  "trackingNumber": "9400100000000000000000",
  "timestamp": "2023-06-06T19:37:39.608Z"
}
```
