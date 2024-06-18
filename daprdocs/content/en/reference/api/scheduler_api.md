---
type: docs
title: "Scheduler API reference"
linkTitle: "Scheduler API"
description: "Detailed documentation on the scheduler API"
weight: 1300
---

{{% alert title="Note" color="primary" %}}
Scheduler is currently in alpha.
{{% /alert %}}

With the Scheduler API, you can orchestrate jobs and tasks in your environment.

## Schedule a job

Schedule a job with the given name.

```
POST http://localhost:3500/v1.0-alpha1/job/schedule/<name>
```

### URL parameters

Parameter | Description
--------- | -----------
`name` | Job name

### Request content

Any request content will be passed to the scheduler as input. The Dapr API passes the content as-is without attempting to interpret it.

### HTTP response codes

Code | Description
---- | -----------
`202`  | Accepted
`400`  | Request was malformed
`500`  | Request formatted correctly, error in dapr code or Scheduler control plane service

### Response content

The API call will provide a response similar to this:

```json
{
  "job": {
      "data": {
          "@type": "type.googleapis.com/google.type.Expr",
          "expression": "cassie87"
      },
      "dueTime": "30s"
  }
}
```

## Get job data

Get a job's data with its given name.

```
GET http://localhost:3500/v1.0-alpha1/job/<name>
```

### URL parameters

Parameter | Description
--------- | -----------
`name` | Job name

### HTTP response codes

Code | Description
---- | -----------
`202`  | Accepted
`400`  | Request was malformed
`500`  | Request formatted correctly, error in dapr code or Scheduler control plane service

### Response content

The API call will provide a response similar to this:

```json
{
  "name":"test1",
  "dueTime":"30s",
  "data": {
     "@type":"type.googleapis.com/google.type.Expr",
     "expression":"expression1"
   }
}                                    
```
## Delete a job

Delete a job you've created and scheduled with its given name.

```
DELETE http://localhost:3500/v1.0-alpha1/job/<name>
```

### URL parameters

Parameter | Description
--------- | -----------
`name` | Job name

### HTTP response codes

Code | Description
---- | -----------
`202`  | Accepted
`400`  | Request was malformed
`500`  | Request formatted correctly, error in dapr code or Scheduler control plane service

### Response content

None.

## Next steps

[Scheduler API overview]({{< ref scheduler-overview.md >}})