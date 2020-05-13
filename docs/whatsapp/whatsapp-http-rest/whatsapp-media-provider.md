---
title: Media provider
excerpt: >-
  Get to know how to use your own file storage service for sending secured media
  links.
next:
  pages:
    - whatsapp-stickerpack-management
---
You can send media files that are downloadable from URLs secured by Basic or Bearer Authentication to WhatsApp users.
WhatsApp API allows you to have different media providers for the same type of authentication and decide which one to use on a per request basis.

> 📘 Note
>
> With the 2020-03-10 release constraints for the name of a media provider are added. It is recommended to update any existing media provider configurations to use `name` parameters that meet these constraints.

### Adding/Updating a provider
> 📘 Note
>
> Be sure to give each provider a unique name as reusing a name will overwrite the previous entry.

#### Request
`POST whatsapp/v1/{bot-id}/provision/provider`

JSON object parameters:

| Name    | Description                      | JSON Type    | Default    | Constraints                                         | Required |
| ------- | -------------------------------- | ------------ | ---------- | --------------------------------------------------- | :------: |
| name    | Name of the provider             | String       | N/A        | A-Z, a-z, -, and _ allowed. 200 characters maximum. | Yes      |
| type    | The type the provider            | String       | N/A        | `www`                                               | Yes      |
| config  | The config object                | Object       | N/A        | N/A                                                 | Yes      |

#### Response

`200 OK`

Empty response body

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

### Config object types

#### Basic authentication
*config* JSON object parameters:

| Name    | Description                      | JSON Type    | Default    | Constraints           | Required |
| ------- | -------------------------------- | ------------ | ---------- | --------------------- | :------: |
| username| Username                         | String       | N/A        | N/A                   | Yes      |
| password| Password                         | String       | N/A        | N/A                   | Yes      |

```json
{
    "name": "your-basic-provider",
    "type": "www",
    "config": {
        "basic": {
            "username": "user",
            "password": "AbCdEf123456"
        }
    }
 }
```
#### Bearer authentication
*config* JSON object parameters:

| Name    | Description                      | JSON Type    | Default    | Constraints           | Required |
| ------- | -------------------------------- | ------------ | ---------- | --------------------- | :------: |
| bearer  | Bearer authentication token      | String       | N/A        | N/A                   | Yes      |

```json
{
    "name": "your-bearer-provider",
    "type": "www",
    "config": {
        "bearer": "AbCdEf123456"
    }
 }
```

### Deleting a provider from the providers list

`DELETE whatsapp/v1/{bot-id}/provision/provider/{provider-name}`

#### Responses

`200 OK`

Empty response body

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

### Retrieving the providers list
`GET whatsapp/v1/{bot-id}/provision/provider`

#### Responses
`200 OK`

The response body is a JSON object with the following parameters: 


| Name    | Description                      | JSON Type    |
| ------- | -------------------------------- | ------------ |
| name    | Name of the provider             | String       |
| type    | The type the provider            | String       |
| config  | The config object                | Object       |

**Basic authentication media provider**

| Name    | Description                      | JSON Type    | 
| ------- | -------------------------------- | ------------ | 
| username| Username                         | String       |
| password| Password                         | String       | 

**Bearer authentication media provider**

| Name    | Description                      | JSON Type    | 
| ------- | -------------------------------- | ------------ | 
| bearer  | Bearer authentication token      | String       |


**Sample**
```json
{
  "settings": {
    "application": {
      "media": {
        "providers": [
          {
            "name": "basic_media_provider_name",
            "type": "www",
            "config": {
              "basic": {
                "username": "user",
                "password": "AbCdEf123456&44jf"
              }
            }
          },
          {
            "name": "bearer_media_provider_name",
            "type": "www",
            "config": {
              "bearer": "AbCdEf123456&44jf"
            }
          }
        ]
      }
    }
  }
}
```

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).
