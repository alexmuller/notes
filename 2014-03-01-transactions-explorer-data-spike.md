# Transactions Explorer data spike: March 2014

## Code

We have some Python that generates two different types of data from a Google Spreadsheet:

```json
{
  "_id": "base64(_start_at, _end_at, type, service-id)",
  "_start_at": "YYYY-MM-01T00:00:00+00:00",
  "_end_at": "YYYY-MM-01T00:00:00+00:00",
  "type": "quarterly",
  "service-id": "???????",
  "number-of-transactions": 123,
  "number-of-digital-transactions": 456
}
```

```json
{
  "_id": "base64(_start_at, _end_at, type, service-id)",
  "_start_at": "YYYY-MM-01T00:00:00+00:00",
  "_end_at": "YYYY-MM-01T00:00:00+00:00",
  "type": "seasonally-adjusted",
  "service-id": "???????",
  "number-of-transactions": 123,
  "number-of-digital-transactions": 456,
  "cost-per-transaction": 9,
  "cost-per-digital-transaction": 8
}
```

This array will contain 770 services &times; 2 different types of data &times; 5 quarters = 7700 elements.

Data is `POST`ed as `null` if it contains an empty string, a hyphen/dash or a not requested marker (`***`).

## Issues and questions

### Storing the two different types of data

Should these go in the same bucket or two buckets?

### Uniquely identifying a service

How should we uniquely identify a service? We have the department, the service name, and sometimes a mainstream slug.

### Non-requested data

Occassionally we mark data as `***` indicating it was not requested from a department. I believe
this is different to a department not providing data.

Will we need to show this differentiation on a graph? If so, should we change the way non-requested data is
stored from `null` to something else?

Maybe this is a problem we should solve later.
