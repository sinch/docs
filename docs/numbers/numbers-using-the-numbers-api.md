---
title: Using the Numbers API
excerpt: >-
  Numbers API
hidden: false
---

This section will show you how to get started to use the Numbers API to search, buy, list, update and release Numbers.Start using your Numbers APIIn this guide, we will show you how to:

1. Get project id, client id and key id
2. Authentication
3. Search for numbers
4. Buy numbers
5. List number(s)
6. Update numbers
7. Release numbers

## Get project id, client id and client key

To get the project id, client id and key id you will need to log into your self-serve account (dashboard.sinch.com) and visit https://dashboard.sinch.com/settings/project-management.

![Project management](images/project_management.png)

The project ID

Then go Access Keys section https://dashboard.sinch.com/settings/access-keys. Here you need to click the “New Key” to generate an client id and key id.

![Access Keys](images/access_keys.png)

Make sure to write down the key since it will not be possible to retrieve after creation

## Authentication

#### Basic

Our basic security works with any project, its the client_id and client_secret that you can find in the dasbhoard.
Learn more about Sinch authentication and authorization, Security Scheme Type HTTP, HTTP Authorization Scheme basic

#### Oauth

Our basic security works with any project, its the client_id and client_secret that you can find in the dasbhoard This is the recomended way of to access our apis.

Security Scheme Type OAuth2
clientCredentials OAuth Flow Token URL: https://eu.auth.sinch.com/oauth2/token

Revoke URL: https://eu.auth.sinch.com/oauth2/revoke

## Search numbers

Search for numbers that are available for you to buy. You can filter by any property on the available number resouce.

```shell
curl --request GET \
 --url 'https://numbers.api.sinch.com/v1alpha1/projects/projectId/availableNumbers?numberPattern.searchPattern=SEARCH_PATTERN_UNSPECIFIED&type=NUMBER_TYPE_UNSPECIFIED&capability=NUMBER_CAPABILITY_UNSPECIFIED' \
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

#### Response

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

## Rent a number

Rent a number to use with SMS or Voice products

```shell
curl --request POST \
 --url https://numbers.api.sinch.com/v1alpha1/projects/projectId/availableNumbers/phoneNumber:rent \
 --header 'Accept: application/json' \
```

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
