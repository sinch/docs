---
title: Send rich messages with Facebook Messenger
excerpt: >-
  Learn and see how different kind of messages render on Facebook Messenger.
hidden: false
---

## Sending Media Message <span class="betabadge">Beta</span>

Go to [Project ID and AccessKeys](https://developers.sinch.com/docs/conversation-getting-started#5-project-id-and-access-keys) to obtain your AccessToken

```shell Curl
curl --location --request POST 'https://eu.conversation.api.sinch.com/v1beta/projects/{project_id}/messages:send' \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer <access token>' \
 -d '{
    "app_id": "{{YOUR_APP_ID}}",
    "recipient": {
        "contact_id": "{{YOUR_CONTACT_ID}}"
    },
    "message": {
        "media_message": {
            "url": "https://1vxc0v12qhrm1e72gq1mmxkf-wpengine.netdna-ssl.com/wp-content/uploads/2018/12/favicon.png"
        }
    },
    "channel_priority_order": [
        "MESSENGER"
    ]
}'
```

![Facebook Media Message](../conversation-channel-support/images/channel-support/messenger/fb_message_media.jpg)

## Sending Choice Message

```shell Curl

curl --location --request POST 'https://eu.conversation.api.sinch.com/v1beta/projects/{project_id}/messages:send' \

-H 'Content-Type: application/json' \
-H 'Authorization: Bearer <access token>' \
-d '{
    "app_id": "{{YOUR_APP_ID}}",
    "recipient": {
        "contact_id": "{{YOUR_CONTACT_ID}}"
    },
    "message": {
        "choice_message": {
            "text_message": {
                "text": "What do you want to do?"
            },
            "choices": [
                {
                    "text_message": {
                        "text": "Suggested Reply Text"
                    }
                },
                {
                    "url_message": {
                        "title": "URL Choice Message:",
                        "url": "https://www.sinch.com"
                    }
                },
                {
                    "call_message": {
                        "title": "Call Choice Message:Q",
                        "phone_number": "46732000000"
                    }
                }
            ]
        }
    },
    "channel_priority_order": [
        "MESSENGER"
    ]
}'
```

![Facebook Message Choice](../conversation-channel-support/images/channel-support/messenger/fb_message_choice.jpg)

## Sending Card Message

```shell Curl
curl --location --request POST 'https://eu.conversation.api.sinch.com/v1beta/projects/{project_id}/messages:send' \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer <access token>' \
 -d '{
    "app_id": "{{YOUR_APP_ID}}",
    "recipient": {
        "contact_id": "{{YOUR_CONTACT_ID}}"
    },
    "message": {
        "card_message": {
            "title": "This is the card title",
            "description": "This is the card description",
            "media_message": {
                "url": "https://1vxc0v12qhrm1e72gq1mmxkf-wpengine.netdna-ssl.com/wp-content/uploads/2019/05/Sinch-logo-Events.png"
            },
            "choices": [
                {
                    "text_message": {
                        "text": "Suggested Reply Text"
                    }
                },
                {
                    "url_message": {
                        "title": "URL Choice Message:",
                        "url": "https://www.sinch.com"
                    }
                },
                {
                    "call_message": {
                        "title": "Call Choice Message:Q",
                        "phone_number": "46732000000"
                    }
                }
            ]
        }
    },

    "channel_priority_order": [
        "MESSENGER"
    ]
}'
```

![Facebook Card Message](../conversation-channel-support/images/channel-support/messenger/fb_message_card.jpg)

## Sending Carousel Message

```shell Curl
curl --location --request POST 'https://eu.conversation.api.sinch.com/v1beta/projects/{project_id}/messages:send' \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer <access token>' \
 -d '{
    "app_id": "{{YOUR_APP_ID}}",
    "recipient": {
        "contact_id": "{{YOUR_CONTACT_ID}}"
    },
   "message": {
        "carousel_message": {
            "cards": [
                {
                    "title": "This is the card 1 title",
                    "description": "This is the card 1 description",
                    "media_message": {
                        "url": "https://1vxc0v12qhrm1e72gq1mmxkf-wpengine.netdna-ssl.com/wp-content/uploads/2019/05/Sinch-logo-Events.png"
                    },
                    "choices": [
                        {
                            "text_message": {
                                "text": "Suggested Reply 1 Text"
                            }
                        },
                        {
                            "text_message": {
                                "text": "Suggested Reply 2 Text"
                            }
                        },
                        {
                            "text_message": {
                                "text": "Suggested Reply 3 Text"
                            }
                        }
                    ]
                },
                {
                    "title": "This is the card 2 title",
                    "description": "This is the card 2 description",
                    "media_message": {
                        "url": "https://1vxc0v12qhrm1e72gq1mmxkf-wpengine.netdna-ssl.com/wp-content/uploads/2019/05/Sinch-logo-Events.png"
                    },
                    "choices": [
                        {
                            "url_message": {
                                "title": "URL Choice Message:",
                                "url": "https://www.sinch.com"
                            }
                        }
                    ]
                },
                {
                    "title": "This is the card 3 title",
                    "description": "This is the card 3 description",
                    "media_message": {
                        "url": "https://1vxc0v12qhrm1e72gq1mmxkf-wpengine.netdna-ssl.com/wp-content/uploads/2019/05/Sinch-logo-Events.png"
                    },
                    "choices": [
                        {
                            "call_message": {
                                "title": "Call Choice Message:Q",
                                "phone_number": "46732000000"
                            }
                        }
                    ]
                },
                {
                    "title": "This is the card 4 title",
                    "description": "This is the card 4 description",
                    "media_message": {
                        "url": "https://1vxc0v12qhrm1e72gq1mmxkf-wpengine.netdna-ssl.com/wp-content/uploads/2019/05/Sinch-logo-Events.png"
                    },
                    "choices": [
                        {
                            "location_message": {
                                "title": "Location Choice Message",
                                "label": "Enriching Engagement",
                                "coordinates": {
                                    "latitude": 55.610479,
                                    "longitude": 13.002873
                                }
                            }
                        }
                    ]
                }
            ]
        }
    },
    "channel_priority_order": [
        "MESSENGER"
    ]
}'
```

![Facebook Carousel Message](../conversation-channel-support/images/channel-support/messenger/fb_message_carousel.jpg)

## Sending Location Message

```shell Curl
curl --location --request POST 'https://eu.conversation.api.sinch.com/v1beta/projects/{project_id}/messages:send' \
-H 'Content-Type: application/json' \
-H 'Authorization: Bearer <access token>' \
 -d '{
    "app_id": "{{YOUR_APP_ID}}",
    "recipient": {
        "contact_id": "{{YOUR_CONTACT_ID}}"
    },
    "message": {
        "location_message": {
            "title": "Location Message",
            "label": "Enriching Engagement",
            "coordinates": {
                "latitude": 55.610479,
                "longitude": 13.002873
            }
        }
    },
    "channel_priority_order": [
        "MESSENGER"
    ]
}'
```

![Facebook Location Message](../conversation-channel-support/images/channel-support/messenger/fb_message_location.jpg)
