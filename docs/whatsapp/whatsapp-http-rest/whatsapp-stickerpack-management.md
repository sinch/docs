---
title: Stickerpack management
excerpt: These endpoints can be used to manage first-party (maintained by WhatsApp) and third-party (maintained by you) stickerpacks and stickers.
---

## Retrieve stickerpacks

Use this endpoint to retrieve a list of stickerpack IDs.

#### Request
`GET whatsapp/v1/{bot-id}/stickerpacks`

Query parameter:

| Name      | Description                  | Default      | Constraints                        | Required |
| --------- | ---------------------------- | ------------ | ---------------------------------- | :------: |
| namespace | `third_party` or `whatsapp`  | `whatsapp`   | Either `third_party` or `whatsapp` | No       |

#### Response

`200 OK`

The response body is a JSON object with the following contents:

| Name         | Description                     | JSON Type     |
| ------------ | ------------------------------- | ------------- |
| stickerpacks | Array of stickerpack ID objects | Array[Object] | 

The the stickerpack ID objects have the following structure:

| Name  | Description              | JSON Type |
| ----- | ------------------------ | ----------|
| id    | A stickerpack identifier | String    | 

```json
{
    "stickerpacks": [
        {
            "id": "4316843a-874b-4530-86a2-1ab55c53cd3f"
        },
        {
            "id": "4316843a-874b-4530-86a2-1ab55c53cd3f"
        }
    ]
}
```

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

## Retrieve stickerpack information

Use this endpoint to retrieve information about a stickerpack.

#### Request
`GET whatsapp/v1/{bot-id}/stickerpacks/{stickerpack-id}`

Query parameter:

| Name      | Description                  | Default      | Constraints                        | Required |
| --------- | ---------------------------- | ------------ | ---------------------------------- | :------: |
| namespace | `third_party` or `whatsapp`  | `whatsapp`   | Either `third_party` or `whatsapp` | No       |

#### Response

`200 OK`

The response body is a JSON object with the following contents:

| Name                   | Description                                        | JSON Type | Required |
| ---------------------- | -------------------------------------------------- | --------- | -------- |
| publisher              | Publisher of the stickerpack                       | String    | Yes      |
| name                   | Stickerpack name                                   | String    | Yes      |
| ios_app_store_link     | Link to the stickerpack in the Apple iOS App Store | String    | No       |
| android_app_store_link | Link to the stickerpack in the Google Play store   | String    | No       |

```json
{
    "publisher": "My publisher name",
    "name": "My stickerpack"
}
```

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

## Retrieve sticker indices

Use this endpoint to retrieve a list of sticker indices.

#### Request
`GET whatsapp/v1/{bot-id}/stickerpacks/{stickerpack-id}/stickers`

Query parameter:

| Name      | Description                  | Default      | Constraints                        | Required |
| --------- | ---------------------------- | ------------ | ---------------------------------- | :------: |
| namespace | `third_party` or `whatsapp`  | `whatsapp`   | Either `third_party` or `whatsapp` | No       |

#### Response

`200 OK`

The response body is a JSON object with the following contents:

| Name      | Description                     | JSON Type     |
| --------- | ------------------------------- | ------------- |
| stickers  | Array of sticker index objects  | Array[Object] | 

The sticker index objects have the following structure:

| Name  | Description     | JSON Type |
| ----- | ----------------| ----------|
| index | A sticker index | String    | 

```json
{
    "stickers": [
        {
            "index": "0"
        },
        {
            "index": "1"
        }
    ]
}
```

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

## Retrieve sticker information

Use this endpoint to retrieve information about a sticker in a stickerpack.

#### Request
`GET whatsapp/v1/{bot-id}/stickerpacks/{stickerpack-id}/stickers/{sticker-index}`

Query parameter:

| Name      | Description                  | Default      | Constraints                        | Required |
| --------- | ---------------------------- | ------------ | ---------------------------------- | :------: |
| namespace | `third_party` or `whatsapp`  | `whatsapp`   | Either `third_party` or `whatsapp` | No       |

#### Response

`200 OK`

The response body is a JSON object with the following contents:

| Name   | Description                                                                             | JSON Type     |
| ------ | --------------------------------------------------------------------------------------- | ------------- |
| id     | The identifier for the sticker, which can be used when sending the sticker in a message | String        | 
| emojis | Array of emojis included in the stickerpack                                             | Array[String] | 

```json
{
    "id": "c0b0aebe-02cc-4ef1-9b7a-014987f6c361",
    "emojis": [
        "🐥",
        "😃"
    ]
}
```

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

## Create stickerpack

Use this endpoint to create a third-party stickerpack.

#### Request
`POST whatsapp/v1/{bot-id}/stickerpacks`

