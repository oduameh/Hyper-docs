# Webhooks {#webhooks}

## Overview

Webhooks deliver real-time event notifications from the Cloud agent to your application. Instead of polling for status changes, your integration receives HTTP callbacks when connections advance, credentials are issued, proofs are verified, or DIDs are published. This guide explains webhook concepts, configuration options, security considerations, and handling patterns.

---

## Webhook fundamentals

### What are webhooks?

Webhooks send HTTP requests to a registered endpoint when an event occurs. The request body carries the event payload so your application can react immediately without polling.

### Cloud agent webhook scope

The Cloud agent emits webhooks for the [Connect](https://hyperledger-identus.github.io/docs/tutorials/connections/connection), [Issue](https://hyperledger-identus.github.io/docs/tutorials/credentials/didcomm/issue), [Presentation](https://hyperledger-identus.github.io/docs/tutorials/credentials/didcomm/present-proof), and [DID publication](https://hyperledger-identus.github.io/docs/tutorials/dids/publish) flows. Each state change in those protocols generates a callback that includes the resource identifier, the new state, and the wallet that triggered the event.

## Configure the webhook feature

### Enable webhook delivery

The Cloud agent supports global webhooks that capture events from every wallet and wallet-scoped webhooks that capture events for a single wallet.

**Enable global webhooks with environment variables**

The following environment variables configure global webhook notifications:

| Name | Description | Default |
| :---- | :---- | :---- |
| `GLOBAL_WEBHOOK_URL` | The webhook endpoint URL where the notifications will be sent | null |
| `GLOBAL_WEBHOOK_API_KEY` | The optional API key (bearer token) to use as the `Authorization` header | null |

**Enable default wallet webhooks with environment variables**

In multi-tenant deployments, the Cloud agent can create a default wallet to simplify development. Configure that wallet’s webhook through environment variables. After the wallet exists, subsequent changes must use the API because the stored settings override the environment variables.

| Name | Description | Default |
| :---- | :---- | :---- |
| `DEFAULT_WALLET_ENABLED` | Automatically create default on the Cloud agent startup | true |
| `DEFAULT_WALLET_WEBHOOK_URL` | The webhook endpoint URL where the notifications will be sent | null |
| `DEFAULT_WALLET_WEBHOOK_API_KEY` | The optional API key (bearer token) to use as the `Authorization` header | null |

**Enable wallet webhooks with the REST API**

In multi-tenant deployments you can manage each wallet’s webhook using the REST API. Create a webhook by sending a POST request to `/events/webhooks`, which returns the resource definition for that wallet.

```shell
curl --location --request POST 'http://localhost:8080/cloud-agent/events/webhooks' \
  --header 'Content-Type: application/json' \
  --header 'Accept: application/json' \
  --header "apiKey: $API_KEY" \
  --data-raw '{
    "url": "http://localhost:9095"
  }'
```

Response Example:

```json
{
    "id": "e9569dd0-bffa-4be4-94fe-f5025a79029a",
    "url": "http://localhost:9095",
    "customHeaders": {},
    "createdAt": "2023-09-12T08:39:03.871339Z"
}
```

### Secure the webhook endpoint

Protect the webhook endpoint to maintain the integrity and confidentiality of event data.

- Use HTTPS to encrypt traffic between the Cloud agent and your service.
- Authenticate incoming requests with API keys or tokens.
- Validate and sanitize each payload before processing it.

When you configure `GLOBAL_WEBHOOK_API_KEY` or `DEFAULT_WALLET_WEBHOOK_API_KEY`, the Cloud agent includes the value as a bearer token in the `Authorization` header. For more complex scenarios, use the REST API `customHeaders` property to define any additional headers required by your endpoint.

## Event format and types

### Event format

Webhook notifications use JSON payloads with a consistent structure. The top-level fields identify the event, and the `data` field contains the payload that matches the event type.

```json
{
  "id": "cb8d4e96-30f0-4892-863f-44d49d634211",
  "ts": "2023-07-06T12:01:19.769427Z",
  "type": "xxxx",
  "data": {
    // Event-specific data goes here 
  },
  "walletId": "00000000-0000-0000-0000-000000000000"
}
```

This format lets you handle every webhook uniformly while focusing on the event-specific details in `data`.

The following example shows a connection state change when an invitation is generated:

```json
{
  "id": "cb8d4e96-30f0-4892-863f-44d49d634211",
  "ts": "2023-07-06T12:01:19.769427Z",
  "type": "ConnectionUpdated",
  "data": {
    "connectionId": "c10787cf-99bb-47f4-99bb-1fdcca32b673",
    "label": "Connect with Alice",
    "role": "Inviter",
    "state": "InvitationGenerated",
    "invitation": {
      "id": "c10787cf-99bb-47f4-99bb-1fdcca32b673",
      "type": "https://didcomm.org/out-of-band/2.0/invitation",
      "from": "did:peer:2.Ez6LS...jIiXX0",
      "invitationUrl": "https://my.domain.com/path?_oob=eyJpZCI6...bXX19"
    },
    "createdAt": "2023-07-06T12:01:19.760126Z",
    "self": "c10787cf-99bb-47f4-99bb-1fdcca32b673",
    "kind": "Connection"
  },
  "walletId": "00000000-0000-0000-0000-000000000000"
}
```

### Common event types

The `id` field contains the unique event identifier, `ts` records the timestamp, and `walletId` identifies the wallet that produced the event. The `type` field maps to the protocol that triggered the notification. Possible values include:

| Value | Description |
| :---- | :---- |
| `ConnectionUpdated` | An update in the connection flow state |
| `IssueCredentialRecordUpdated` | An update in the VC issuance flow state |
| `PresentationUpdated` | An update in the VC presentation flow state |
| `DIDStatusUpdated` | An update in the DID publication state |

You can subscribe to state changes throughout the protocols:

- **Connection state changes.** Track updates such as `InvitationGenerated`, `ConnectionRequestSent`, and `ConnectionResponseReceived`. For the full list, see the `state` field in the [connection resource](https://hyperledger-identus.github.io/docs/agent-api/#tag/Connections-Management/operation/getConnection).
- **Credential state changes.** Follow issuance events like `OfferSent`, `RequestReceived`, and `CredentialSent`. Review the `protocolState` field in the [credential resource](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/getCredentialRecord) for every possible value.
- **Presentation state changes.** Monitor proof workflows with statuses such as `RequestReceived`, `PresentationGenerated`, and `PresentationVerified`. Refer to the `status` field in the [presentation resource](https://hyperledger-identus.github.io/docs/agent-api/#tag/Present-Proof/operation/getPresentation).
- **DID publication state changes.** Receive the `Published` event when a DID becomes available.

## Process webhook notifications

### Handle incoming webhook requests

Use the following steps to process Cloud agent callbacks:

1. Receive the HTTP request at your webhook endpoint.
2. Parse the JSON payload and extract the event details.
3. Run your application logic based on the event contents.
4. Return a `2xx` response to acknowledge receipt. Any other status code triggers a retry.

### Handle retries

The Cloud agent retries failed webhook deliveries up to three times with five-second intervals. Retries are currently not configurable, so design idempotent handlers that can safely process the same event more than once.

### A basic webhook implementation for logging requests

The following Python example sets up a webhook endpoint and logs incoming HTTP requests to the console. Pass the listening port as a command-line parameter so you can run multiple listeners in parallel while testing issuer, holder, and verifier agents.

```py
#!/usr/bin/env python3
"""
Very simple HTTP server in python for logging requests
Usage::
    ./server.py [<port>]
"""
import logging
import json
from http.server import BaseHTTPRequestHandler, HTTPServer

grey = "\x1b[38;20m"
yellow = "\x1b[33;20m"
green = "\x1b[32;20m"
red = "\x1b[31;20m"
bold_red = "\x1b[31;1m"
reset = "\x1b[0m"

consoleHandler = logging.StreamHandler()
formatter = logging.Formatter(f"""%(asctime)s - %(levelname)s - %(name)s
--------------------------------------- request ---------------------------------------
{green}%(method)s %(path)s{reset}
%(headers)s
{yellow}%(data)s{reset}
-----------------------------------
"""
                              )
consoleHandler.setFormatter(formatter)
consoleHandler.setLevel(logging.INFO)

logger = logging.getLogger('http-request')
logger.setLevel(logging.INFO)
logger.addHandler(consoleHandler)

class S(BaseHTTPRequestHandler):

    def _set_response(self):
        self.send_response(200)
        self.send_header('Content-type', 'text/html')
        self.end_headers()

    def do_GET(self):
        logging.info("GET request,\nPath: %s\nHeaders:\n%s\n", str(self.path), str(self.headers))
        self._set_response()
        self.wfile.write("GET request for {}".format(self.path).encode('utf-8'))

    def do_POST(self):
        content_length = int(self.headers['Content-Length'])  # <--- Gets the size of data
        post_data = self.rfile.read(content_length)  # <--- Gets the data itself
        json_obj = json.loads(post_data.decode('utf-8'))
        json_data = json.dumps(json_obj, indent=2)
        logger.info(msg="Request content", extra={
            'method': "POST",
            'path': str(self.path),
            'headers': str(self.headers),
            'data': json_data
        })
        self._set_response()
        self.wfile.write("POST request for {}".format(self.path).encode('utf-8'))

    def log_message(self, format, *args):
        pass


def run(server_class=HTTPServer, handler_class=S, port=80):
    server_address = ('', port)
    httpd = server_class(server_address, handler_class)
    try:
        httpd.serve_forever()
    except KeyboardInterrupt:
        pass
    httpd.server_close()


if __name__ == '__main__':
    from sys import argv

    if len(argv) == 2:
        run(port=int(argv[1]))
    else:
        run()
```

## Conclusion

Webhooks keep your integration synchronized with Cloud agent activity without polling. Secure each endpoint, configure the appropriate scope, and design idempotent handlers so your services can respond to SSI events as soon as they occur.
