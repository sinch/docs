---
title: Get your virtual numbers
excerpt: 'Get, move and update your virtual numbers using the API calls shown here. '
hidden: false
---

## List Numbers

This GET request lists all virtual numbers for your project. 

#### Request

```shell
curl --request GET \
 --url 'https://numbers.api.sinch.com/v1alpha1/projects/{project_id}/activeNumbers' \
 --header 'Accept: application/json'
 --u {clientId}:{clientSecret}
```

## List Numbers in US

This GET request lists all US virtual numbers for your project.

#### Request

```shell
curl --request GET \
 --url 'https://numbers.api.sinch.com/v1alpha1/projects/{project_id}/activeNumbers?regionCode=US' \
 --header 'Accept: application/json'
 --u {clientId}:{clientSecret}
```

#### Response

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

This PATCH request allows you to move a virtual number between different SMS services and give the number a new friendly name.

#### Request

```shell
curl --request PATCH \
 --url https://numbers.api.sinch.com/v1alpha1/projects/projectId/activeNumbers/phoneNumber? \
 --header 'Accept: application/json'
```

#### phoneNumber in E.164 format

E.164 is the international telephone numbering plan that guarantees that each device on the PSTN (public switched telephone network) has a globally unique number. This number allows calls and texts to be correctly routed in individual phones in different countries.

The format of an E.164 number, which has a maximum of 15 digits, is [+][country code][subscriber number including area code]

An example of this format is +12025550134. This number may need to be URL encoded to work. For example, %2B12025550134.

#### Update Mask

The updateMask parameter determines which resource fields are modified in an update.

The following fields can be updated:

- displayName
- smsConfiguration.servicePlanId

#### Body

updateMask parameters can be attached in the request body as shown below in this example:

#### Request

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

## Release a virtual number from a project

This POST call allows you to cancel your subscription for a specific virtual number. Remember, the phoneNumber element must follow the E.164 format and may need to be URL encoded as shown above.

#### Request

```shell
curl --request POST \
 --url https://numbers.api.sinch.com/v1alpha1/projects/projectId/activeNumbers/phoneNumber:release \
 --header 'Accept: application/json'
```
