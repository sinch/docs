---
title: Rent a number
excerpt: ''
hidden: false
---

To buy a number you first need to find a number that suites your needs  

## Search for a number

Search for numbers that are available for you to rent. In this example you will search for any US number.

```shell
curl --request GET \
 --url 'https://numbers.api.sinch.com/v1alpha1/projects/{projectId}/availableNumbers?regionCode=US' \
 --header 'Accept: application/json' \
 -u {clientId}:{clientSecret}
```
Replace {projectId}, {clientId} and {clientSecret} with your values. 

You can filter by any property on the available number resource. learn more about it in the [API specification](https://developers.sinch.com/reference#numberservice_listavailablenumbers).  


### Response

```json
{
  "availableNumbers": [
    {
      "phoneNumber": "+12089087284",
      "regionCode": "US",
      "type": "LOCAL",
      "capability": ["SMS"],
      "setupPrice": {
        "currencyCode": "USD",
        "amount": "0.00"
      },
      "monthlyPrice": {
        "currencyCode": "USD",
        "amount": "2.00"
      },
      "paymentIntervalMonths": 1
    }
  ]
}
```
Take a note of the phoneNumber you will need it in the next step. 
## Rent a number

Rent a number to use with SMS or Voice products

```shell
curl --request POST \
 --url https://numbers.api.sinch.com/v1alpha1/projects/projectId/availableNumbers/{phoneNumber}:rent \
 --header 'Accept: application/json' \
 -u {clientId}:{clientSecret}
```
Replace {projectId}, {clientId} and {clientSecret} with your values. 

#### phoneNumber

The phone number in e.164 format with leading +. Example +12025550134. This may need to be URL encoded to work. Example %2B12025550134

#### smsConfiguration

Sms configuration parameters

##### servicePlanId

The service_plan_id can be found when logging into dashboard.sinch.com in SMS>APIs section.

#### Response

```json
{
  "phoneNumber": "+12092224786"
}
```

## List Numbers

Lists all numbers for a project.

```shell
curl --request GET \
 --url 'https://numbers.api.sinch.com/v1alpha1/projects/projectId/activeNumbers?numberPattern.searchPattern=SEARCH_PATTERN_UNSPECIFIED&type=NUMBER_TYPE_UNSPECIFIED&capability=NUMBER_CAPABILITY_UNSPECIFIED' \
 --header 'Accept: application/json'
```

#### searchPattern

Search pattern to apply.

- START: Numbers that start with the provided sequence of digits
- CONTAINS: Numbers that contain the sequence of digits
- END: Numbers that end with the sequence of digits

#### type

Only return numbers with the given type. MOBILE, LOCAL or TOLL_FREE.

- MOBILE: Numbers that belong to a specific range
- LOCAL: Numbers that are assigned to a specific geographic region
- TOLL_FREE: Number that are free of charge for the calling party but billed for all arriving calls

#### capability

Number capability to filter by. SMS and/or VOICE.

- SMS: The SMS product can use the number
- VOICE: The Voice product can use the number

#### regionCode

Only return numbers for the given region code. ISO 3166-1 alpha-2 country code of the phone number. Example US, GB or SE.

#### Size

Optional. The maximum number of items to return.

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
