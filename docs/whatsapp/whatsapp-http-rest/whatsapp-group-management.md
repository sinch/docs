---
title: Group Management
excerpt: >-
  Manage your WhatsApp groups and their admins. Learn how to add or remove
  members of the different groups.
next:
  pages:
    - whatsapp-media-provider
---

> ⚠️ Warning
>
> Facebook has announced the deprecation of their WhatsApp Group Management API:
>
> 
> "We have re-evaluated our feature of Groups on the API and have announced that we will remove this feature in the coming months. We know some businesses have valued using Groups to connect with users and appreciate the potential this feature will have. We will re-evaluate opening up Groups to businesses on the API at a later date after conducting some additional development.
> 
> The Groups API node will stop accepting any new WhatsApp Business API client phone numbers on July 6, 2020 and end for all WhatsApp Business API client phone numbers on Oct 6, 2020. Please begin migrating calls using this endpoint to avoid any disruption."
>
>
> Sinch WhatsApp Group Management API will stop accepting any newly registered phone number on July 6, 2020 and for all phone numbers on October 6, 2020.
> 

A collection of endpoints used to manage groups that are linked to a specific bot.
  - [Creating a new group](doc:whatsapp-group-management#create-group-endpoint)
  - [Listing all groups associated with your bot](doc:whatsapp-group-management#list-groups-endpoint)
  - [List all information for a specific group](doc:whatsapp-group-management#list-group-information-endpoint)
  - [Updating a groups subject](doc:whatsapp-group-management#update-group-subject-endpoint)
  - [Updating a groups icon](doc:whatsapp-group-management#update-group-icon-endpoint)
  - [Leaving a group created by your bot](doc:whatsapp-group-management#leave-group-endpoint)
  - [Remove group members from a specific group](doc:whatsapp-group-management#remove-members-endpoint)
  - [Add admins to a specific group](doc:whatsapp-group-management#add-admins-endpoint)
  - [Remove admins from a specific group](doc:whatsapp-group-management#remove-admins-endpoint)
  - [Get an invite link to a specific group](doc:whatsapp-group-management#get-invite-link-endpoint)
  - [Remove an active invite link from a specific group](doc:whatsapp-group-management#remove-invite-link-endpoint)

## Create group endpoint

`POST whatsapp/v1/{bot-id}/groups`

JSON object parameters:

| Name    | Description                      | JSON Type    | Default    | Constraints           | Required |
| ------- | :------------------------------: | :----------: | :--------: | :-------------------: | :------: |
| subject | Group subject                    | String       | N/A        | None                  | Yes      |

### Create group sample

```json
{
  "subject": "Sinch WhatsApp Team Group"
}
```

#### Responses

`200 OK`

The response body is a JSON object with the following parameters:

|Name          | Description                    | JSON Type     |
|--------------|--------------------------------|---------------|
|group_id      | Group id of just created group | String        |

**Sample**

```json
{
  "group_id": "group:447506616260-1565342732"
}
```

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`503 Service Unavailable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

## List groups endpoint

`GET whatsapp/v1/{bot-id}/groups`

#### Responses

`200 OK`

The response body is a JSON object with the following parameters:

|Name          | Description                       | JSON Type     |
|--------------|-----------------------------------|---------------|
|groups        | Array of string group identifiers | String array  |

**Sample**

```json
{
  "groups": [
    "group:447506616260-1565342732"
  ]
}
```

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`503 Service Unavailable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

## List group information endpoint

`GET whatsapp/v1/{bot-id}/groups/{group-id}`

#### Responses

`200 OK`

The response body is a JSON object with the following parameters:

| Name          | Description                               | JSON Type    |
| ------------- | ----------------------------------------- | ------------ |
| admins        | Array of the WhatsApp identity of all admins | String array |
| creation_time | ISO-8601 creation datetime of the group   | String       |
| creator       | WhatsApp identity of the creator of the group        | String       |
| members       | Array of WhatsApp identities for members in the group | String array |
| subject       | Subject of the group                      | String       |

**Sample**

```json
{
    "admins": [
        "1805550000"
    ],
    "creator": "1805550000",
    "members": [
        "1805550000",
        "46732001122"
    ],
    "subject": "Sinch Team WhatsApp Group",
    "creation_time": "2019-09-23T07:56:22.000Z"
}
```

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`503 Service Unavailable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors). Try again later.

`406 Not Acceptable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

## Update group subject endpoint

`PATCH whatsapp/v1/{bot-id}/groups/{group-id}`

JSON object parameters:

| Name    | Description                      | JSON Type    | Default    | Constraints           | Required |
| ------- | -------------------------------- | ------------ | ---------- | --------------------- | :------: |
| subject | Group subject                    | String       | N/A        | None                  | Yes      |

### Update group subject sample

```json
{
  "subject": "Updated Sinch WhatsApp Team Group"
}
```

#### Responses

`200 OK`

Empty response body

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`503 Service Unavailable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors). Try again later.

`406 Not Acceptable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

## Update group icon endpoint

`POST whatsapp/v1/{bot-id}/groups/{group-id}/icon`

JSON object parameters:

| Name    | Description                      | JSON Type    | Default    | Constraints           | Required |
| ------- | -------------------------------- | ------------ | ---------- | --------------------- | :------: |
| url     | Public url of group icon         | String       | N/A        | Valid URL, jpeg/png   | Yes      |


### Update group icon sample

```json
{
  "url": "https://www.example.com/new_icon.jpg"
}
```

#### Responses

`200 OK`

Empty response body

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`503 Service Unavailable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors). Try again later.

`406 Not Acceptable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

## Leave group endpoint

`DELETE whatsapp/v1/{bot-id}/groups/{group-id}`

#### Responses

`200 OK`

Empty response body

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`503 Service Unavailable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors). Try again later.

`406 Not Acceptable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

## Remove members endpoint

`DELETE whatsapp/v1/{bot-id}/groups/{group-id}/member`

JSON object parameters:

| Name    | Description                      | JSON Type    | Default    | Constraints           | Required |
| ------- | -------------------------------- | ------------ | ---------- | --------------------- | :------: |
| numbers | List of international phone numbers | String array | N/A        | 1 to 20 elements      | Yes      |

### Remove members sample

```json
{
  "numbers": [
    "46732001122",
    "46732002244"
  ]
}
```

#### Responses

`200 OK`

Empty response body

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`503 Service Unavailable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors). Try again later.

`406 Not Acceptable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

## Add admins endpoint

`POST whatsapp/v1/{bot-id}/groups/{group-id}/admin`

JSON object parameters:

| Name    | Description                      | JSON Type    | Default    | Constraints           | Required |
| ------- | -------------------------------- | ------------ | ---------- | --------------------- | :------: |
| numbers | List of international phone numbers | String array | N/A        | 1 to 20 elements      | Yes      |

### Add admins sample

```json
{
  "numbers": [
    "46732001122",
    "46732002244"
  ]
}
```

#### Responses

`200 OK`

Empty response body

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`503 Service Unavailable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors). Try again later.

`406 Not Acceptable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

## Remove admins endpoint

`DELETE whatsapp/v1/{bot-id}/groups/{group-id}/admin`

JSON object parameters:

| Name    | Description                      | JSON Type    | Default    | Constraints           | Required |
| ------- | -------------------------------- | ------------ | ---------- | --------------------- | :------: |
| numbers | List of international phone numbers | String array | N/A        | 1 to 20 elements      | Yes      |

### Remove admins sample

```json
{
  "numbers": [
    "46732001122",
    "46732002244"
  ]
}
```

#### Responses

`200 OK`

Empty response body

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`503 Service Unavailable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors). Try again later.

`406 Not Acceptable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

## Get invite link endpoint

`GET whatsapp/v1/{bot-id}/groups/{group-id}/invite`

#### Responses

`200 OK`

The response body is a JSON object with the following parameters:

| Name          | Description                               | JSON Type    |
| ------------- | ----------------------------------------- | ------------ |
| invite_link   | Public url of link. Any WhatsApp user who clicks this link will join the group that this link relates to | String array |

**Sample**

```json
{
    "invite_link": "https://chat.whatsapp.com/<invite-link>"
}
```

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`503 Service Unavailable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors). Try again later.

`406 Not Acceptable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

## Remove invite link endpoint

`DELETE whatsapp/v1/{bot-id}/groups/{group-id}/invite`

#### Responses

`200 OK`

Empty response body

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).

`503 Service Unavailable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors). Try again later.

`406 Not Acceptable`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#http-errors).
