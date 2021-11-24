# Errors

> 400 Bad Request Example Error Response:

```json
{
  "statusCode": 400,
  "error": "Bad Request",
  "message": "vin is required",
  "validation": {
    "errors": [
      {
        "key": ["vin"],
        "path": ["vin"],
        "message": "vin is required",
        "type": "any",
        "constraint": "required",
        "label": "vin"
      }
    ]
  }
}
```

> 500 Internal Server Error Example Error Response:

```json
{
  "statusCode": 500,
  "error": "Internal Server Error",
  "message": "An internal server error occurred"
}
```

Our API uses the following error codes:

| Error Code | Meaning                                                                                   |
| ---------- | ----------------------------------------------------------------------------------------- |
| 400        | Bad Request -- Your request is invalid.                                                   |
| 401        | Unauthorized -- Your API key is wrong.                                                    |
| 403        | Forbidden -- You do not have access to that route.                                        |
| 404        | Not Found -- The specified information could not be found.                                |
| 405        | Method Not Allowed -- You tried to access a route with an invalid method.                 |
| 406        | Not Acceptable -- You requested a format that isn't json.                                 |
| 500        | Internal Server Error -- We had a problem with our server. Try again later.               |
| 503        | Service Unavailable -- We're temporarily offline for maintenance. Please try again later. |
