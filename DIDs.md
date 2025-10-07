# DIDs {#dids}

## Understanding decentralized identifiers

Decentralized identifiers (DIDs) are the cornerstone of self-sovereign identity. In Hyperledger Identus, PRISM DIDs are created offline, optionally published to the Cardano blockchain, and can be updated or deactivated throughout their lifecycle. This section covers the complete DID management process, from creation through deactivation.

Understanding DID lifecycle management is essential for issuing credentials (DIDs are used as issuer identifiers) and for holders who need DIDs to receive and present credentials.

---

## Creating a DID

PRISM DIDs are a type of [decentralized identifier (DID)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#decentralized-identifier) used across the Atala PRISM product suite.

It is a variation of a [sidetree protocol](https://identity.foundation/sidetree/spec/) and uses the Cardano blockchain as the underlying ledger for [DID resolution](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-resolution) and operation publication. Please refer to the [PRISM method specification](https://github.com/input-output-hk/prism-did-method-spec/blob/main/w3c-spec/PRISM-method.md) for a more detailed explanation of how it works.

PRISM DIDs can be created entirely offline without interacting with the blockchain by constructing a DID create-operation, a [protocol buffer (protobuf)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#protocol-buffer) message with a set of public keys and services. Once the create-operation gets constructed, deriving a DID from this operation is possible, which is well-defined by the [PRISM DID method](https://github.com/input-output-hk/prism-did-method-spec/blob/main/w3c-spec/PRISM-method.md).

## Defining roles

1. [DID Controller](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-controller) is the organization or individual who has control of the DID.

## Meeting the prerequisites

1. DID Controller Cloud agent up and running.

## Reviewing the example

For this example, a PRISM DID gets created and stored inside the Cloud agent along with the private keys. It is not automatically published. The Agent will keep track of private keys used for the create-operation and the content of the operation itself.

The Cloud agent provides two endpoint groups to facilitate PRISM DID usage.

- `/dids/*` facilitate of low-level interactions between DID operations and the blockchain. The DID controllers will handle key management independently and use these endpoints for blockchain interaction.  
    
- `/did-registrar/*` Facilitates a higher-level interaction with PRISM DID, where the Cloud agent handles key management concerns.

## Reviewing the endpoints

The example uses the following endpoints.

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| [`GET /did-registrar/dids`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID-Registrar/operation/listManagedDid) | List all DIDs stored in the Cloud agent | DID Controller |
| [`POST /did-registrar/dids`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID-Registrar/operation/createManagedDid) | Create a new PRISM DID to be managed by the Cloud agent | DID Controller |
| [`GET /dids/{didRef}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID/operation/getDid) | Resolve a DID to DID document representation | DID Controller |

## Managing DID controller interactions

### Checking an existing DID on the Cloud agent

```shell
curl --location --request GET 'http://localhost:8080/cloud-agent/did-registrar/dids' \
  --header "apikey: $API_KEY" \
  --header 'Accept: application/json'
```

The result should show an empty list, as no DIDs exist on this Cloud agent instance.

### Creating the Cloud agent managed DID using the DID registrar endpoint

The DID controller can create a new DID by sending a [DID document](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-document) template to the Agent. Since key pairs are generated and managed by the Cloud agent, DID controller only has to specify the key `id`, `purpose` (`authentication`, `assertionMethod`, etc.), and optional `curve` (`secp256k1`, `Ed25519`, `X25519`). If the `curve` is omitted, the agent uses the `secp256k1` curve by default.

```shell
curl --location --request POST 'http://localhost:8080/cloud-agent/did-registrar/dids' \
  --header 'Content-Type: application/json' \
  --header 'Accept: application/json' \
  --header "apikey: $API_KEY" \
  --data-raw '{
    "documentTemplate": {
      "publicKeys": [
        {
          "id": "auth-1",
          "purpose": "authentication",
          "curve": "secp256k1"
        }
      ],
      "services": []
    }
  }'
```

The response should look like

```json
{
    "longFormDid": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff:Cr4BCrsBElsKBmF1dGgtMRAEQk8KCXNlY3AyNTZrMRIg0opTuxu-zt6aRbT1tPniG4eu4CYsQPM3rrLzvzNiNgwaIIFTnyT2N4U7qCQ78qtWC3-p0el6Hvv8qxG5uuEw-WgMElwKB21hc3RlcjAQAUJPCglzZWNwMjU2azESIKhBU0eCOO6Vinz_8vhtFSAhYYqrkEXC8PHGxkuIUev8GiAydFHLXb7c22A1Uj_PR21NZp6BCDQqNq2xd244txRgsQ"
}
```

### Listing the created DID

Check the `GET /did-registrar/dids` endpoint. The response should return a list containing one DID.

```json
{
    "contents": [
        {
            "did": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff",
            "longFormDid": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff:Cr4BCrsBElsKBmF1dGgtMRAEQk8KCXNlY3AyNTZrMRIg0opTuxu-zt6aRbT1tPniG4eu4CYsQPM3rrLzvzNiNgwaIIFTnyT2N4U7qCQ78qtWC3-p0el6Hvv8qxG5uuEw-WgMElwKB21hc3RlcjAQAUJPCglzZWNwMjU2azESIKhBU0eCOO6Vinz_8vhtFSAhYYqrkEXC8PHGxkuIUev8GiAydFHLXb7c22A1Uj_PR21NZp6BCDQqNq2xd244txRgsQ",
            "status": "CREATED"
        }
    ],
    "kind": "ManagedDIDPage",
    "pageOf": "http://localhost:8080/cloud-agent/did-registrar/dids",
    "self": "http://localhost:8080/cloud-agent/did-registrar/dids"
}
```

### Resolving the created DID

To check that the DID document is correctly populated, test the created DID against the resolution endpoint.

Replacing the `{didRef}` with the long-form DID, and the response should return the DID document. Replacing the `{didRef}` with the short-form DID, and the resolution should fail since the DID still needs to be published.

```shell
curl --location --request GET 'http://localhost:8080/cloud-agent/dids/{didRef}' \
--header "apikey: $API_KEY" \
--header 'Accept: */*'
```

Example DID document response (some fields omitted for readability)

```json
{
    "@context": "https://w3id.org/did-resolution/v1",
    "didDocument": {
        "@context": ["https://www.w3.org/ns/did/v1"],
        ...
        "id": "did:prism:62675a438616773280f70e4f4d1047133fc56bb183758fcccd5d5714ea5b1959:Cr0BCroBEloKBWtleS0xEARCTwoJc2VjcDI1NmsxEiDRh7iIj8WKJ28nde1uc6ZnEBWIwEVMXlIEmrqCo-bE5Bogn6o2TzP0HzekLOhA-06MrIpOuaaHL_Rhy01wyjV4ypsSXAoHbWFzdGVyMBABQk8KCXNlY3AyNTZrMRIg0y28R1CS3F0-kwNcQShdRhtcvz-LQlI86z1DIYrKM7oaIPkmCAegj-sSaAy0zTxrR9F4TSXB-62vCQxIsEovkEcA",
        "verificationMethod": [
            {...}
        ],
    },
    "didDocumentMetadata": {...},
    "didResolutionMetadata": {...}
}
```

---

## Publishing a DID

PRISM DID creation involves generating key pairs and additional data (e.g., services) to construct a create-operation. The create-operation allows [DID Controller](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-controller) to derive two types of [DIDs](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#decentralized-identifiers):

1. [Long-form DID](https://github.com/input-output-hk/prism-did-method-spec/blob/main/w3c-spec/PRISM-method.md#long-form-dids-unpublished-dids). It has the following format `did:prism:<initial-hash>:<encoded-state>`  
2. Short-form DID. It has the following format `did:prism:<initial-hash>`

The difference is the long-form DID contains the encoded create-operation in the DID itself while, the short-form DID doesn't have this information. As a result, when resolving the short-form DID, the resolver must look for additional data from the blockchain. On the other hand, resolving long-form DID is self-contained. Even if the data is not on the blockchain, the resolver can still work out the DID document of the long-form DID.

The resolution of short-form DID is achievable by DID publication, which is a process of putting the create-operation on the blockchain.

## Defining roles

1. DID Controller is the organization or individual who has control of the DID.

## Meeting the prerequisites

1. DID Controller Cloud agent up and running  
2. DID Controller has a DID created by the Cloud agent (see [Create DID](https://hyperledger-identus.github.io/docs/tutorials/create.md))

## Understanding the publication flow

Publishing a DID requires the DID create-operation and the DID `MASTER` key pairs, which the Cloud agent already created under the hood. When the DID Controller requests a publication of their DID, the Cloud agent uses the DID `MASTER` key to sign the operation and submit the signed operation to the blockchain. After the operation submission to the blockchain, a specific number of confirmation blocks must get created before the DID operation is processed and published. (see [PRISM DID method \- Processing of DID operation](https://github.com/input-output-hk/prism-did-method-spec/blob/main/w3c-spec/PRISM-method.md#processing-of-operations))

## Reviewing the endpoints

The example uses the following endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| [`GET /did-registrar/dids/{didRef}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID-Registrar/operation/getManagedDid) | Get the DID stored in the Cloud agent | DID Controller |
| [`POST /did-registrar/dids/{didRef}/publications`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID-Registrar/operation/publishManagedDid) | Publish the DID stored in the Cloud agent to the blockchain | DID Controller |
| [`GET /dids/{didRef}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID/operation/getDid) | Resolve a DID to DID document representation | DID Controller |

## Managing DID controller interactions

### Checking the status of an unpublished DID

DID Controller checks the status by replacing the `{didRef}` with the unpublished DID on the Agent. The `{didRef}` path segment can be either short-form or long-form DID. When a DID gets created and not published, it has the status of `CREATED`.

```shell
curl --location --request GET 'http://localhost:8080/cloud-agent/did-registrar/dids/{didRef}' \
--header "apikey: $API_KEY" \
--header 'Accept: application/json'
```

Example response

```json
{
    "did": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff",
    "longFormDid": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff:Cr4BCrsBElsKBmF1dGgtMRAEQk8KCXNlY3AyNTZrMRIg0opTuxu-zt6aRbT1tPniG4eu4CYsQPM3rrLzvzNiNgwaIIFTnyT2N4U7qCQ78qtWC3-p0el6Hvv8qxG5uuEw-WgMElwKB21hc3RlcjAQAUJPCglzZWNwMjU2azESIKhBU0eCOO6Vinz_8vhtFSAhYYqrkEXC8PHGxkuIUev8GiAydFHLXb7c22A1Uj_PR21NZp6BCDQqNq2xd244txRgsQ",
    "status": "CREATED"
}
```

### Requesting publication of a DID

To publish a DID, use DID Controller `POST` a request to `/did-registrar/dids/{didRef}/publications` endpoint.

```shell
curl --location --request POST 'http://localhost:8080/cloud-agent/did-registrar/dids/{didRef}/publications' \
--header "apikey: $API_KEY" \
--header 'Accept: application/json'
```

The Cloud agent will retrieve a DID `MASTER` key and sign the operation before submitting it to the blockchain. The process is asynchronous, and it takes time until the operation is confirmed. The DID Controller receives a scheduled operation as a response.

```json
{
    "scheduledOperation": {
        "didRef": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff",
        "id": "0cf945eaf3be143e5d64eef992666f3c613ae986fdb34e71ef4a9d2f25a6704f"
    }
}
```

The response contains the `scheduledOperation` property, which describes a scheduled operation. The submitted DID operations are batched together along with other operations to reduce the transaction cost when interacting with the blockchain.

The Cloud agent will eventually expose an endpoint to check the status of a scheduled operation. Checking the publishing status is possible by following Step 3\.

### Waiting for the DID operation confirmation

The DID Controller checks the DID status the same as in Step 1\. The status of the DID has changed to `PUBLICATION_PENDING`.

Example response with status `PUBLICATION_PENDING`

```json
{
    "did": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff",
    "longFormDid": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff:Cr4BCrsBElsKBmF1dGgtMRAEQk8KCXNlY3AyNTZrMRIg0opTuxu-zt6aRbT1tPniG4eu4CYsQPM3rrLzvzNiNgwaIIFTnyT2N4U7qCQ78qtWC3-p0el6Hvv8qxG5uuEw-WgMElwKB21hc3RlcjAQAUJPCglzZWNwMjU2azESIKhBU0eCOO6Vinz_8vhtFSAhYYqrkEXC8PHGxkuIUev8GiAydFHLXb7c22A1Uj_PR21NZp6BCDQqNq2xd244txRgsQ",
    "status": "PUBLICATION_PENDING"
}
```

If the operation is confirmed, the status becomes `PUBLISHED`. The `longFormDid` property is no longer mandatory for published DID.

Example response with status `PUBLISHED`

```json
{
    "did": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff",
    "status": "PUBLISHED"
}
```

**Note:** The `status` here is the internal status of the DID on the Cloud agent (`PUBLISHED`, `CREATED`, `PUBLICAION_PENDING`). It does not indicate the lifecycle of the DID observed on the blockchain (e.g., deactivated, etc.). The [DID resolution](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-resolution) metadata is for that purpose.

### Resolving a short-form DID

Only published DID can be resolved using short-form. To confirm that the short-form DID is resolvable, test the DID against the resolution endpoint.

Replace `{didRef}` with the short-form DID; the response should return a DID document.

```shell
curl --location --request GET 'http://localhost:8080/cloud-agent/dids/{didRef}' \
--header "apikey: $API_KEY" \
--header 'Accept: */*'
```

---

## Updating a DID

PRISM DID method allows [DID Controller](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-controller) to update the content of the [DID document](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-document) by constructing a DID update-operation. The update-operation describes the update action on the DID document. For example, DID Controller can add a new key to the DID document by constructing an update-operation containing the `AddKeyAction`. It is also possible for DID Controller to compose multiple actions in the same update-operation. The [PRISM DID method \- Update DID section](https://github.com/input-output-hk/prism-did-method-spec/blob/main/w3c-spec/PRISM-method.md#update-did) includes a complete list of supported update actions. The PRISM DID method only allows published [DID](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#decentralized-identifier) to be updated.

When updating a DID, each operation is connected through cryptography, forming a sequence of operations known as the lineage. It's important to note that the lineage should not have any forks, meaning that the operations should follow a single, uninterrupted path. Only the operations within the valid lineage are considered when updating the data on the DID document, while any operations on a forked lineage are disregarded and discarded by the protocol.

Please refer to [PRISM DID method \- processing of update operation](https://github.com/input-output-hk/prism-did-method-spec/blob/main/w3c-spec/PRISM-method.md#processing-of-updatedidoperations) for more detail about how a DID update-operation is processed. It has an important implication on how the operation lineage is determined.

## Defining roles

1. DID Controller is the organization or individual who has control of the DID.

## Meeting the prerequisites

1. DID Controller Cloud agent is up and running  
2. DID Controller has a DID created by the Cloud agent (see [Create DID](https://hyperledger-identus.github.io/docs/tutorials/create.md))  
3. DID Controller has a DID published to the blockchain (see [Publish DID](https://hyperledger-identus.github.io/docs/tutorials/publish.md))

## Understanding the update flow

The Cloud agent allows the DID Controller to update the DID easily. This update mechanism is implementation specific and links the DID update-operation from the last confirmed operation observed on the blockchain.

When updating a DID, there is a waiting period for the update to be confirmed on the blockchain. If the DID Controller updates the DID before the previous update is confirmed, it can create a situation where the sequence of updates splits into two separate paths, which is not allowed according to the protocol. This happens because the Cloud agent connects the update operation to the latest confirmed update. Once the pending update operation is confirmed, any other pending operation that does not link to the latest confirmed operation will be discarded. The subsequent updates continuing from that operation will also be discarded. However, the Cloud agent has a safeguard in place to prevent this issue by rejecting multiple updates submitted on the same DID while previous updates are still being processed.

Please refer to the `SECURE_DEPTH` parameter in [PRISM method \- protocol parameters](https://github.com/input-output-hk/prism-did-method-spec/blob/main/w3c-spec/PRISM-method.md#versioning-and-protocol-parameters) for the number of confirmation blocks. At the time of writing, this number is 112 blocks.

This example shows the DID update capability on the Cloud agent and the steps to verify that the update has been confirmed and applied.

## Reviewing the endpoints

The example uses the following endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| [`POST /did-registrar/dids/{didRef}/updates`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID-Registrar/operation/updateManagedDid) | Update a PRISM DID | DID Controller |
| [`GET /dids/{didRef}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID/operation/getDid) | Resolve a DID to DID document representation | DID Controller |

## Managing DID controller interactions

### Checking the current state of the DID document

Given the DID Controller has a DID on the Cloud agent and that DID is published, he can resolve the DID document using short-form DID.

```shell
curl --location --request GET 'http://localhost:8080/cloud-agent/dids/{didRef}' \
--header "apikey: $API_KEY" \
--header 'Accept: */*'
```

Example DID document response (some fields omitted for readability)

```json
{
    "@context": "https://w3id.org/did-resolution/v1",
    "didDocument": {
        "@context": ["https://www.w3.org/ns/did/v1"],
        ...
        "verificationMethod": [
            {
                "controller": "did:prism:4262377859267f308a06ec6acf211fbe4d6745aa9e637e04548771169616fb86",
                "id": "did:prism:4262377859267f308a06ec6acf211fbe4d6745aa9e637e04548771169616fb86#key-1",
                "publicKeyJwk": {
                    "crv": "secp256k1",
                    "kty": "EC",
                    "x": "biEpgXMrmKMghF8LsXbIR0VDyIO31mnPkbJBpGDYH1g",
                    "y": "0YLIMfxY0_3J8Db6W0I1wcHZG3vRCRndNEnVn4h2V7Y"
                },
                "type": "EcdsaSecp256k1VerificationKey2019"
            }
        ]
        ...
    },
    "didDocumentMetadata": {...},
    "didResolutionMetadata": {...}
}
```

The `verificationMethod` in the DID document only shows one public key called `key-1`.

### Adding a new key and removing the existing key

The current DID document contains a key called `key-1`. The DID Controller wishes to remove that key and add a new key called `key-2`

The DID Controller submits a DID update request to `POST /did-registrar/dids/{didRef}/updates`.

```shell
curl --location --request POST 'http://localhost:8080/cloud-agent/did-registrar/dids/did:prism:4262377859267f308a06ec6acf211fbe4d6745aa9e637e04548771169616fb86/updates' \
--header 'Content-Type: application/json' \
--header 'Accept: application/json' \
--header "apikey: $API_KEY" \
--data-raw '{
    "actions": [
        {
            "actionType": "REMOVE_KEY",
            "removeKey": {
                "id": "key-1"
            }
        },
        {
            "actionType": "ADD_KEY",
            "addKey": {
                "id": "key-2",
                "purpose": "authentication",
                "curve": "secp256k1"
            }
        }
    ]
}'
```

Under the hood, the Cloud agent constructs the DID update-operation from the last confirmed operation observed on the blockchain. The DID Controller should receive a response about the scheduled operation, waiting for confirmation on the blockchain.

```json
{
    "scheduledOperation": {
        "didRef": "did:prism:4262377859267f308a06ec6acf211fbe4d6745aa9e637e04548771169616fb86",
        "id": "cb61cff083e27e2f8bc35b0e561780dc027c4f0072d2a2e478b2e0f26e3783b0"
    }
}
```

### Waiting for the confirmation and observing the change on the DID document

When the DID Controller tries to resolve the DID again using the example in Step 1, the content might remain the same because the operation still needs to be confirmed and applied.

The DID Controller keeps polling this endpoint until the new key, `key-2`, gets observed.

Example response of updated DID document (some fields omitted for readability)

```json
{
    "@context": "https://w3id.org/did-resolution/v1",
    "didDocument": {
        "@context": ["https://www.w3.org/ns/did/v1"],
        ...
        "verificationMethod": [
            {
                "controller": "did:prism:4262377859267f308a06ec6acf211fbe4d6745aa9e637e04548771169616fb86",
                "id": "did:prism:4262377859267f308a06ec6acf211fbe4d6745aa9e637e04548771169616fb86#key-2",
                "publicKeyJwk": {
                    "crv": "secp256k1",
                    "kty": "EC",
                    "x": "sg5X06yRDNaW2YcuMuOPwrDPp_vqOtKng0hMHxaME10",
                    "y": "uAKJanSsNoC_bcL4YS93qIqu_Qwdsr_80DzRTzI8RLU"
                },
                "type": "EcdsaSecp256k1VerificationKey2019"
            }
        ]
        ...
    },
    "didDocumentMetadata": {...},
    "didResolutionMetadata": {...}
}
```

A new key, called `key-2`, gets observed, and `key-1`, now removed, has disappeared from the `verificationMethod`.

## Planning future work

The example only shows the case of a successful update. In case of failure, the Cloud agent will provide the capability to retrieve the low-level operation status and failure detail in the future.

---

## Deactivating a DID

DID deactivation is an important feature that provides greater control for managing digital identity. DID deactivation can be helpful if the [DID](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#decentralized-identifier) is compromised or unused. This feature is crucial for the security and risk management of identity owners.

Similar to [DID update](https://hyperledger-identus.github.io/docs/tutorials/update.md), deactivating a PRISM DID is a process of putting deactivate-operation on the blockchain so that other participants know that the DID is no longer active. The PRISM DID method only allows published DID to be deactivated.

## Defining roles

1. DID Controller is the organization or individual who has control of the DID.

## Meeting the prerequisites

1. DID Controller Cloud agent up and running  
2. DID Controller has a DID created on the Cloud agent (see [Create DID](https://hyperledger-identus.github.io/docs/tutorials/create.md))  
3. DID Controller has a DID published to the blockchain (see [Publish DID](https://hyperledger-identus.github.io/docs/tutorials/publish.md))

## Understanding the deactivation flow

DID deactivation operates similarly to the DID update, where deactivate-operation publishes to the blockchain, and confirmation blocks must be created before participants consider it deactivated. Once the DID is deactivated, all content in the [DID document](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-document) gets deleted, and no operation will affect the DID afterward. The same concept also holds for PRISM DID deactivation in that the fork can occur if any subsequent operation occurs before the operation is confirmed. Please refer to the `SECURE_DEPTH` parameter in [PRISM method \- protocol parameters](https://github.com/input-output-hk/prism-did-method-spec/blob/main/w3c-spec/PRISM-method.md#versioning-and-protocol-parameters) for the number of confirmation blocks. At the time of writing, this number is 112 blocks.

DID deactivation is easily performed with the Cloud agent. Under the hood, the Cloud agent uses the `MASTER` keys to sign the intended operation and automatically post the operation to the blockchain. This example shows the DID deactivation and steps to observe the changes to the DID using the Cloud agent.

## Reviewing the endpoints

The example uses the following endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| [`POST /did-registrar/dids/{didRef}/deactivations`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID-Registrar/operation/deactivateManagedDid) | Deactivate a PRISM DID | DID Controller |
| [`GET /dids/{didRef}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID/operation/getDid) | Resolve a DID to DID document representation | DID Controller |

## Managing DID controller interactions

### Checking the current state of the DID document

Given the DID Controller has a DID managed by the Cloud agent and that DID is published, he can resolve the DID document using short-form DID.

```shell
curl --location --request GET 'http://localhost:8080/cloud-agent/dids/{didRef}' \
--header "apikey: $API_KEY" \
--header 'Accept: */*'
```

Example DID document response (some fields omitted for readability)

```json
{
    "didDocument": {...},
    "didDocumentMetadata": {
        "canonicalId": "did:prism:66e431434f201c7ae43f6e63569f1ee556d7dfbee1646101547324013e545d2c",
        "deactivated": false,
        ...
    },
    "didResolutionMetadata": {...}
}
```

The DID metadata shows the `deactivation` status as `false`, meaning that this DID is still active.

### Requesting DID deactivation from the PRISM agent

The active status comes from the last step. The DID deactivation can be performed by calling `POST /did-registrar/dids/{didRef}/deactivations` and replacing `{didRef}` with the DID to deactivate.

```shell
curl --location --request POST 'http://localhost:8080/cloud-agent/did-registrar/dids/{didRef}/deactivations' \
--header "apikey: $API_KEY" \
--header 'Accept: application/json'
```

Under the hood, the Cloud agent constructs the DID deactivate-operation from the last confirmed operation observed on the blockchain. The DID Controller should receive a response about the scheduled operation, waiting for confirmation on the blockchain. If this deactivate-operation gets confirmed on the blockchain and not discarded as a fork, the DID becomes deactivated.

### Waiting for the confirmation and observing the change in the DID metadata

When the DID Controller tries to resolve the DID again using the example in Step 1, the content might remain the same because the operation still needs to be confirmed and applied.

The DID Controller keeps polling this endpoint until the `deactivated` status in DID document metadata changes to `true`.

Example response of deactivated DID document (some fields omitted for readability)

```json
{
    "didDocumentMetadata": {
        "canonicalId": "did:prism:66e431434f201c7ae43f6e63569f1ee556d7dfbee1646101547324013e545d2c",
        "deactivated": true,
        ...
    },
    "didResolutionMetadata": {...}
}
```

The DID metadata indicates that the DID is deactivated and the `didDocument` is empty.

---
