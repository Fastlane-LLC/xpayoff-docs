# Orders

## Fetch Order Types

> Fetch Order Types Request Example Response Body

```json
{
  "orderTypes": ["...array of current order types"],
  "success": true
}
```

This route returns an array of strings, which consists of the current types of orders that can be requested.
### HTTP Request

`GET https://{YOUR_BASE_URL}/orders/order-types`

Order Type | Description                      | 
----------|------------------------|
Payoff Request | Information regarding a consumer's vehicle loan
Bill of Sale | Digital copy
Finance/Lease Agreement | Digital copy
Lien Release Letter | Digital copy
One and the Same Letter | Digital copy
Payment History | Digital copy
Repo Affidavit | Digital copy
Title Image | Digital copy
Payment Status | Inform lender a payoff has been mailed, and the status of its delivery
Title Status | Request lender to mail the title


## Create Order(s) Request

> Create Order(s) Request Example Response Body:

```json
{
  "packetId": "c30ae9da-9222-4de5-81fe-fe1ac590fa0f",
  "orders": [
    {
      "type": "Payoff Request",
      "orderId": "3e8c38f3-f4ef-4414-88da-1793d25ef6f0",
    }, 
    {
      "orderId": "77462941-7b6c-4bd0-9749-588c32654864",
      "type": "Bill of Sale"
    }
  ],
  "success": true
}
```

You can use this route to make a loan payoff request as well as additional order types if subscribed to xData. Please note that although not every field is required, our query success rate increases with the more data provided to us.

Please note that if you are subscribed to our Fulfillment Center, high success rates require as much information as possible.

### HTTP Request

`POST https://{YOUR_BASE_URL}/orders`

### Request Body

This route accepts a JSON payload of an object comprising of:

Body Parameter | Description                                                                                                                                                                                 | Required? | Fulfillment Center Only
-------------- |---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------| --------- | -----------------------
accountNumber | The account number for the loan; we may not be able to obtain payoffs without this in some cases, ex: `"123456"`                                                                             | N | N
customerName | The name of the customer for the loan                                                                                                                                                       | N | N
customerAddress | The address of the customer for the loan                                                                                                                                                    | N | N
customerBirthdate | The birthdate of the customer for the loan, ex: `"YYYY-MM-DD"`                                                                                                                              | N | N
customerPhoneNumber | The phone number of the customer for the loan, ex: `"2125321234"`                                                                                                                           | N | N
entityName | The name of the organization, dealership, lender, or other entity that is requesting the payoff information for their own purposes. This must be the organization that will act on the data. | Y | N
financeType | How the loan was financed, can be one of: `"Retail"` or `"Lease"` (Lease finance type is not supported at this time)                                                                         | N | N
lenderId | The LossExpress Lender UUID. You can receive this UUID by utilizing our lender search functionality                                                                                         | Y | N
lenderName | A lender name, if a corresponding lender is not in our lender database; this will enter the lender into our queue for validation. Use lenderId whenever possible                            | Y | Y
requesterEmailAddress | The email address of the user requesting the data                                                                                                                                           | N | Y
requesterName | The name of the user requesting the data                                                                                                                                                    | N | Y
requesterPhoneNumber | The phone number of the user requesting the data                                                                                                                                            | N | Y
socialSecurityNumber | The customer's social security number, can be full or the last four, ex: `"434540594"` (can include hyphens) or `"1234"`                                                                    | N | N
vehicleMileage | The customer's mileage on the vehicle, must be a number value, ex: `25000`                                                                                                                  | N | N
vin | Vehicle Identification Number                                                                                                                                                               | Y | N
types | An array of the types of orders for the request                                                                                                                                             | Y | Y 

**Note: Some lenders require Account Number and/or Social Security Number to successfully return the payoff.**

## Cancel Order Request

> Cancel Order Request Example Response Body:

```json
{
  "success": true,
  "orders": [
    {
      "orderId": "d7dfb949-073b-4424-8c7e-195f6f08352c",
      "orderType": "Bill of Sale",
      "status": "cancelled"
    }
  ]
}
```

This route allows cancellation of a particular order in event that it is no longer needed. 

### HTTP Request

`DELETE https://{YOUR_BASE_URL}/orders/cancel-order/{orderId}`

### URL Parameters

Parameter | Description
--------- | -----------
orderId | The LossExpress UUID associated with the order to be cancelled

## Cancel Packet Request

> Cancel Packet Request Example Response Body:

```json
{
  "success": true,
  "orders": [
    {
      "orderId": "d932d1f0-656b-45d6-ae27-1e0904523b3f",
      "type": "Payoff Request",
      "status": "cancelled"
    },
    {
      "orderId": "45581b4d-4849-45ed-adc2-fdb7a333486c",
      "type": "Bill of Sale",
      "status": "cancelled"
    }
  ]
}
```

This route allows cancellation of an entire request, which will subsequently cancel all orders related to a packet.

### HTTP Request

`DELETE https://{YOUR_BASE_URL}/orders/{packetId}`

### URL Parameters

Parameter | Description
--------- | -----------
packetId | The LossExpress UUID associated with the packet to be cancelled
