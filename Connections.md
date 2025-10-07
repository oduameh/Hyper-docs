# Connections {#connections}

Establishing a connection lets two Hyperledger Identus agents exchange messages securely. This guide outlines the steps for creating an invitation, accepting it, and tracking the connection lifecycle through the DIDComm protocol.

The [Connection protocol](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#connection-protocol) governs how agents authenticate each other before they can share credentials or proofs.

## Roles

The protocol defines two participants:

1. [Inviter](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#inviter). Initiates a connection by sending a [connection invitation](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#connection-invitation).
2. [Invitee](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#invitee). Accepts the invitation by returning a [connection request](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#connection-request).

## Prerequisites

- Inviter and invitee Cloud agents running.

## Cloud agent endpoints

Use the following REST API endpoints to manage the connection lifecycle:

1. [`/connections`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Connections-Management).
   - [`POST`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Connections-Management/operation/createConnection). Creates a new connection and returns an invitation.
   - [`GET`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Connections-Management/operation/getConnections). Returns the list of connections.
2. [`GET /connections/{connectionId}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Connections-Management/operation/getConnection). Returns an existing connection record by ID.
3. [`POST /connection-invitations`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Connections-Management/operation/acceptConnectionInvitation). Accepts an externally received invitation.

:::info Please check the full [Cloud agent API](https://hyperledger-identus.github.io/docs/agent-api) specification for more detailed information. :::

## Inviter flow

1. Generate and share a new Out-of-Band (OOB) invitation (the connection is created in `InvitationGenerated` state).
2. Receive a connection request from the invitee (the connection moves to `ConnectionRequestReceived` state).
3. Accept the connection request (the connection moves to `ConnectionResponsePending` state).
4. Send the connection response via the DIDComm Agent (the connection reaches `ConnectionResponseSent` state).

The following diagram represents the Inviter's Connection state transitions:

```
---
title: Inviter Connection State
---
stateDiagram-v2
[*] --> InvitationGenerated: generate and share new OOB invitation
InvitationGenerated --> ConnectionRequestReceived: receive connection request
ConnectionRequestReceived --> ConnectionResponsePending: accept connection request
ConnectionResponsePending --> ConnectionResponseSent: send connection response (DIDComm)
ConnectionResponseSent --> [*]
```

## Invitee flow

1. Receive the OOB invitation (`InvitationReceived` state).
2. Accept the invitation (the connection is created in `ConnectionRequestPending` state).
3. Send the connection request via [DIDComm](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#didcomm) (the connection reaches `ConnectionRequestSent` state).
4. Receive the connection response (the connection reaches `ConnectionResponseReceived` state).

The following diagram represents the Invitee's Connection state transitions:

```
---
title: Invitee Connection State
---
stateDiagram-v2
[*] --> InvitationReceived: receive OOB invitation
InvitationReceived --> ConnectionRequestPending: accept invitation
ConnectionRequestPending --> ConnectionRequestSent: send connection request (DIDComm)
ConnectionRequestSent --> ConnectionResponseReceived: receive connection response
ConnectionResponseReceived --> [*]
```

## Sequence diagram

The following diagram shows the end-to-end flow for establishing a connection between an inviter and an invitee.

## Command line example

The following example demonstrates how you could use two Cloud agent APIs to set up a connection between them.

### Inviter creates an invitation

```shell
curl -X 'POST' \
 'http://localhost:8080/cloud-agent/connections' \
 -H 'Content-Type: application/json' \
 -H "apikey: $API_KEY" \
 -d '{ "label": "Connect with Alice" }' | jq
```

Example response:

```json
{
  "connectionId": "1893e207-4cba-4792-8179-067c78d2acc2",
  "createdAt": "2023-02-02T17:14:13.593647Z",
  "invitation": {
    "from": "did:peer:inviter-did",
    "id": "1893e207-4cba-4792-8179-067c78d2acc2",
    "invitationUrl": "https://domain.com/path?_oob={RAW_INVITATION}",
    "type": "https://didcomm.org/out-of-band/2.0/invitation"
  },
  "kind": "/connections/1893e207-4cba-4792-8179-067c78d2acc2",
  "label": "Connect with Alice",
  "self": "Connection",
  "state": "InvitationGenerated"
}
```

### **Invitee** accepts OOB invitation

Replace `{RAW_INVITATION}` with the value of the '\_oob' query string parameter from the invitation URL above

```shell
curl -X 'POST' \
 'http://localhost:8090/cloud-agent/connection-invitations' \
 -H 'Content-Type: application/json' \
 -H "apikey: $API_KEY" \
 -d '{ "invitation": "{RAW_INVITATION}" }' | jq
```

Example response:

```json
{
  "connectionId": "755a457a-878e-4292-a3f2-cb83601f802e",
  "createdAt": "2023-02-02T18:05:58Z",
  "invitation": {
    "from": "did:peer:inviter-did",
    "id": "1893e207-4cba-4792-8179-067c78d2acc2",
    "invitationUrl": "https://domain.com/path?_oob={RAW_INVITATION}",
    "type": "https://didcomm.org/out-of-band/2.0/invitation"
  },
  "kind": "/connections/755a457a-878e-4292-a3f2-cb83601f802e",
  "myDid": "did:peer:invitee-did",
  "self": "Connection",
  "state": "ConnectionRequestPending",
  "theirDid": "did:peer:inviter-did",
  "updatedAt": "2023-02-02T18:05:59Z"
}
```

### Invitee retrieves the list of connections

```shell
curl -X 'GET' 'http://localhost:8090/cloud-agent/connections' \
    -H "apikey: $API_KEY" | jq
```

Example output:

```json
{
  "contents": [
    {
      "connectionId": "755a457a-878e-4292-a3f2-cb83601f802e",
      "createdAt": "2023-02-02T18:05:58Z",
      "invitation": {
        "from": "did:peer:issuer-did",
        "id": "1893e207-4cba-4792-8179-067c78d2acc2",
        "invitationUrl": "https://domain.com/path?_oob={RAW_INVITATION}",
        "type": "https://didcomm.org/out-of-band/2.0/invitation"
      },
      "kind": "/connections/755a457a-878e-4292-a3f2-cb83601f802e",
      "myDid": "did:peer:holder-did",
      "self": "Connection",
      "state": "ConnectionResponseReceived",
      "theirDid": "did:peer:issuer-did",
      "updatedAt": "2023-02-02T18:06:14Z"
    }
  ],
  "kind": "Collection",
  "self": "/collections"
}
```

### Inviter retrieves the list of connections

```shell
curl -X 'GET' 'http://localhost:8080/cloud-agent/connections' \
    -H "apikey: $API_KEY" | jq
```

Example response:

```json
{
  "contents": [
    {
      "connectionId": "1893e207-4cba-4792-8179-067c78d2acc2",
      "createdAt": "2023-02-02T17:14:13Z",
      "invitation": {
        "from": "did:peer:issuer-did",
        "id": "1893e207-4cba-4792-8179-067c78d2acc2",
        "invitationUrl": "https://domain.com/path?_oob={RAW_INVITATION}",
        "type": "https://didcomm.org/out-of-band/2.0/invitation"
      },
      "kind": "/connections/1893e207-4cba-4792-8179-067c78d2acc2",
      "label": "Connect with Alice",
      "myDid": "did:peer:issuer-did",
      "self": "Connection",
      "state": "ConnectionResponseSent",
      "theirDid": "did:peer:holder-did",
      "updatedAt": "2023-02-02T18:06:16Z"
    }
  ],
  "kind": "Collection",
  "self": "/collections"
}
```

:::info Please check the full [Cloud agent API](https://hyperledger-identus.github.io/docs/agent-api) specification for more detailed information. :::

---
