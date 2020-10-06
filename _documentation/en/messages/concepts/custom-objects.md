---
title: Custom objects
navigation_weight: 4
description: Understanding custom objects
---

# Custom objects

The Messages API has the concept of a custom object. The custom object allows you to leverage advanced features of the supported messaging platforms, such as Facebook Messenger, WhatsApp and Viber.

The custom object takes a partial section of the underlying messaging platform's API request format, and sends it directly to that platform.

Using WhatsApp by way of example, for the following Messages API call:

```json
{
    "from": {
        "type": "whatsapp",
        "number": "447700900001"
    },
    "to": {
        "type": "whatsapp",
        "number": "447700900000"
    },
    "message": {
        "content": {
            "type": "custom",
            "custom": {
                $CUSTOM_OBJECT
            }
        }
    }
}
```

Nexmo would send the following to WhatsApp:

```json
{
  "recipient_type": "individual",
  "to": "$TO_NUMBER",
  {$CUSTOM_OBJECT}
}
```

The JSON in `CUSTOM_OBJECT` depends on the messaging platform's format for the type of request you want to make.

If the WhatsApp request was:

```json
{
    "recipient_type": "individual",
    "to": "447700900000",
    "type": "template",
    "template": {
        "namespace": "whatsapp:hsm:technology:nexmo",
        "name": "parcel_location",
        "language": {
            "policy": "deterministic",
            "code": "en"
        },
        "components": [
            {
                "type": "location",
                "location": {
                    "longitude": -122.425332,
                    "latitude": 37.758056,
                    "name": "Facebook HQ",
                    "address": "1 Hacker Way, Menlo Park, CA 94025"
                }
            },
            {
                "type": "body",
                "parameters": [
                    {
                        "type": "text",
                        "text": "Value 1"
                    },
                    {
                        "type": "text",
                        "text": "Value 2"
                    },
                    {
                        "type": "text",
                        "text": "Value 3"
                    }
                ]
            }
        ]
    }
}
```

The Nexmo Messages API request format would be:

```json
{
    "from": {
        "type": "whatsapp",
        "number": "447700900001"
    },
    "to": {
        "type": "whatsapp",
        "number": "447700900000"
    },
    "message": {
        "content": {
            "type": "custom",
            "custom": {
                "type": "template",
                "template": {
                    "namespace": "whatsapp:hsm:technology:nexmo",
                    "name": "parcel_location",
                    "language": {
                        "policy": "deterministic",
                        "code": "en"
                    },
                    "components": [
                        {
                            "type": "header",
                            "parameters": [
                                {
                                    "type": "location",
                                    "location": {
                                        "longitude": -122.425332,
                                        "latitude": 37.758056,
                                        "name": "Facebook HQ",
                                        "address": "1 Hacker Way, Menlo Park, CA 94025"
                                    }
                                }
                            ]
                        },
                        {
                            "type": "body",
                            "parameters": [
                                {
                                    "type": "text",
                                    "text": "Value 1"
                                },
                                {
                                    "type": "text",
                                    "text": "Value 2"
                                },
                                {
                                    "type": "text",
                                    "text": "Value 3"
                                }
                            ]
                        }
                    ]
                }
            }
        }
    }
}
```

The custom object, `CUSTOM_OBJECT`, defined in the original request format is:

```json
  "type": "template",
  "template": {
    "namespace": "whatsapp:hsm:technology:nexmo",
    "name": "parcel_location",
    "language": {
      "policy": "deterministic",
      "code": "en"
    },
    "components": [
      {
        "type": "location",
        "location": {
          "longitude": -122.425332,
          "latitude": 37.758056,
          "name": "Facebook HQ",
          "address": "1 Hacker Way, Menlo Park, CA 94025"
        }
      },
      {
        "type": "body",
        "parameters": [
          {
            "type": "text",
            "text": "Value 1"
          },
          {
            "type": "text",
            "text": "Value 2"
          }
        ]
      }
    ]
  }
```

Note that _when using custom objects_, the format for properties such as language code must match that of the target platform. For example, in the underlying WhatsApp message format, language codes are of the form `en_GB`, not `en-GB`.

## Facebook Messenger Message Templates

You can use custom objects for sending Facebook Messenger message templates. For example:

```json
{
    "from": {
        "type": "messenger",
        "id": $FB_SENDER_ID
    },
    "to": {
        "type": "messenger",
        "id": $FB_RECIPIENT_ID
    },
    "message": {
        "content": {
            "type": "custom",
            "custom": {
                $CUSTOM_OBJECT
            }
        }
    }
}
```

The Messages API message would be transformed to something similar to the following:

```json
{
    "recipient": {
        "id": "<PSID>"
    },
    "message": {
        $CUSTOM_OBJECT
    }
}
```

Where the custom object, `$CUSTOM_OBJECT`, can be anything from within the [Messenger Message Object](https://developers.facebook.com/docs/messenger-platform/send-messages/templates/).

For example, consider sending media by facebook URL, the `$CUSTOM_OBJECT` format would look like

```json
"message": {
  "attachment": {
    "type": "template",
    "payload": {
      "template_type": "media",
      "elements": [{
        "media_type": "<image|video>",
        "url": "<FACEBOOK_URL>"
      }]
    }
  }
}
```

The Nexmo Messages API request format would be:

```json
{
    "from": {
        "type": "messenger",
        "id": $FB_SENDER_ID
    },
    "to": {
        "type": "messenger",
        "id": $FB_RECIPIENT_ID
    },
    "message": {
        "attachment": {
            "type": "template",
            "payload": {
                "template_type": "media",
                "elements": [
                    {
                        "media_type": "<image|video>",
                        "url": "<FACEBOOK_URL>"
                    }
                ]
            }
        }
    }
}
```

## See more examples

-   [WhatsApp send contact](/messages/code-snippets/whatsapp/send-contact)
-   [WhatsApp send location](/messages/code-snippets/whatsapp/send-location)
-   [Facebook Messenger custom object](/messages/code-snippets/messenger/send-template)
