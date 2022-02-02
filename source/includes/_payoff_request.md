# Loan Payoff Request

## Create Request

> This route returns the following object:

```json
{
  "payoffId": "98efa558-4f62-4917-9aff-76ed0c4effa8",
  "success": true
}
```

> Note the `payoffId` returned, as it is a completely unique identifier for the request that will help you reconcile returns sent via the webhook.

You can use this route to make a loan payoff request. Please note that although not every field is required, our query success rate increases with the more data provided to us.

Please note that if you are subscribed to our Fulfillment Center, high success rates require as much information as possible.

### HTTP Request

`POST https://{YOUR_BASE_URL}/payoff`

### Request Body

This route accepts a JSON payload of an object comprising of:

Body Parameter | Description | Required? | Fulfillment Center Only
-------------- | ----------- | --------- | -----------------------
accountNumber | The account number for the loan; we may not be able to obtain payoffs without this in some cases | N | N
customerName | The name of the customer for the loan | N | N
customerAddress | The address of the customer for the loan | N | N
customerBirthdate | The birthdate of the customer for the loan | N | N
customerPhoneNumber | The phone number of the customer for the loan | N | N
entityName | The name of the organization, dealership, lender, or other entity that is requesting the payoff information for their own purposes. This must be the organization that will act on the data. | Y | N
financeType | How the loan was financed, can be one of: `Retail` or `Lease` | N | N
lenderId | The LossExpress Lender UUID. You can receive this UUID by utilizing our lender search functionality | Y | N
lenderName | A lender name, if a corresponding lender is not in our lender database; this will enter the lender into our queue for validation. Use lenderId whenever possible | Y | Y
requesterEmailAddress | The email address of the user requesting the data | N | Y
requesterName | The name of the user requesting the data | N | Y
requesterPhoneNumber | The phone number of the user requesting the data | N | Y
socialSecurityNumber | The customer's social security number, can be full or the last four | N | N
vehicleMileage | The customer's mileage on the vehicle | N | N
vin | Vehicle Identification Number | Y | N

## Cancel Request

> This route returns the following object:

```json
{
  "payoffId": "98efa558-4f62-4917-9aff-76ed0c4effa8",
  "success": true
}
```

**This route is only available when subscribed to our Fulfillment Center.**

This route can be utilized to cancel an ongoing request in our system.

### HTTP Request

`DELETE https://{YOUR_BASE_URL}/payoff/{payoffId}`

### Request Parameters

Parameter | Description | Required?
----------| ----------- | ---------
payoffId | LossExpress payoff request UUID | Y
