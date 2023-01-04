---
title: LossExpress xPayoff API

includes:
  - lenders
  - orders
  - payoff_request
  - errors

search: true

code_clipboard: true
---

# Introduction

Welcome to the LossExpress xPayoff API! You can utilize this API to get pertinent information regarding a consumer's vehicle loan.

# Updates

**2023-01-04**
- New `Account is delinquent` fulfillment center error message added in [Webhook Body](#webhook-body)

**2022-08-24**

- New `company` key added to standard and overnight addresses in [Webhook Body](#webhook-body)
- New `ownerName`, `coOwnerName`, and `titledState` keys added in [Webhook Body](#webhook-body)


**2022-08-16**

- New `maxAttempts` boolean added to [Webhook Body](#webhook-body)

**2022-08-01**

- New [Test VIN](#testing) for example payoff request errors

**2022-06-29**

- Adds [orders](#orders) for xData clients
- New [cancel routes](#cancel-order-request) for payoff requests and orders
- New [Test VINs](#testing) for orders

# Testing

Depending on the configuration of your instance, please use one of the following as your base URL:

Production URL | Development URL                   | 
----------|---------------------------------|
`https://xpayoff-api.lossexpress.com/` | `https://xpayoff-api-dev.lossexpress.com/` 

The following Test VINs can be used on both instances when making either a payoff request or xData order. 
See [Loan Payoff Request](#loan-payoff-request), [Create Order(s) Request](#create-order-s-request), and [Webhook Body](#webhook-body) for more details.

Test VIN | Outcome                                          | 
----------|--------------------------------------------|
`1FTMF1CB6JKE12626` | Automated Payoff Quote 
`1N4AA5AP8EC474073` | Automated Payoff Quote 
`5FNYF4H46CB077722` | Automated Payoff Quote 
`3LNHL2JC5CR800827` | Automated Payoff Quote 
`ZA9RU31B9XLA12448` | Automated Payoff Quote 
`1N4AL3AP8JC231503` | Automated Payoff Quote
`DX4AH51N1K6437778` | Payoff Quote Error Example
`DX3AA08D68N901917` | 'Title Status' Webhook (xData only)
`DXHDU4AD4AU955646` | 'Payment Status' Webhook (xData only)
`DXWRA69M74M033915` | Document Order Webhook ('Lien Release Letter', 'Finance/Lease Agreement', 'Title Image', 'Bill of Sale', 'Repo Affidavit', 'One and the Same Letter', 'Payment History') (xData only)

# Authentication

Our xPayoff API utilizes a simple API key-based authentication, where the key (generated from our developer interface) must be passed in the Authorization header like so:

`Authorization: Bearer exampletokenbutreplacewithyourown`

# Receiving Information (Webhooks)

We send any payoff information we receive for any requests you make as a POST call to any HTTPS endpoint you specify within our system. This section details what you can expect with webhooks within LossExpress xPayoff API.

Please note that you'll be able to manage your webhooks within the developer interface, and that you _must_ have a webhook enabled in order to make payoff requests.

## General Information

Whenever we receive payoff information (almost always within seconds of an initial request), we send a POST request to a URL that has been designated by you within our system.

Your URL must be publicly accessible (or at least accessible by our servers), and must accept the request, responding with a 200 HTTP status code.

We will deactivate any webhooks (as well as corresponding payoff requests) for any account that sends non-200 HTTP status codes in response to our requests to the URL five times in a row, or ten times in a 15 minute period of time.

## Signed Request

Every request to a webhook will be signed by LossExpress, with the request containing a `X-LossExpress-Signature` header that will contain an SHA256 HMAC that is generated using the following string:

`STRINGIFIED_REQUEST_BODY:PAYOFF_ID`

Where STRINGIFIED_REQUEST is the JSON of the request body (with no padding/whitespace) and PAYOFF_ID is the payoff ID for the particular request. This signature is signed with your API key and can be used to validate that the request came from LossExpress.

## Webhook Body

> Example "Payoff Request" Webhook Body:

```json
{
  "packetId": "f94bbd89-e9e0-45ac-a02b-e4d4afad6a8f",
  "payoffId": "f94bbd89-e9e0-45ac-a02b-e4d4afad6a8f",
  "orderId": "61951010-d4a0-48b3-86b6-3908da2b0800",
  "orderType": "Payoff Request",
  "accountNumber": "12345678",
  "ownerName": "Jane Doe",
  "coOwnerName": "John Doe",
  "titledState": "CO",
  "payoffAmount": 10222.33,
  "perDiem": 2.33,
  "validThroughDate": "2020-08-22T00:00:00.000Z",
  "lenderPhoneNumber": "+12223334444",
  "checkPayableTo": "Jane Doe",
  "standardMailingAddress": {
    "company": "Standard Lender",
    "attn": "John Doe",
    "streetAddress": "1000 Main Street",
    "streetAddress2": "Suite 900",
    "city": "Dallas",
    "state": "TX",
    "zipCode": "75204"
  },
  "overnightMailingAddress": {
    "company": "Overnight Lender",
    "attn": "Jeremy Doe",
    "streetAddress": "2000 Main Street",
    "streetAddress2": "Suite 450",
    "city": "Dallas",
    "state": "TX",
    "zipCode": "75204"
  },
  "success": true,
  "fulfilledBy": "integration",
  "customerAuthorizationAttempted": true
}
```

> Example Document Order Type Webhook Body:

```json
{
  "packetId": "a18854a1-ee1c-4ce5-817e-4307ae029894",
  "orderType": "Bill of Sale",
  "orderId": "467eaac4-9a34-480c-85e4-b8da79184a2f",
  "documentUrl": "https://{YOUR_BASE_URL}/documents/f02e4050-6596-46ed-bf1e-0456403964af",
  "success": true,
  "fulfilledBy": "fulfillment center",
  "customerAuthorizationAttempted": false
}
```

> Example "Payment Status" Webhook Body:

```json
{
  "packetId": "29782bd1-3d7c-43db-ace9-1182ee65a29a",
  "orderType": "Payment Status",
  "orderId": "6eb55f20-4d2b-4bd4-8f9c-271842f2852c",
  "paymentReceived": true,
  "dateReceived": "2022-05-12",
  "paymentDeposited": true,
  "dateDeposited": "2022-05-13",
  "paymentMailingAddress": {
    "streetAddress": "P.O. Box 660443",
    "city": "Dallas",
    "zipCode": "75265",
    "state": "TX"
  },
  "success": true,
  "fulfilledBy": "fulfillment center",
  "customerAuthorizationAttempted": false
}
```

> Example "Title Status" Webhook Body:

```json
{
  "packetId": "29782bd1-3d7c-43db-ace9-1182ee65a29a",
  "orderType": "Title Status",
  "orderId": "85a4796f-a970-4968-a5ff-77b5b4410dab",
  "trackingNumber": "1ZR524150396191339",
  "titleSent": true,
  "dateSent": "2022-05-11",
  "titleDeliveryScheduled": "2022-05-12",
  "deliveryType": "UPS",
  "titleAddress": {
    "streetAddress": "P.O. Box 660443",
    "city": "Dallas",
    "zipCode": "75265",
    "state": "TX"
  },
  "success": true,
  "fulfilledBy": "fulfillment center",
  "customerAuthorizationAttempted": false
}
```

> Example "Attempt" Webhook Body:

```json
{
  "packetId": "38d3fb91-302d-4fd8-89d4-a9a6c63b41f4",
  "orderType": "Payoff Request",
  "orderId": "ddc7b596-d6f7-4c82-9bd5-90e9aeefe72e",
  "attempt": true,
  "status": "pending",
  "errorMessage": "Integration failed",
  "customerAuthorizationAttempted": false
}
```

The body of the request sent to the webhook will be a JSON object with the following possible keys:

| Key                            | Description                                                                                                                                                         | Example                                |
|--------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------|
| packetId                       | The LossExpress UUID (A `packet` is a grouping of orders)                                                                                                           | `"f94bbd89-e9e0-45ac-a02b-e4d4afad6a8f"` |
| payoffId                       | The LossExpress UUID (Same as above - will be deprecated in future)                                                                                                 | `"f94bbd89-e9e0-45ac-a02b-e4d4afad6a8f"` |
| orderId                        | The LossExpress UUID for the specific order                                                                                                                         | `"61951010-d4a0-48b3-86b6-3908da2b0800"` |
| orderType                      | The type of order that has been requested                                                                                                                           | `"Payoff Request"`                       |
| ownerName                      | The vehicle owner's name                                                                                                                                            | `"Payoff Request"`                       |
| coOwnerName                    | The vehicle co-owner's name                                                                                                                                         | `"Payoff Request"`                       |
| titledState                    | The Titled State of the vehicle                                                                                                                                     | `"Payoff Request"`                       |
| payoffAmount                   | A number containing the amount remaining on the loan                                                                                                                | `10222.33`                             |
| perDiem                        | The per diem for the particular loan                                                                                                                                | `2.33`                                 |
| validThroughDate               | A JSON timestamp for the valid through date                                                                                                                         | `"2020-08-22T00:00:00.000Z"`             |
| lenderPhoneNumber              | A valid phone number for the lender                                                                                                                                 | `"+12223334444"`                         |
| checkPayableTo                 | To whom the check is payable to                                                                                                                                     | `"+12223334444"`                         |
| standardMailingAddress         | Object containing a standard mailing address for the lender, can contain the following keys: `company, attn, streetAddress, streetAddress2, city, state, zipCode`   |
| overnightMailingAddress        | Object containing an overnight mailing address for the lender, can contain the following keys: `company, attn, streetAddress, streetAddress2, city, state, zipCode` |
| accountNumber                  | Contains the account number for the account as provided by the lender                                                                                               |
| eftAccountNumber               | An account number for EFT                                                                                                                                           |
| eftRoutingNumber               | A routing number for EFT                                                                                                                                            |
| eftState                       | The state, as it relates to EFT                                                                                                                                     | `"TX"`                                   |
| eftCity                        | The city, as it relates to EFT                                                                                                                                      | `"DALLAS"`                               |
| eftZipCode                     | The ZIP code, as it relates to EFT                                                                                                                                  | `"75204"`                                |
| success                        | A boolean, stating whether we were able to retrieve the payoff or not                                                                                               | `true`                                 |
| errorMessage                   | If success is false, we will attempt to provide an error message                                                                                                    |
| fulfilledBy                    | Can be `"integration"` or `"fulfillment center"`, depending on how the payoff was fulfilled                                                                         | `"integration"`                          |
| customerAuthorizationAttempted | Can be `true` or `false`, depending on whether verbal customer authorization was utilized to retrieve the payoff. Disabled at the company level by default.         | `false`                                |
| status                         | The current status for an order when receiving an Customer Authorization attempt webhook (attempts must be enabled)                                                 | `"pending"`                              |
| attempt                        | Will return `true` if the webhook received is a Customer Authorization attempt (attempts must be enabled)                                                           
| trackingNumber                 | The tracking number for a mailed Title Status request                                                                                                               | `"340-7-52607790A-PKG1OF1"`
| titleSent                      | Can be `true` or `false` depending on whether Title Status has been mailed                                                                                          | `true`
| dateSent                       | The date sent for a mailed Title Status request                                                                                                                     | `"2022-05-19"`
| titleDeliveryScheduled         | The schedule delivery date for a mailed Title Status request                                                                                                        | `"2022-05-21"`
| titleAddress                   | An object consisting of `streetAddress`, `city`, `state`, `zipCode` for a Title Status request                                                                      |
| paymentReceived                | Can be `true` or `false` depending on whether payment is received on a Payment Status request                                                                       | `true`
| dateReceived                   | The date received on a Payment Status request                                                                                                                       | `"2022-05-12"`
| dateDeposited                  | The date deposited on a Payment Status request                                                                                                                      | `"2022-05-12"`
| paymentDeposited               | Can be `true` or `false` depending on whether the payment was deposited on a Payment Status Request`                                                                | `true`
| paymentMailingAddress          | An object consisting of `streetAddress`, `city`, `state`, `zipCode` for a Payment Status request                                                                    |
| documentUrl                    | The URL that can be used as a `GET` request to retrieve your requested digital document                                                                             | `"https://{YOUR_BASE_URL}/documents/f02e4050-6596-46ed-bf1e-0456403964a"`
| maxAttempts                    | A boolean set to `true` if no more attempts are available on the order to be fulfilled                                                                              |`true`


<aside class="notice">
  Only `payoffId`, `packetId`, and `success` are guaranteed to be in any given webhook body sent.
</aside>

### Error Messages (Fulfillment Center)

Below are the current list of messages that could be returned when we run into an issue gathering a payoff via our fulfillment center:

- Account is in title perfection
- Account number required
- Account is a lease
- Account is delinquent
- Call dropped
- Customer authorization required
- Customer would not authorize
- Date of birth required
- Lender would not release to a third party
- Loan has been paid off
- Required to be sent via email
- Required to be sent via fax
- Social Security number required
- Social Security number required (last 4)
- Unable to find account
- Unable to verify lender
- Wait time too long
- Wrong email
- Wrong fax
- Wrong phone number
