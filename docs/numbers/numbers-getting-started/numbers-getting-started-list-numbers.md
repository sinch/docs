---
title: Working with your numbers
excerpt: ''
hidden: false
---

## List Numbers

Lists all your numbers.  

```shell
curl --request GET \
 --url 'https://numbers.api.sinch.com/v1alpha1/projects/{project_id}/activeNumbers' \
 --header 'Accept: application/json'
 --u {clientId}:{clientSecret}
```

## List Numbers in US

Lists all US numbers for a project.


```shell
curl --request GET \
 --url 'https://numbers.api.sinch.com/v1alpha1/projects/{project_id}/activeNumbers?regionCode=US' \
 --header 'Accept: application/json'
 --u {clientId}:{clientSecret}
```

#### Result

```json
{
  "activeNumbers": [
    {
      "phoneNumber": "+12086390939",
      "projectId": "0994c893-4d74-4455-8259-4f5c68d92f6f",
      "displayName": "",
      "regionCode": "US",
      "type": "LOCAL",
      "capability": ["SMS"],
      "money": {
        "currencyCode": "USD",
        "amount": "2.0"
      },
      "paymentIntervalMonths": 1,
      "nextChargeDate": "2020-11-29T09:54:20.516Z",
      "expireAt": "2020-11-29T09:54:20.516Z",
      "smsConfiguration": {
        "servicePlanId": "957ae16ae12d4942b91b61eda5e4ed6b",
        "scheduledProvisioning": null
      }
    }
  ]
}
```

## Update Number

With update number you can move a number between different SMS services and give it a new friendly name.

```shell
curl --request PATCH \
 --url https://numbers.api.sinch.com/v1alpha1/projects/projectId/activeNumbers/phoneNumber? \
 --header 'Accept: application/json'
```

#### phoneNumber

The phone number in e.164 format with leading +. Example +12025550134. This may need to be URL encoded to work. Example %2B12025550134

#### Update Mask

updateMask that determines which resource fields are modified in an update. Following fields can be updated:

- displayName
- smsConfiguration.servicePlanId

#### Body

updateMask parameters can be attached in the request body like this:

```json
{
  "smsConfiguration": {
    "servicePlanId": "957ae16ae12d4942b91b61eda5e4ed6b"
  }
}
```

#### Response

```json
{
  "phoneNumber": "+12092224786",
  "projectId": "0994c893-4d74-4455-8259-4f5c68d92f6f",
  "displayName": "",
  "regionCode": "US",
  "type": "LOCAL",
  "capability": ["SMS"],
  "money": {
    "currencyCode": "USD",
    "amount": "2.0"
  },
  "paymentIntervalMonths": 1,
  "nextChargeDate": "2020-11-30T10:32:08.882Z",
  "expireAt": "2020-11-30T10:32:08.882Z",
  "smsConfiguration": {
    "servicePlanId": "957ae16ae12d4942b91b61eda5e4ed6b",
    "scheduledProvisioning": {
      "servicePlanId": "957ae16ae12d4942b91b61eda5e4ed6b",
      "status": "IN_PROGRESS",
      "lastUpdatedTime": "2020-11-02T12:09:59.876Z"
    }
  }
}
```

## Release number from project

Cancel your subscription for a specific phone number.

```shell
curl --request POST \
 --url https://numbers.api.sinch.com/v1alpha1/projects/projectId/activeNumbers/phoneNumber:release \
 --header 'Accept: application/json'
```

#### phoneNumber

The phone number in e.164 format with leading +. Example +12025550134. This may need to be URL encoded to work. Example %2B12025550134