The request body is a JSON object with the following contents:

| Name                   | Description                                        | JSON Type | Constraints                   | Required |
| ---------------------- | -------------------------------------------------- | --------- | ----------------------------- | -------- |
| publisher              | Publisher of the stickerpack                       | String    | N/A                           | Yes      |
| name                   | Stickerpack name                                   | String    | N/A                           | Yes      |
| ios_app_store_link     | Link to the stickerpack in the Apple iOS App Store | String    | Valid Apple iOs App Store URL | No       |
| android_app_store_link | Link to the stickerpack in the Google Play store   | String    | Valid Google Play store URL   | No       |

```json
{
	"publisher": "My stickerpack publisher",
	"name": "My stickerpack name"
}
```

#### Response

`201 Created`

The response body is a JSON object with the following contents:

| Name      | Description                                | JSON Type  |
| --------- | ------------------------------------------ | ---------- |
| id        | The identifier for the created stickerpack | String     | 

```json
{
    "id": "4316843a-874b-4530-86a2-1ab55c53cd3f"
}
```

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

## Update stickerpack

Use this endpoint to update a third-party stickerpack.

#### Request
`PATCH whatsapp/v1/{bot-id}/stickerpacks/{stickerpack-id}`

The request body is a JSON object with the following contents:

| Name                   | Description                                        | JSON Type | Constraints                   | Required |
| ---------------------- | -------------------------------------------------- | --------- | ----------------------------- | -------- |
| publisher              | Publisher of the stickerpack                       | String    | N/A                           | No       |
| name                   | Stickerpack name                                   | String    | N/A                           | No       |
| ios_app_store_link     | Link to the stickerpack in the Apple iOS App Store | String    | Valid Apple iOs App Store URL | No       |
| android_app_store_link | Link to the stickerpack in the Google Play store   | String    | Valid Google Play store URL   | No       |

Each supplied field overwrites the value for the existing stickerpack.

```json
{
	"name": "My new stickerpack name"
}
```

#### Response

`200 OK`

Empty response body.

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

## Delete stickerpack

Use this endpoint to delete a third-party stickerpack.

#### Request
`DELETE whatsapp/v1/{bot-id}/stickerpacks/{stickerpack-id}`

#### Response

`200 OK`

Empty response body.

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

## Create sticker

Use this endpoint to create a sticker in a third-party stickerpack.

#### Request
`POST whatsapp/v1/{bot-id}/stickerpacks/{stickerpack-id}/stickers`

Accepted content types can be found in the [introduction](doc:whatsapp-introduction#section-accepted-media-types).

The request body is a JSON object with the following contents:

| Name    | Description                                 | JSON Type     | Constraints                                                    | Required |
| ------- | ------------------------------------------- | ------------- | -------------------------------------------------------------- | -------- |
| url     | Public URL of sticker                       | String        | Valid URL and accepted content type                            | Yes      | 
| emojis  | Array of emojis included in the stickerpack | Array[String] | Each emoji should be a single character. Three emojis maximum. | No       | 

```json
{
    "url": "https://example.com/sticker.webp",
    "emojis": [
        "🐥",
        "😃"
    ]
}
```

#### Response

`201 Created`

The response body is a JSON object with the following contents:

| Name      | Description                                         | JSON Type  |
| --------- | --------------------------------------------------- | ---------- |
| index     | The index of the created sticker in the stickerpack | String     | 

```json
{
    "index": "0"
}
```

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

## Update sticker

Use this endpoint to create a sticker in a third-party stickerpack.

#### Request
`PATCH whatsapp/v1/{bot-id}/stickerpacks/{stickerpack-id}/stickers/{sticker-index}`

Accepted content types can be found in the [introduction](doc:whatsapp-introduction#section-accepted-media-types).

The request body is a JSON object with the following contents:

| Name    | Description                                 | JSON Type     | Constraints                                                    | Required |
| ------- | ------------------------------------------- | ------------- | -------------------------------------------------------------- | -------- |
| url     | Public URL of sticker                       | String        | Valid URL and accepted content type                            | No       | 
| emojis  | Array of emojis included in the stickerpack | Array[String] | Each emoji should be a single character. Three emojis maximum. | No       | 

```json
{
	"url": "https://example.com/updated_sticker.webp",
}
```

#### Response

`200 OK`

Empty response body.

```json
{
    "index": "0"
}
```

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

## Delete sticker

Use this endpoint to delete a sticker in a third-party stickerpack.

#### Request
`DELETE whatsapp/v1/{bot-id}/stickerpacks/{stickerpack-id}/stickers/{sticker-index}`

#### Response

`200 OK`

Empty response body.

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).
