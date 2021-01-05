---
title: Search, rent and configure numbers
excerpt: ''
hidden: false
---
### Search for a number

#### Request

```shell
curl --request GET \
 --url 'https://numbers.api.sinch.com/v1alpha1/projects/{projectId}/availableNumbers?regionCode=US' \
 --header 'Accept: application/json' \
 -u {clientId}:{clientSecret}
```

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

## Search for a Toll-free number

Use the same GET request as above but add the number type you are interested in.

#### Request

```shell
curl --request GET \
 --url 'https://numbers.api.sinch.com/v1alpha1/projects/{projectId}/availableNumbers?regionCode=US&type=TOLL_FREE' \
 --header 'Accept: application/json' \
 -u {clientId}:{clientSecret}
```
**Note**: You will need the “phoneNumber” in the response to rent your number.


## Rent a number to use with SMS or Voice products

In this **POST** example, remember to replace {projectId}, {clientId} and {clientSecret} with your values.

#### Request

```shell
curl --request POST \
 --url https://numbers.api.sinch.com/v1alpha1/projects/projectId/availableNumbers/+12089087284:rent \
 --header 'Accept: application/json' \
 --u {clientId}:{clientSecret} 
```

#### Response

```json
{
  "phoneNumber": "+12092224786"
}
```

## Rent a number and configure it for SMS

In this **POST** example, replace {projectId}, {clientId} and {clientSecret}, and [servicePlanId](https://dashboard.sinch.com/sms/api) with your values. Your **servicePlanId** can be found in your dashboard under **SMS** > **APIs** > **REST configuration**

#### Request

```shell
curl --request POST \
 --url https://numbers.api.sinch.com/v1alpha1/projects/projectId/availableNumbers/+12089087284:rent \
 --header 'Accept: application/json' \
 --u {clientId}:{clientSecret} 
 --d '{"smsConfiguration":{"servicePlanId":"sdfewe383408d"}}'
```
Replace {projectId}, {clientId} and {clientSecret}, and [servicePlanId](https://dashboard.sinch.com/sms/api) with your values.  

## Search for a Toll free number

Use the same GET request as above but add the number type you are interested in.

```shell
curl --request GET \
 --url 'https://numbers.api.sinch.com/v1alpha1/projects/{projectId}/availableNumbers?regionCode=US&type=TOLL_FREE' \
 --header 'Accept: application/json' \
 -u {clientId}:{clientSecret}
```

**Note**: You will need the “phoneNumber” in the response to rent your number.


## Rent a number to use with SMS or Voice products

In this **POST** example, remember to replace {projectId}, {clientId} and {clientSecret} with your values.

#### Request

```shell
curl --request POST \
 --url https://numbers.api.sinch.com/v1alpha1/projects/projectId/availableNumbers/+12089087284:rent \
 --header 'Accept: application/json' \
 --u {clientId}:{clientSecret} 
```

#### Response

```json
{
  "phoneNumber": "+12092224786"
}
```

## Rent a number and configure it for SMS

In this **POST** example, replace {projectId}, {clientId} and {clientSecret}, and [servicePlanId](https://dashboard.sinch.com/sms/api) with your values. Your **servicePlanId** can be found in your dashboard under **SMS** > **APIs** > **REST configuration**

#### Request

```shell
curl --request POST \
 --url https://numbers.api.sinch.com/v1alpha1/projects/projectId/availableNumbers/+12089087284:rent \
 --header 'Accept: application/json' \
 --u {clientId}:{clientSecret} 
 --d '{"smsConfiguration":{"servicePlanId":"sdfewe383408d"}}'
```


