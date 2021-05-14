# Loan Payoff Request

> This route returns the following object:

```json
{
  "payoffId": "98efa558-4f62-4917-9aff-76ed0c4effa8",
  "success": true
}
```

> Note the `payoffId` returned, as it is a completely unique identifier for the request that will help you reconcile returns sent via the webhook.

You can use this route to make a loan payoff request. Please note that although not every field is required, our query success rate increases with the more data provided to us.

### HTTP Request

`POST https://xpayoff-api.lossexpress.com/payoff`

### Request Body

This route accepts a JSON payload of an object comprising of:

Body Parameter | Description | Required?
-------------- | ----------- | ---------
accountNumber | The account number for the loan; we may not be able to obtain payoffs without this in some cases | N
customerName | The name of the customer for the loan | N
entityName | The name of the organization, dealership, lender, or other entity that is requesting the payoff information for their own purposes. This must be the organization that will act on the data. | Y
financeType | How the loan was financed, can be one of: `Retail` or `Lease` | N
lenderId | The LossExpress Lender UUID. You can receive this UUID by utilizing our lender search functionality | Y
requesterEmailAddress | The email address of the user requesting the data | N
requesterName | The name of the user requesting the data | N
requesterPhoneNumber | The phone number of the user requesting the data | N
vin | Vehicle Identification Number | Y
