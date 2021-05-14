> Example Lender Object

```json
{
  "lenderId": "c30ae9da-9222-4de5-81fe-fe1ac590fa0f",
  "lenderName": "Test Lender",
  "aliases": [
    "Test Lender Bank",
    "Bank of Test Lender"
  ]
}
```

The route in this section is meant to be used for providing a robust search solution for the end users of our partners. We do not currently provide an index containing all of our lenders, as that information is frequently updated and added to.

The Lender object itself contains the following keys:

Object Key | Description
---------- | -----------
aliases | An array of strings containing common aliases that have been indexed in LossExpress for this lender
lenderId | The LossExpress UUID for the lender
lenderName | The name of the lender in LossExpress

## Search Lenders

> Example Search Response:

```json
{
  "lenders": [
    ...lender objects,
  ]
}
```

This route allows for searching LossExpress's lender database for matches. We recommend showing a full list of the lenders returned for selection by the end user, while also allowing users to move forward with the text they've entered, in case there is no exact match.

Lenders with aliases that match the text provided are also returned in this query. We recommend displaying information to the user notifying them that the text they've entered is an alias for another lender in this situation.

If a lender is not found in our database, simply pass the lender name in place of a `lenderId` when creating a claim in LossExpress, and our team will add the lender to the database for future requests, if possible.

### HTTP Request

`GET https://exapi.lossexpress.com/lenders`

### Query Parameters

Parameter | Description
--------- | -----------
name | Name being queried against in LossExpress. Must be at least 3 characters long, and must be properly encoded


