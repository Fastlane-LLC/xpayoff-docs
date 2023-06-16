# Payments

## How to create
- Individual request: To create new order type `Send Payment` by itself use [Create Order(s) Request](#create-order-s-request)

- Add to existing packet: To add a new order type `Send Payment` to an existing `packet` use [Add Order to Packet](#add-order-to-packet), must include the `packetId` you want to associate the new order with in query parameters.

### Request Body

This route accepts a JSON payload of an object comprising of:

Body Parameter | Description                                                                                                                                                                                | Required? | Fulfillment Center Only
-------------- |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------| --------- | -----------------------
lenderId | The LossExpress Lender UUID. You can receive this UUID by utilizing our lender search functionality                                                                                        | Y | N
lenderName | A lender name, if a corresponding lender is not in our lender database; this will enter the lender into our queue for validation. Use lenderId whenever possible                           | Y | Y
vin | Vehicle Identification Number                                                                                                                                                              | Y | N
types | An array of the types of orders for the request                                                                                                                                            | Y | Y 
payment | An object required for 'Send Payment' order type. Includes `paymentAmount`, `type`, `speed`, `achRoutingNumber`, `achAccountNumber`, `mailingAddress`                                      | Y | N

## How to test
- See section [Testing](#testing) where order type is “Send Payment”, this will give you an automated webhook response that replicates what you would receive in production. 
