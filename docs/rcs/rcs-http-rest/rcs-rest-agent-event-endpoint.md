---
title: Agent Event Endpoint
excerpt: 'Send events from a RCS chatbot (A2P Agent) to a end user. Emulate real user behaviour by sending displayed notifications and composing events with the RCS chatbot. '
---
The agent event endpoint provides operations for sending events from the agent to the user.

### Sending agent events to user

Send events to a user from the agent. This enables the agent to emulate real user behaviour by sending displayed notifications and composing events.

#### HTTP Request

`POST /rcs/v1/{agent_id}/events`

#### Path parameters

| Field     | Type   | Description                | Default | Constraints | Required |
| --------- | ------ | -------------------------- | ------- | ----------- | -------- |
| agent\_id | string | ID of the agent to be used | No      | No          | Yes      |

#### Request Body

The request body contains an instance of `AgentEvent`.

#### Response

**200 OK**

The event was successfully sent.

**502 Bad gateway**

Unable to send event due to issue with service provider / operator, please retry.

##### Examples

###### Sending a composing event

**Sending a composing event**
```curl
curl https://api.clxcommunications.com/rcs/v1/my-agent-id/events \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer zIMEJGfwD4oJ4qObPPjwZxwiP5cKARXRJpt9Kf6GSv7uOesvRV" \
  -d '{
    "to": "46555123456",
    "event_id": "ce5f9373-8a77-45fa-a78b-84a931005dc9",
    "event": {
      "type": "agent_composing"
    }
  }'
```


###### Sending a read event

**Sending a read event**
```curl
curl https://api.clxcommunications.com/rcs/v1/my-agent-id/events \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer zIMEJGfwD4oJ4qObPPjwZxwiP5cKARXRJpt9Kf6GSv7uOesvRV" \
  -d '{
    "to": "46555123456",
    "event_id": "ce5f9373-8a77-45fa-a78b-84a931005dc9",
    "event": {
      "type": "agent_read",
      "message_id": "Jsiuh76sJKAhdsiufg86823"
    }
  }'
```


#### JSON Model

##### AgentEvent

JSON Representation

**AgentEvent**
```json
{
  "to": string,
  "event_id": string,
  "event": {
    "type": enum(
      "agent_composing"
      "agent_read"
    )
    ... // type specific fields
  }
}
```


###### Fields

<div class="magic-block-html">
    <div class="marked-table">
        <table>
            <thead>
            <tr class="header">
                <th>Field</th>
                <th>Type</th>
                <th>Description</th>
                <th>Default</th>
                <th>Constraints</th>
                <th>Required</th>
            </tr>
            </thead>
            <tbody>
            <tr class="odd">
                <td>to</td>
                <td>string</td>
                <td>MSISDN of the recipient</td>
                <td>No</td>
                <td>^(?:00)[1-9][0-9]{8,16}$</td>
                <td>Yes</td>
            </tr>
            <tr class="even">
                <td>event_id</td>
                <td>string</td>
                <td>Client provided unique event id. If absent an id will be generated for you and will be seen in the response</td>
                <td>No</td>
                <td>^[0-9a-f]{8}-[0-9a-f]{4}-[1-5][0-9a-f]{3}-[89ab][0-9a-f]{3}-[0-9a-f]{12}$</td>
                <td>No</td>
            </tr>
            <tr class="odd">
                <td>event</td>
                <td><dl>
                    <dt>oneOf:</dt>
                    <dd><ul>
                        <li>object(<code class="interpreted-text" data-role="ref">AgentComposingEvent</code>)</li>
                        <li>object(<code class="interpreted-text" data-role="ref">AgentReadEvent</code>)</li>
                    </ul>
                    </dd>
                </dl></td>
                <td>Object describing the event to send to the user</td>
                <td>No</td>
                <td>N/A</td>
                <td>Yes</td>
            </tr>
            </tbody>
        </table>
    </div>
</div>


##### AgentComposingEvent

JSON Representation

**AgentComposingEvent**
```json
{
  "type": "agent_composing"
}
```


###### Fields

| Field | Type   | Description                                                                                 | Default | Constraints | Required |
| ----- | ------ | ------------------------------------------------------------------------------------------- | ------- | ----------- | -------- |
| type  | string | Static string 'agent\_composing'. This type notifies the recipient that the agent is typing | N/A     | N/A         | Yes      |

##### AgentReadEvent

JSON Representation

**AgentReadEvent**
```json
{
  "type": "agent_read",
  "message_id": string
}
```


###### Fields

| Field       | Type   | Description                                                                                                                | Default | Constraints | Required |
| ----------- | ------ | -------------------------------------------------------------------------------------------------------------------------- | ------- | ----------- | -------- |
| type        | string | Static string 'agent\_read'. This type notifies the recipient that the message sent by the user has been read by the agent | N/A     | N/A         | Yes      |
| message\_id | string | The message id that the agent has read. This is the message id the agent received in object(`UserAgentMessage`)            | N/A     | N/A         | Yes      |
