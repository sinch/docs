---
title: Rent a number
excerpt: ''
hidden: false
---


## Rent a number in the US

To rent a number you first need to find a number that suites the needs for your application.

### Search for a number

Search for numbers that are available for you to rent. In this example you will search for any US number.

```shell
curl --request GET \
 --url 'https://numbers.api.sinch.com/v1alpha1/projects/{projectId}/availableNumbers?regionCode=US' \
 --header 'Accept: application/json' \
 -u {clientId}:{clientSecret}
```
Replace {projectId}, {clientId} and {clientSecret} with your values. 

You can filter by any property on the available number resource. learn more about it in the [API specification](https://developers.sinch.com/reference#numberservice_listavailablenumbers).  


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
Take a note of the phoneNumber you will need it in the next step. 

### Rent the number

Rent a number to use with SMS or Voice products

```shell
curl --request POST \
 --url https://numbers.api.sinch.com/v1alpha1/projects/projectId/availableNumbers/+12089087284:rent \
 --header 'Accept: application/json' \
 --u {clientId}:{clientSecret} 
```
Replace {projectId}, {clientId} and {clientSecret} with your values. 

#### Response

```json
{
  "phoneNumber": "+12092224786"
}
```

### Rent the number and configure it for SMS

Rent a number to use with SMS or Voice products

```shell
curl --request POST \
 --url https://numbers.api.sinch.com/v1alpha1/projects/projectId/availableNumbers/+12089087284:rent \
 --header 'Accept: application/json' \
 --u {clientId}:{clientSecret} 
 --d '{"smsConfiguration":{"servicePlanId":"sdfewe383408d"}}'
```
Replace {projectId}, {clientId} and {clientSecret}, and [servicePlanId](https://dashboard.sinch.com/sms/api) with your values.  

## Search for a Toll free number.

As above but add type you are interested in
```shell
curl --request GET \
 --url 'https://numbers.api.sinch.com/v1alpha1/projects/{projectId}/availableNumbers?regionCode=US&type=TOLL_FREE' \
 --header 'Accept: application/json' \
 -u {clientId}:{clientSecret}
```