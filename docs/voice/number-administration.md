---
title: Number Administration
excerpt: >-
  Administrate numbers through the same endpoints that is used by the Sinch
  Dashboard. Read more here.
next:
  pages:
    - log-in
---

This document outlines how to administrate numbers through the same endpoints used by the Sinch dashboard. It has two logical steps:

- Starting a session:
  - Logging in
  - Creating an instance
  - Retrieving your account’s organisation data
- Working with the “number” endpoints.
  - Listing available numbers
  - Reserving a quantity of numbers
  - Checking out the reservation
  - [Listing numbers on the account or attached to an application](doc:voice-rest-api-calling-api#get-numbers)
  - [Assigning to an application](doc:voice-rest-api-calling-api#update-numbers)
  - [Unassigning from an application](doc:voice-rest-api-calling-api#un-assign-number)

For general information on how to use the Sinch APIs including methods, types, errors and authorization, please check the [Using REST](doc:using-rest) page.

---

## Log in

First you need to login to retrieve a token that will be used to create an instance.

### Headers

This is a [public resource](doc:using-rest#public-resources).

When logging in, you should always pass in the header the “number administration” key. This is the key that you should pass:

    669762D5-2B10-44E0-8418-BC9EE4457555

> **Note**
>
> Always use this exact key in the log in step. Do not use any of your app keys.

    Authorization: Application 669762D5-2B10-44E0-8418-BC9EE4457555
    x-timestamp: {now}

    eg:
    Authorization: Application 669762D5-2B10-44E0-8418-BC9EE4457555
    x-timestamp: 2015-06-20T11:43:10.944Z

### Request

    [POST]

    URL:
    https://userapi.sinch.com/v1/users/{type}/{identity}/authentication

    eg:
    https://userapi.sinch.com/v1/users/email/address@example.com/authentication

    [body]
        string - password

    eg:

```json
{
  "password": "YOUR_ACCOUNT_PASSWORD"
}
```

### Response

    string - authorization
    User   - user

    eg:

```json
{
  "authorization": "eyJhcHBsaWNhdGlvbktleSI6IllPVVJfQVBQTElDQVRJT05fS0VZIiwiaWRlbnRpdHkiOnsidHlwZSI6ImVtYWlsIiwiZW5kcG9pbnQiOiJhZGRyZXNzQGV4YW1wbGUuY29tIn0sImNyZWF0ZWQiOiIyMDE1LTA2LTI0VDA4OjMyOjMyLjk0MTc2MDVaIn0=:Uc3UQ6tnextCCXiuieizBGNf16SDKFGFWMpu6LKbOwA=",
  "user": {
    "id": 123456789,
    "identities": [
      {
        "type": "email",
        "endpoint": "address@example.com"
      },
      {
        "type": "number",
        "endpoint": "+12055500000"
      }
    ],
    "profile": {
      "name": {
        "first": "Jane",
        "last": "Bloggs",
        "full": "Jane Bloggs"
      },
      "contact": {
        "email": "address@example.com"
      }
    }
  }
}
```

---

## Create an instance

As a second step, you need to create an instance with the ‘authorization’ string received from the authentication response (above).

### Headers

This is a protected resource and requires a [user signed request](doc:using-rest#user-signed-request).

    Authorization: User {authorization}
    x-timestamp: {now}

    eg:
    Authorization: User eyJhcHBsaWNhdGlvbktleSI6IllPVVJfQVBQTElDQVRJT05fS0VZIiwiaWRlbnRpdHkiOnsidHlwZSI6ImVtYWlsIiwiZW5kcG9pbnQiOiJhZGRyZXNzQGV4YW1wbGUuY29tIn0sImNyZWF0ZWQiOiIyMDE1LTA2LTI0VDA4OjMyOjMyLjk0MTc2MDVaIn0=:Uc3UQ6tnextCCXiuieizBGNf16SDKFGFWMpu6LKbOwA=
    x-timestamp: 2015-06-20T11:43:10.944Z

### Request

    [POST]

    URL:
    https://api.sinch.com/v1/instance

[body]
string - authorization
Version - version

[Version]
string - os
string - platform

eg:

```json
{
  "authorization": "eyJhcHBsaWNhdGlvbktleSI6IllPVVJfQVBQTElDQVRJT05fS0VZIiwiaWRlbnRpdHkiOnsidHlwZSI6ImVtYWlsIiwiZW5kcG9pbnQiOiJhZGRyZXNzQGV4YW1wbGUuY29tIn0sImNyZWF0ZWQiOiIyMDE1LTA2LTI0VDA4OjMyOjMyLjk0MTc2MDVaIn0=:Uc3UQ6tnextCCXiuieizBGNf16SDKFGFWMpu6LKbOwA=",
  "version": {
    "os": "{os}",
    "platform": "{platform}"
  }
}
```

> **Note**
>
> ‘version’ data is mandatory, but is up to the client to decide how to populate it. Choose values that best represent your OS and platform combination.

### Response

    [Instance]
        string   - id
        byte[]   - secret
        integer? - expiresIn

    eg:

```json
{
  "id": "00a3ffb1-0808-4dd4-9c7d-e4383d82e445",
  "secret": "bRo76GRddEyetgJDTgkLHA==",
  "expiresIn": 172800
}
```

---

## Get organization

With the instance that you created you can now perform instance signed requests, which are needed for the number administration endpoints. First you should retrieve your organisation Id.

### Headers

This is a protected resource and requires an [instance signed request](doc:using-rest#instance-signed-request).

    Authorization: Instance {instance ID}:{instance signature}
    x-timestamp: {now}

    eg:
    Authorization: Instance 00a3ffb1-0808-4dd4-9c7d-e4383d82e445:bb9i9SGuw8mPspPF6WHzIZxw4yQxdOwGaDliMi+IhCU=
    x-timestamp: 2015-06-20T11:43:10.944Z

### Request

    [GET]

    URL:
    https://api.sinch.com/v1/organisations

    [body]
        empty

    eg:
    {}

### Response

    [Organisation]
        int - id
        string? - name
        string? - website
        string? - countryId
        string? - phone
        string? - vatnumber

eg:

```json
  {
     "id":"8888123",
     "name":"Acme"
     "website":"http://www.example.com"
     "phone":"+12055500000"
     "countryId":"US"
  }
```

---

## Get numbers available to rent

This API returns all available numbers, so that you can pick which ones you want to rent.

### Headers

This is a protected resource and requires an [instance signed request](doc:using-rest#instance-signed-request).

    Authorization: Instance {instance ID}:{instance signature}
    x-timestamp: {now}

    eg:
    Authorization: Instance 00a3ffb1-0808-4dd4-9c7d-e4383d82e445:jYJQFcgc1uh7DO2uQZyLu7rpxOc3jXjcuQNKWiHFJiI=
    x-timestamp: 2015-06-20T11:43:10.944Z

### Request

    [GET]

    URL:
    https://portalapi.sinch.com/v1/organisations/id/{organisationId}/numbers/shop

    eg:
    https://portalapi.sinch.com/v1/organisations/id/8888123/numbers/shop

    [body]
        empty

    eg:

```json
{}
```

### Response

    NumberGroup[]

    [NumberGroup]
        int      - groupId
        string   - country
        string   - countryId
        string   - city
        string   - type
        integer  - availability
        string   - areaCode
        Money    - cost
        string[] - capabilities

    eg:

```json
[
  {
    "groupId": 1,
    "country": "France",
    "countryId": "FR",
    "city": "PARIS",
    "type": "Voice",
    "availability": 7,
    "areaCode": "01",
    "cost": {
      "amount": 2.0,
      "currencyId": "USD"
    },
    "capabilities": ["voice"]
  },
  {
    "groupId": 2,
    "country": "UK",
    "countryId": "GB",
    "city": "LONDON",
    "type": "Voice",
    "availability": 2,
    "areaCode": "020",
    "cost": {
      "amount": 2.0,
      "currencyId": "USD"
    },
    "capabilities": ["voice"]
  },
  {
    "groupId": 75,
    "country": "US",
    "countryId": "US",
    "city": "New Orleans",
    "type": "Sms",
    "availability": 5,
    "areaCode": "504",
    "cost": {
      "amount": 2.0,
      "currencyId": "USD"
    },
    "capabilities": ["sms"]
  }
]
```

### Error Codes

    [Error codes]
        ParameterValidation (40001) - Invalid number type: {NUMBER_TYPE}.  Type must be one of: sms, voice.
        ForbiddenRequest (40300)    - The current user is not the owner of the organisation: {ORGANISATION_ID}
        InvalidScheme (40301)       - The authorization scheme 'instance' is required.

---

## Reserve number(s)

To rent numbers, you must first reserve them and then check out. This endpoint allows reserving the numbers that you would like to rent.

### Headers

This is a protected resource and requires an [instance signed request](doc:using-rest#instance-signed-request).

    Authorization: Instance {instance ID}:{instance signature}
    x-timestamp: {now}

    eg:
    Authorization: Instance 00a3ffb1-0808-4dd4-9c7d-e4383d82e445:a6p7RYw8bMr3JuZh1LArvWTLJjIgCeQj5nsRZaXW7VQ=
    x-timestamp: 2015-06-20T11:43:10.944Z

### Request

    [PUT]

    URL:
    https://portalapi.sinch.com/v1/organisations/id/{organisationId}/numbers/shop

    eg:
    https://portalapi.sinch.com/v1/organisations/id/8888123/numbers/shop

    [body]
        integer  - groupId
        integer  - quantity
        string[] - capabilities

    eg:

```json
{
  "groupId": 13,
  "quantity": 1,
  "capabilities": ["voice", "sms"]
}
```

### Response

    string - referenceId

    eg:

```json
{
  "referenceId": "c8b27491-3379-44f3-af31-fbbfbb2ba510"
}
```

### Error Codes

    [Error codes]
        ParameterValidation (40001) - Invalid capability '{CAPABILITY}'
        MissingParameter (40002)    - No groupIds specified
        InvalidRequest (40003)      - Too many number groups specified.  Unable to complete request.
                                    - Invalid number group: {GROUP_ID}
        ForbiddenRequest (40300)    - The current user is not the owner of the organisation: {ORGANISATION_ID}
        InvalidScheme (40301)       - The authorization scheme 'instance' is required.

---

## Checkout reserved numbers

This endpoints allows you to check out the numbers that you have reserved, so that they are assigned to your account. Please make sure ou have enough credit to rent the numbers.

### Headers

This is a protected resource and requires an [instance signed request](doc:using-rest#instance-signed-request).

    Authorization: Instance {instance ID}:{instance signature}
    x-timestamp: {now}

    eg:
    Authorization: Instance 00a3ffb1-0808-4dd4-9c7d-e4383d82e445:FN0oGGh6UAzdP8WVVwgbhXqUM2KJpHoWgjcgUKpS3bU=
    x-timestamp: 2015-06-20T11:43:10.944Z

### Request

    [POST]

    URL:
    https://portalapi.sinch.com/v1/organisations/id/{organisationId}/numbers/shop

    eg:
    https://portalapi.sinch.com/v1/organisations/id/8888123/numbers/shop

    [body]
        string[] - referenceIds

    eg:

```json
{
  "referenceIds": ["c8b27491-3379-44f3-af31-fbbfbb2ba510"]
}
```

### Response

    204 - No Content

### Error Codes

    [Error codes]
        NotEnoughCredit (40200)         - Insufficient funds to complete the transaction.
        ForbiddenRequest (40300)        - The current user is not the owner of the organisation: {ORGANISATION_ID}
        InvalidScheme (40301)           - The authorization scheme 'instance' is required.
        ResourceNotFound (40400)        - Unable to assign numbers to organization: reservation(s) not found or expired
        ApplicationConfiguration (42200)- Accounting not set up for this organization.

---

## List all rented numbers

This endpoint lists all the numbers that are assigned to your account.

### Headers

This is a protected resource and requires an [instance signed request](doc:using-rest#instance-signed-request).

    Authorization: Instance {instance ID}:{instance signature}
    x-timestamp: {now}

    eg:
    Authorization: Instance 00a3ffb1-0808-4dd4-9c7d-e4383d82e445:PToDaAs7AiJDqGKZHCl3mayEigGWodVfg4fSlkAYLHg=
    x-timestamp: 2015-06-20T11:43:10.944Z

### Request

    [GET]

    URL:
    https://portalapi.sinch.com/v1/organisations/id/{organisationId}/numbers

    eg:
    https://portalapi.sinch.com/v1/organisations/id/8888123/numbers

    [body]
        empty

    eg:
    {}

### Response

    Number[]

    [Number]
        long     - number
        integer  - groupId
        string[] - capabilities
        string?  - applicationKey
        string   - countryId
        string   - city
        string   - country
        string   - areaCode
        string?  - expires
        Money    - cost

    eg:

```json
[
  {
    "number": 14150005550,
    "groupId": 13,
    "capabilities": ["voice"],
    "countryId": "US",
    "city": "SAN FRANCISCO",
    "country": "US",
    "areaCode": "415",
    "expires": "2015-12-31T23:59:59+00:00",
    "cost": {
      "amount": 0.0,
      "currencyId": "USD"
    }
  },
  {
    "number": 14150005551,
    "groupId": 13,
    "capabilities": ["voice"],
    "applicationKey": "bb7b4e39-4227-4913-8c81-2db4abb54fb3",
    "countryId": "US",
    "city": "SAN FRANCISCO",
    "country": "US",
    "areaCode": "415",
    "expires": "2015-12-31T23:59:59+00:00",
    "cost": {
      "amount": 0.0,
      "currencyId": "USD"
    }
  }
]
```

If some numbers are already assigned to your app, then the application key will also appear in the response for those numbers.

### Error Codes

    [Error Codes]
        InvalidScheme (40301)       - The authorization scheme 'instance' is required.
        ForbiddenRequest (40300)    - The current user is not the owner of the organisation: {ORGANISATION_ID}

---

## Get numbers assigned to an application

This endpoint lists all numbers that are assigned to a particular app.

### Headers

This is a protected resource and requires an [instance signed request](doc:using-rest#instance-signed-request).

    Authorization: Instance {instance ID}:{instance signature}
    x-timestamp: {now}

    eg:
    Authorization: Instance 00a3ffb1-0808-4dd4-9c7d-e4383d82e445:VE1UwyOa8r9DscyBWGVZ43qEDn+SGJGoNe2aN8WrR+8=
    x-timestamp: 2015-06-20T11:43:10.944Z

### Request

    [GET]

    URL:
    https://portalapi.sinch.com/v1/applications/key/{applicationKey}/numbers

    eg:
    https://portalapi.sinch.com/v1/applications/key/bb7b4e39-4227-4913-8c81-2db4abb54fb3/numbers

    [body]
        empty

    eg:
    {}

### Response

    Number[]

    [Number]
        long - number
        integer - groupId
        string[] - capabilities
        string? - applicationKey
        string - countryId
        string - city
        string - country
        string - areaCode
        string? - expires
        Money    - cost

    eg:

```json
[
  {
    "number": 14150005551,
    "groupId": 13,
    "capabilities": ["voice"],
    "applicationKey": "bb7b4e39-4227-4913-8c81-2db4abb54fb3",
    "countryId": "US",
    "city": "SAN FRANCISCO",
    "country": "US",
    "areaCode": "415",
    "expires": "2015-12-31T23:59:59+00:00",
    "cost": {
      "amount": 0.0,
      "currencyId": "USD"
    }
  }
]
```

The “capabilities” can either be “voice” or “sms”.

### Error Codes

    [Error Codes]
        InvalidScheme (40301)       - The authorization scheme 'instance' is required.
        ResourceNotFound (40400)    - Application with the key {KEY_HERE} was not found.
        TemporaryDown (50300)       - Service is temporarily unavailable, please try again later.

---

## Add numbers to an application (Overwrite)

This API overwrites all numbers assigned to your app with the new set of numbers that you specify.

> **Attention**
>
> When using this API method, any numbers that may already be configured for the particular application will be overwritten by the new list of numbers that you specify in this API call.

A new endpoint to only add a specific number to an application without overwriting the existing numbers will be available very soon.

### Headers

This is a protected resource and requires an [instance signed request](doc:using-rest#instance-signed-request).

    Authorization: Instance {instance ID}:{instance signature}
    x-timestamp: {now}

    eg:
    Authorization: Instance 00a3ffb1-0808-4dd4-9c7d-e4383d82e445:6mLYthlkatoYKkq15oI/RuwtC8sIwfJsPrSHkOLDmUM=
    x-timestamp: 2015-06-20T11:43:10.944Z

### Request

    [PUT]

    URL:
    https://portalapi.sinch.com/v1/applications/key/{applicationKey}/numbers/{numberCapability}

    eg:
    https://portalapi.sinch.com/v1/applications/key/bb7b4e39-4227-4913-8c81-2db4abb54fb3/numbers/voice

    [body]
        long[] - numbers

    eg:
    {
        "numbers":[14150005550]
    }

> **Note**
>
> Accepted values for ‘numberCapability’ are **sms** or **voice**.

### Response

    Number[]

    [Number]
        long - number
        integer - groupId
        string[] - capabilities
        string? - applicationKey
        string - countryId
        string - city
        string - country
        string - areaCode
        string? - expires
        Money    - cost

    eg:

```json
[
  {
    "number": 14150005550,
    "groupId": 13,
    "capabilities": ["voice"],
    "applicationKey": "bb7b4e39-4227-4913-8c81-2db4abb54fb3",
    "countryId": "US",
    "city": "SAN FRANCISCO",
    "country": "US",
    "areaCode": "415",
    "expires": "2015-12-31T23:59:59+00:00",
    "cost": {
      "amount": 0.0,
      "currencyId": "USD"
    }
  },
  {
    "number": 14150005551,
    "groupId": 13,
    "capabilities": ["voice"],
    "applicationKey": "bb7b4e39-4227-4913-8c81-2db4abb54fb3",
    "countryId": "US",
    "city": "SAN FRANCISCO",
    "country": "US",
    "areaCode": "415",
    "expires": "2015-12-31T23:59:59+00:00",
    "cost": {
      "amount": 0.0,
      "currencyId": "USD"
    }
  }
]
```

### Error Codes

    [Error Codes]
        ParameterValidation (40001) - Invalid number type: {NUMBER_TYPE}.  Type must be one of: sms, voice.
        InvalidScheme (40301)       - The authorization scheme 'instance' is required.
        ResourceNotFound (40400)    - Application with the key {KEY_HERE} was not found.
        TemporaryDown (50300)       - Service is temporarily unavailable, please try again later.

<div id="removeanumbersfromanapplication">

Remove a number from an application

</div>

With this API you can remove (unassign) a number from your application. They will still be assigned to your account.

### Headers

This is a protected resource and requires an [instance signed request](doc:using-rest#instance-signed-request).

    Authorization: Instance {instance ID}:{instance signature}
    x-timestamp: {now}

    eg:
    Authorization: Instance 00a3ffb1-0808-4dd4-9c7d-e4383d82e445:EA85F4qGFB0+tYZUh68g22R8DgtzxpsCXttLq1NKPgM=
    x-timestamp: 2015-06-20T11:43:10.944Z

### Request

    [DELETE]

    URL:
    https://portalapi.sinch.com/v1/applications/key/{applicationKey}/numbers/{number}

    eg:
    https://portalapi.sinch.com/v1/applications/key/bb7b4e39-4227-4913-8c81-2db4abb54fb3/numbers/14150005551

    [body]
        empty

    eg:
    {}

### Response

    204 - No Content

### Error Codes

    [Error Codes]
        InvalidRequest (40003)      - The 'number' specified is not a valid number.
        InvalidScheme (40301)       - The authorization scheme 'instance' is required.
        ResourceNotFound (40400)    - Application with the key {KEY_HERE} was not found.
        TemporaryDown (50300)       - Service is temporarily unavailable, please try again later.
