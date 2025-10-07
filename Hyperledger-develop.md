# Hyperledger Identus – develop section {#hyperledger-identus-–-develop-section}

## Overview

The Develop section provides comprehensive tutorials for building self-sovereign identity solutions with Hyperledger Identus. Each tutorial includes step-by-step instructions, code examples, and explanations of the underlying concepts. Whether you're new to Identus or implementing specific features, this section guides you through the complete development process.

## How to navigate

**New to Identus?** Start with the [Quick start guide](#quick-start-guide) for a complete end-to-end workflow.

**Implementing specific features?** Jump to the relevant tutorial (Connections, DIDs, Schemas, Credentials, etc.). Each tutorial includes roles, prerequisites, and step-by-step instructions.

**Troubleshooting?** See [Troubleshooting and considerations](#troubleshooting-and-considerations).

## Developer roles

**Backend developers** – focus on DIDs, schemas, credential definitions, and credentials tutorials to build issuer and verifier services.

**Frontend developers** – start with the Quick start guide to understand the flow, then reference wallet SDK examples in the Quick Start.

**DevOps engineers** – review prerequisites in the Quick start guide, focus on multi-tenancy and secret management tutorials for secure infrastructure deployment.

## Tutorial conventions

Throughout this section, code examples use the following conventions:

- Issuer Keycloak: `http://localhost:9980/`  
- Issuer Agent: `http://localhost:8080/cloud-agent/`  
- Holder Agent: `http://localhost:8090/cloud-agent/`  
- Verifier Agent: `http://localhost:8100/cloud-agent/`

:::info To use the Identus Cloud agents, include an `apiKey` header in your requests:

```shell
export API_KEY=<your-api-key-value>
```

Alternatively, replace `$API_KEY` with your key in the CURL commands throughout these tutorials. :::

[Hyperledger Identus – develop section](#hyperledger-identus-–-develop-section)

[Quick start guide](#quick-start-guide)

[Connections](#connections)

[DIDs](#dids)

[Schemas](#schemas)

[Credential definitions](#credential-definitions)

[Multi-tenancy](#multi-tenancy)

[Secret management](#secret-management)

[VDR management](#vdr-management)

[Webhooks](#webhooks)

[Troubleshooting and considerations](#troubleshooting-and-considerations)

# Quick start guide {#quick-start-guide}

## Introduction to self-sovereign identity (SSI)

[Self-sovereign identity (SSI)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#self-sovereign-identity) is complex. This Quick Start Guide explains the fundamental concepts to get up and running with Hyperledger Identus. This guide will familiarize you with the general concepts and how to create [decentralized identifiers (DIDs)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#decentralized-identifier), issue credentials, make connections, and verify credentials with [verifiable presentations](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifiable-presentation). Refer to the Concepts and Components sections for a more in-depth explanation.

The trust triangle is the most basic process for conveying trust in the digital world. There are three roles in an SSI ecosystem: [Holders](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#holder), [Issuers](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#issuer), and [Verifiers](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifier).

Holders can be any entity, such as individuals, organizations, and digital or physical things. They will hold [verifiable credentials (VCs)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifiable-credential) and use a verifiable presentation to share their VCs.

Issuers can also be any entity that makes [claims](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#claim) about an [entity](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#entity). These claims are attestations, or evidence of something, about the Holder. As an example, an insurance company would provide proof of valid insurance.

Verifiers are the [relying party](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#relying-party) in the triangle. They will request information from the Holder, such as proof of insurance, and the Holder will use a verifiable presentation to share the appropriate VCs with the Verifier. The Holder's digital signature, the issuer DID get verified, and the contents therein to ensure nothing has been tampered with.

## Hyperledger Identus flow

The diagram details how the concepts fit alongside the Identus components in a typical SSI interaction.

## Hyperledger Identus components

Identus consists of core libraries that facilitate typical SSI interactions between [Issuers](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#issuer), [Holders](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#holder), and [Verifiers](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifier).

### Cloud agent

A Cloud agent can issue, hold, and verify [verifiable credentials (VCs)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifiable-credential) for any entity and manage [decentralized identifiers (DIDs)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#decentralized-identifier) and DID-based connections. The Cloud agent has an easy-to-use REST API to enable easy integration into any solution and uses [DIDComm V2](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#didcomm) as a messaging protocol for Cloud agent-to-Cloud agent communication.

It is maintained as an open source through the [Hyperledger Identus](https://www.hyperledger.org/projects/identus).

More in-depth documentation about [Cloud agent](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#cloud-agent) can be found in the [Cloud agent overview](https://hyperledger-identus.github.io/docs/home/identus/cloud-agent/overview).

### Wallet SDKs

[Wallet SDKs](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#wallet-sdk) for web and mobile (iOS, Android, TypeScript) enable identity holders to store credentials and respond to proof requests. They are typically used in applications that allow identity holders to interact with issuers and verifiers.

More in-depth documentation about the different Wallet SDKs can be found here ([TypeScript](https://hyperledger-identus.github.io/docs/sdk-ts/sdk/), [Swift](https://hyperledger-identus.github.io/sdk-swift/documentation/edgeagentsdk/), [KMP](https://hyperledger-identus.github.io/sdk-kmp/))

### Mediator

[Mediators](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#mediator) are for storing and relaying messages between Cloud agents and Wallet SDKs. They act as a proxy that remains connected to the network and receives any message, credential, or proof request on behalf of the Wallet SDKs (which can be offline occasionally).

More in-depth documentation about Mediator can be found in the [Mediator documentation](https://hyperledger-identus.github.io/docs/home/identus/mediator).

**Node for a Verifiable Data Registry (VDR)**   
To issue and verify VCs to and from DIDs, we need a [Verifiable Data Registry (VDR)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifiable-data-registry) that is globally resolvable and always on. In Identus's case, it is `prism-node`, [anchoring](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#anchoring) key information required for issuance and verification on the Distributed Ledger.

## Prerequisites

### Agent deployment

This guide will demonstrate a single-tenant deployment with API Key authentication disabled and an in-memory ledger for published DID storage, which is the simplest configuration to get started as a developer. More advanced configuration options can be found in [Multi-Tenancy Management](https://hyperledger-identus.github.io/docs/tutorials/multitenancy/tenant-onboarding) and associated [Environment Variables](https://hyperledger-identus.github.io/docs/home/identus/cloud-agent/environment-variables) configuration options.

We develop on modern machines equipped with either Intel-based x64 processors or Apple ARM processors with a minimum of four cores, 16 GB of memory, and 128GB+ of SSD-type storage.

1. To spin up a Cloud agent ,you must:  
* Have Git installed.  
* Have Docker installed.  
* Clone the [Identus Cloud agent repository](https://github.com/hyperledger/identus-cloud-agent).

```shell
git clone https://github.com/hyperledger/identus-cloud-agent
```

2. Once cloned, create a new file named  **./identus-cloud-agent/infrastructure/local/.env-issuer** to define the Issuer Agent environment variable configuration with the following content:

```
API_KEY_ENABLED=false
AGENT_VERSION=2.0.0
PRISM_NODE_VERSION=2.6.0
PORT=8000
NETWORK=identus
VAULT_DEV_ROOT_TOKEN_ID=root
PG_PORT=5432
```

3. Create a new file named  **./identus-cloud-agent/infrastructure/local/.env-verifier** to define the Verifier Agent environment variable configuration with the following content:

```
API_KEY_ENABLED=false
AGENT_VERSION=2.0.0
PRISM_NODE_VERSION=2.6.0
PORT=9000
NETWORK=identus
VAULT_DEV_ROOT_TOKEN_ID=root
PG_PORT=5433
```

4. Setting the `API_KEY_ENABLED` to `false` disables the requirement of using API Keys.

:::caution

API\_KEY\_ENABLED disables API Key authentication. This should **not** be used beyond Development purposes.

:::

5. Start the `issuer` and `verifier` Cloud agents by running the below commands in the terminal.  
* Issuer Cloud agent:

Mac OSX  terminal shell

```shell
 ./infrastructure/local/run.sh -n issuer -b -e ./infrastructure/local/.env-issuer -p 8000 -d "$(ipconfig getifaddr $(route get default | grep interface | awk '{print $2}'))"
```

Linux terminal shell

```shell
 ./infrastructure/local/run.sh -n issuer -b -e ./infrastructure/local/.env-issuer -p 8000 -d "$(ip addr show $(ip route show default | awk '/default/ {print $5}') | grep 'inet ' | awk '{print $2}' | cut -d/ -f1)"
```

* The Issuer [API endpoint](http://localhost:8000/cloud-agent/) will be accessible on port 8000 `http://localhost:8000/cloud-agent/` with a [Swagger Interface](http://localhost:8000/cloud-agent/redoc) available at `http://localhost:8000/cloud-agent/redoc`.  
    
* Verifier Cloud agent:

For Mac OSX  terminal shell

```shell
 ./infrastructure/local/run.sh -n verifier -b -e ./infrastructure/local/.env-verifier -p 9000 -d "$(ipconfig getifaddr $(route get default | grep interface | awk '{print $2}'))"
```

For the Linux terminal shell

```shell
 ./infrastructure/local/run.sh -n verifier -b -e ./infrastructure/local/.env-verifier -p 9000 -d "$(ip addr show $(ip route show default | awk '/default/ {print $5}') | grep 'inet ' | awk '{print $2}' | cut -d/ -f1)"
```

* The Verifier [API endpoint](http://localhost:9000/cloud-agent/) will be accessible on port 9000 `http://localhost:9000/cloud-agent/` with a [Swagger Interface](http://localhost:9000/cloud-agent/redoc) available at `http://localhost:9000/cloud-agent/redoc`.

### Agent configuration

**Creating LongForm PrismDID**

1. Run the following API request against your Issuer API to create a PRISM DID:

:::note

📌 **Note:** [To create DIDs with various supported curves](https://hyperledger-identus.github.io/docs/tutorials/dids/create#2-create-the-cloud-agent-managed-did-using-did-registrar-endpoint).

:::

```shell
curl --location \
--request POST 'http://localhost:8000/cloud-agent/did-registrar/dids' \
--header 'Accept: application/json' \
--data-raw '{
    "documentTemplate": {
        "publicKeys": [
        {
          "id": "auth-1",
          "purpose": "authentication"
        },
        {
          "id": "issue-1",
              "purpose": "assertionMethod"
        }
      ],
      "services": []
    }
}'
```

2. Publish the DID by replacing `{didRef}` with the `longFormDid` output value from the previous step.

```shell
curl --location \
--request POST 'http://localhost:8000/cloud-agent/did-registrar/dids/{didRef}/publications' \
--header 'Accept: application/json'
```

3. The short version of the DID is the publishedPrismDID.

:::info

📖Learn more about PRISM DIDs and why it is necessary to publish specific DIDs [here](https://staging-docs.atalaprism.io/tutorials/dids/publish).

:::

**Create a credential schema (JWT W3C Credential)**

1. To create a [credential schema](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#credential-schema) on the Issuer API instance, run the following request:

:::info

Replace the `[[publishedPrismDID]]` in the example request with the `did` value from the previous step.

:::

2. We need to capture the schema's guid as its used in further steps.

```shell
curl -X 'POST' \
  'http://localhost:8000/cloud-agent/schema-registry/schemas' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "driving-license",
  "version": "1.0.0",
  "description": "Driving License Schema",
  "type": "https://w3c-ccg.github.io/vc-json-schemas/schema/2.0/schema.json",
  "author": [[publishedPrismDID]],
  "tags": [
    "driving",
    "license"
  ],
  "schema": {
    "$id": "https://example.com/driving-license-1.0.0",
    "$schema": "https://json-schema.org/draft/2020-12/schema",
    "description": "Driving License",
    "type": "object",
    "properties": {
      "emailAddress": {
        "type": "string",
        "format": "email"
      },
      "givenName": {
        "type": "string"
      },
      "familyName": {
        "type": "string"
      },
      "dateOfIssuance": {
        "type": "string",
        "format": "date-time"
      },
      "drivingLicenseID": {
        "type": "string"
      },
      "drivingClass": {
        "type": "integer"
      }
    },
    "required": [
      "emailAddress",
      "familyName",
      "dateOfIssuance",
      "drivingLicenseID",
      "drivingClass"
    ],
    "additionalProperties": true
  }
}'
```

### Starting sample app

All wallet SDK's come bundled with a sample application, that cover all the Identus flows, including establishing connections, issuance, and verification flows.

1. Clone the [TypeScript SDK](https://github.com/hyperledger-identus/sdk-ts) repository.

```shell
git clone -b v6.6.0 https://github.com/hyperledger-identus/sdk-ts
```

2. Ensure you have all applications installed for building the SDK and their dependencies

[rust](https://www.rust-lang.org/tools/install) and [wasm-pack](https://rustwasm.github.io/wasm-pack/installer/) are leveraged to build and use the AnonCreds and DIDComm Rust libraries within TypeScript. To build the SDK locally or run demonstration applications, you must have these applications installed.

The following should work Linux and MacOS. If you experience any issues, refer to the latest installation instructions for your platform.

```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
curl https://rustwasm.github.io/wasm-pack/installer/init.sh -sSf | sh
```

3. Run the following:  
* Build the source SDK:

```shell
cd sdk-ts
git submodule update --init --recursive
npm i
npm run build
```

* Start the React demo:

```shell
cd demos/next
npm i
npm run build
npm run start
```

* This will start the React Wallet SDK TypeScript Demonstration at [http://localhost:3000](http://localhost:3000).  
1. Clone the [Swift SDK](https://github.com/hyperledger/identus-edge-agent-sdk-swift) repository.

```shell
git clone https://github.com/hyperledger/identus-edge-agent-sdk-swift
```

2. Open the XCode project on **./Sample/AtalaPrismWalletDemo/AtalaPrismWalletDemo.xcodeproj**  
3. On the top left of the XCode window you will see a Play/Run button, click it.  
4. The app will start.  
5. Click Wallet Demo 2.0  
* You will be able to run the rest of the guide here.  
1. Clone the [KMM SDK](https://github.com/hyperledger/identus-edge-agent-sdk-kmm) repository.

```shell
git clone https://github.com/hyperledger/identus-edge-agent-sdk-kmm
```

2. Open the Wallet SDK project on IntelliJ or Android Studio.  
3. In the `Run configuration` dropdown, select SampleApp.  
4. Select the device or emulator you want to use.  
5. Click "Run".  
* The SampleApp will launch on the applicable device or emulator.

### Deploy and establish mediation

Mediation is the process that ensures messages get routed and stored correctly between Issuers, Verifiers and Holders, even if they are offline. The mediator offers a service that is always running and can securely store messages and deliver them to the associated DIDs using DIDComm. This enables use-cases where connectivity to a (mobile) wallet cannot be guaranteed.

**Preparation**

1. To get the mediator deployed locally for the demo, clone the [Mediator repository](https://github.com/hyperledger/identus-mediator).

```shell
git clone https://github.com/hyperledger/identus-mediator
```

2. With a Docker service running, open a new terminal and run:

:::info

The latest mediator version can be found at [Mediator releases](https://github.com/hyperledger/identus-mediator/releases). Change the version in the example if you want to use the latest version.

:::

Mac OSX  terminal shell

```shell
MEDIATOR_VERSION=1.1.0 SERVICE_ENDPOINTS="http://$(ipconfig getifaddr $(route get default | grep interface | awk '{print $2}')):8080;ws://$(ipconfig getifaddr $(route get default | grep interface | awk '{print $2}')):8080/ws" docker compose up
```

Linux terminal shell

```shell
MEDIATOR_VERSION=1.1.0 SERVICE_ENDPOINTS="http://$(ip addr show $(ip route show default | awk '/default/ {print $5}') | grep 'inet ' | awk '{print $2}' | cut -d/ -f1):8080;ws://$(ip addr show $(ip route show default | awk '/default/ {print $5}') | grep 'inet ' | awk '{print $2}' | cut -d/ -f1):8080/ws" docker compose up
```

`MEDIATOR_ENDPOINTS` is then set to your local IP address:8080.

3. More advanced documentation and configuration options see the \[https://github.com/hyperledger/identus-mediator).  
     
4. Now you need to capture the Mediator's [Peer DID](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#peer-did) in order to start DIDCOMM V2 Mediation protocol, you can do so by opening you browser at the mediators [endpoint](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#endpoints).

**Demo application**

:::info

In order to complete this step you'll need to connect to the mediators Peer DID which you can fetch by making the following API request.

```shell
curl --location \
--request GET 'localhost:8080/invitation' \
--header 'Content-Type: application/json'
```

:::

Follow the steps in your desired platform as stated below:

1. Open [http://localhost:3000/debug](http://localhost:3000/debug) in your browser,  
2. paste the mediator peer DID (obtained from the `from` attribute after fetching from the mediator's invitation endpoint),  
3. click **Edge Agent** tab in the bottom left,  
4. click **Connect** button,  
5. click **Start** button.  
1. In the app, go to Wallet Demo, and on the Mediator tab, insert the mediator DID.  
1. Go back to the Sample app. In the main screen, you can provide the mediator DID of your choice or use what is there already. Proceed and click **Start** after.  
2. If you are running the SampleApp, click the **Start Agent** button.

The below code examples show how to establish mediation when building your own application.

Code examples 3\. The following code examples represent establishing mediation and instantiating the Cloud agent. 

```ts
  const mediatorDID = SDK.Domain.DID.fromString(
    [[MEDIATOR DID PEER]]
  );
  const api = new SDK.ApiImpl();
  const apollo = new SDK.Apollo();
  const castor = new SDK.Castor(apollo);
  const didcomm = new SDK.DIDCommWrapper(apollo, castor, pluto);
  const mercury = new SDK.Mercury(castor, didcomm, api);
  const store = new SDK.PublicMediatorStore(pluto);
  const handler = new SDK.BasicMediatorHandler(mediatorDID, mercury, store);
  const manager = new SDK.ConnectionsManager(castor, mercury, pluto, handler);
  const seed = apollo.createRandomSeed()
  const agent = new SDK.Agent(
    apollo,
    castor,
    pluto,
    mercury,
    handler,
    manager,
    seed.seed
  );
   /**
   * This internally will attempt to load an existing mediator from the
   * database. If it does not exist it will try to achieve mediation
   * automatically, by creating a PeerDID and sending a MediationRequest.
   * After this step the mediator starts capturing messages for the PeerDID we specied.
   */
  await agent.start()
```

```
  let agent = CloudAgent(mediatorDID: did)
  try await agent.start()
  agent.startFetchingMessages()
```

```kotlin
val apollo = ApolloImpl()
val castor = CastorImpl(apollo)
val pluto = Pluto(DbConnection())
(pluto as PlutoImpl).start(context)
val mercury = mercury = MercuryImpl(
    castor,
    DIDCommWrapper(castor, pluto, apollo),
    ApiImpl(httpClient())
)
val pollux = PolluxImpl(castor)
val seed = apollo.createRandomSeed()
val handler = BasicMediatorHandler(
  mediatorDID = DID(<DID_STRING>),
  mercury = mercury,
  store = BasicMediatorHandler.PlutoMediatorRepositoryImpl(pluto)
)
agent = CloudAgent(
    apollo = apollo,
    castor = castor,
    pluto = pluto,
    mercury = mercury,
    pollux = pollux,
    seed = seed,
    mediatorHandler = handler
)
agent.start()
agent.startFetchingMessages()

```

## Establish holder connections

To connect the Holder to both Cloud agent instances, you must run this in both Issuer and Verifier endpoints.

### Establish a connection on the agent side

A connection must be established between the Holder and Cloud agents to correctly deliver the Issuance \+ Verification Messages to the Holder.

**Establish connection on the Issuer Cloud agent**

```shell
curl --location \
--request POST 'http://localhost:8000/cloud-agent/connections' \
--header 'Content-Type: application/json' \
--data-raw '{
    "label": "Cloud agent demo connection with holder"
}'
```

1. This request will return a JSON response with an invitation and its URL. The Issuer Cloud agent would share this URL as a QR code, and the holder would scan it with the wallet app.  
* Copy the `invitationUrl` and the `connectionId`.

**Establish connection on the Verifier Cloud agent**

```shell
curl --location \
--request POST 'http://localhost:9000/cloud-agent/connections' \
--header 'Content-Type: application/json' \
--data-raw '{
    "label": "Cloud agent demo connection with holder"
}'
```

2. This request will return a JSON response with an invitation and its URL. The Verifier Cloud agent would share this URL as a QR code, and the holder would scan it with the wallet app.  
* Copy the `invitationUrl` and the `connectionId`.

### Establish a connection on the holder side

3. Now that you have the invitation, it's time for the Holder to accept it.

**Demo application** 

4. Open a browser at localhost:3000.  
5. Start the Edge Agent by clicking the button.  
6. Paste the invitation URL generated in the previous step into the `CloudAgent` connection section and click on Create Connection.  
* The application will react when the connection gets established correctly and show a new connection.  
4. On the Out of Bounds (OOB) dialog, paste the invitation URL we generated into the `CloudAgent` connection section and click **Validate**.  
* The application will respond once the connection gets established correctly and show a message under messages.  
4. Go back to the Application:  
5. Click the floating button at the bottom right corner of the Contacts tab.  
6. On the dialog, paste the invitation URL we generated into the `CloudAgent` connection section and click **Validate**.  
* The application will react once the connection gets established correctly and show a message under messages.

Code examples 

```javascript
const parsed = await props.agent.parseOOBInvitation(new URL([[OOB URL]]));
await props.agent.acceptDIDCommInvitation(parsed);
```

```
  let message = try agent.parseOOBInvitation(url: oobUrl)
  try await agent.acceptDIDCommInvitation(invitation: message)
```

```kotlin
val invitation = agent.parseInvitation(oobUrl)
agent.acceptOutOfBandInvitation(invitation)
```

## Issue a Credential from the Issuer to the holder

The credential issuance flow consists of multiple steps, detailed in this section. It starts with the Issuer sending a [Credential Offer](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#credential-offer) to the Holder, which would accept or reject this invitation and create a `credentialRequest` from it. The [credential request](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#credential-request) gets sent through DIDComm to the Issuer, issuing and sending the credential back to the Holder.

The Issuer can create a credential offer in two ways:

1. As a direct credential offer DIDComm message for a holder with an existing connection  
2. As an credential offer as attachment in an OOB invitation message for connectionless issuance  
1. \#\#\# Create a credential offer with an existing connection \*\*Issuer Agent\*\* To trigger the creation of a credential-offer, we call the credential-offers-endpoint, as follows:

:::info

Please replace the following variables in the example request before sending:

- `connectionId`: The ID of the connection previously established between agent and holder. This is part of the response of the POST message from the agent when calling the `cloud-agent/connections` endpoint. It is returned in the `connectionId` attribute. There is a unique connection ID for the relationship between issuer and holder and verifier and holder. In this example, please use the `connectionId` returned when creating the connection between issuer and holder  
- `publishedPrismDID`: The short form of the PRISM DID created when setting up the Issuer agent

The `connectionId` is just the ID of the connection we previously established with the issuer.

The Issuing DID is the published PRISM DID in its short version which was also used to create and publish the credential schema.

- \`\`

:::

```shell
curl --location --request POST 'http://localhost:8000/cloud-agent/issue-credentials/credential-offers' \
--header 'Content-Type: application/json' \
--data-raw '{
    "claims": {
      "emailAddress":"sampleEmail",
      "familyName":"Alice",
      "dateOfIssuance":"2023-01-01T02:02:02Z",
      "drivingLicenseID":"42",
      "drivingClass":1
    },
    "connectionId": [[connectionId]],
    "issuingDID": [[publishedPrismDID]],
    "schemaId": [[schemaId]],
    "automaticIssuance": true
}'
```

1. \#\#\# Create a credential offer as Invitation for connectionless issuance \*\*Issuer Agent\*\* To trigger the creation of a credential-offer, we call the credential-offers-invitation-endpoint, as follows:

:::info

Please replace the following variables in the example request before sending:

- `goalCode`: OPTIONAL A self-attested code the receiver may want to display to the user or use in automatically deciding what to do with the out-of-band message,  
- `goal`: OPTIONAL. A self-attested string that the receiver may want to display to the user about the context-specific goal of the out-of-band message.  
- `publishedPrismDID`: The short form of the PRISM DID created when setting up the Issuer agent

The Issuing DID is the published PRISM DID in its short version, which was also used to create and publish the credential schema.

:::

```shell
curl --location --request POST 'http://localhost:8000/cloud-agent/issue-credentials/credential-offers/invitation' \
--header 'Content-Type: application/json' \
--data-raw '{
    "claims": {
      "emailAddress":"sampleEmail",
      "familyName":"Alice",
      "dateOfIssuance":"2023-01-01T02:02:02Z",
      "drivingLicenseID":"42",
      "drivingClass":1
    },
    "goalCode": [[goalCode]],
    "goal": [[goal]],
    "credentialFormat": "JWT",
    "issuingDID": [[publishedPrismDID]],
    "schemaId": [[schemaId]],
    "automaticIssuance": true
}'
```

### Accept credential offer invitation for connectionless issuance **Holder**

For connectionless issuance, the Holder needs to accept the invitation containing the credential offer. This step is necessary before creating the Credential Request. **Demo application** 

1. In the browser at localhost:3000, navigate to the "Credential Offer" section.  
2. Paste the invitation URL received from the Issuer into the provided input field.  
3. Click on "Accept Invitation" to process the credential offer.  
1. In the Swift mobile app, go to the "Credential Offer" section.  
2. Enter the invitation URL received from the Issuer.  
3. Tap on "Accept Invitation" to process the credential offer.  
1. In the Android mobile app, navigate to the "Credential Offer" section.  
2. Input the invitation URL provided by the Issuer.  
3. Tap "Accept Invitation" to process the credential offer.

Code examples 

```javascript
const parsed = await props.agent.parseOOBInvitation(new URL([[OOB URL]]));
await props.agent.acceptDIDCommInvitation(parsed);
```

```
  let message = try agent.parseOOBInvitation(url: oobUrl)
  try await agent.acceptDIDCommInvitation(invitation: message)
```

```kotlin
val invitation = agent.parseInvitation(oobUrl)
agent.acceptOutOfBandInvitation(invitation)
```

2. \#\#\# Create CredentialRequest from CredentialOffer \*\*Holder\*\* Because this credential Offer was created with the `automaticIssuance` true, as soon as the `CloudAgent` receives this `credentialRequest` it will respond with the `IssuedCredential` message and send this back to the holder.

:::info

automaticIssuance is optional. It can also be manually triggered and confirmed by the Holder.\`\`\`

:::

**Demo application** 3\. The holder will at some point receive a `CredentialOffer`, which the holder must accept, and then, a `CredentialRequest` is created and sent back to the Issuer through DIDComm V2 protocols.

5. 4\. The `CredentialOffer` message will be automatically accepted as soon as it reaches the browser. In exchange, a `CredentialRequest` message will get sent back to the \`CloudAgent.\` 4\. As soon as the `CredentialOffer` message reaches the Swift mobile app, it will display to the user to accept or reject, and in exchange, a `CredentialRequest` message will get sent back to the `CloudAgent`. 4\. As soon as the `CredentialOffer` message reaches the Android mobile app, it will be automatically accepted, and in exchange, a `CredentialRequest` message will get sent back to the `CloudAgent`. Code examples The exchange between CredentialOffer and CredentialRequest is demonstrated through more advanced code samples below, showcasing how different platforms handle it.

```ts
props.agent.addListener(ListenerKey.MESSAGE,async (newMessages:SDK.Domain.Message[]) => {
    //newMessages can contain any didcomm message that is received, including
    //Credential Offers, Issued credentials and Request Presentation Messages
    const credentialOffers = newMessages.filter((message) => message.piuri === "https://didcomm.org/issue-credential/2.0/offer-credential");

    if (credentialOffers.length) {
        for(const credentialOfferMessage of credentialOffers) {
        const credentialOffer = OfferCredential.fromMessage(credentialOfferMessage);
        const requestCredential = await props.agent.prepareRequestCredentialWithIssuer(credentialOffer);
        try {
            await props.agent.sendMessage(requestCredential.makeMessage())
        } catch (err) {
            console.log("continue after err", err)
        }
        }
    }
})
```

```
agent
  .handleMessagesEvents()
  .sink(receiveCompletion: { _ in }, receiveValue: { [weak self] in
    guard
        let message,
        message.direction == .received,
        let msgType = ProtocolTypes(rawValue: message.piuri)
      else { return }

      Task.detached { [weak self] in
          do {
            switch msgType {
             case .didcommOfferCredential:
                let newPrismDID = try await agent.createNewPrismDID()
                guard let requestCredential = try await agent.prepareRequestCredentialWithIssuer(
                    did: newPrismDID,
                    offer: try OfferCredential(fromMessage: message)
                ) else { throw UnknownError.somethingWentWrongError() }

                _ = try await agent.sendMessage(message: try requestCredential.makeMessage())
          } catch {}
      }
  })

```

```kotlin
agent.handleReceivedMessagesEvents().collect { list ->
    list.forEach { message ->
        if (message.piuri == ProtocolType.DidcommOfferCredential.value) {
            val credentials = pluto.getAllCredentials().first()
            if (credentials.isEmpty()) {
                val offer = OfferCredential.fromMessage(message)
                val subjectDID = agent.createNewPrismDID()
                val request =
                agent.prepareRequestCredentialWithIssuer(
                    subjectDID,
                    offer
                )
                mercury.sendMessage(request.makeMessage())
            }
        }
    }
}
```

### Store the issued credential \[Holder\]

:::caution

The sample application are using an insecure storage solution which should only be used for testing purposes and not production environments\!

:::

Code examples 6\. Once the Holder receives a credential from the Cloud agent, it needs to store the credential somewhere: 

```ts
props.agent.addListener(ListenerKey.MESSAGE,async (newMessages:SDK.Domain.Message[]) => {
    //newMessages can contain any didcomm message that is received, including
    //Credential Offers, Issued credentials and Request Presentation Messages
    const issuedCredentials = newMessages.filter((message) => message.piuri === "https://didcomm.org/issue-credential/2.0/issue-credential");
    if (issuedCredentials.length) {
        for(const issuedCredential of issuedCredentials) {
            const issueCredential = IssueCredential.fromMessage(issuedCredential);
            await props.agent.processIssuedCredentialMessage(issueCredential);
        }
    }
})
```

```
agent
  .handleMessagesEvents()
  .sink(receiveCompletion: { _ in }, receiveValue: { [weak self] in
    guard
        let message = $0
        message.direction == .received,
        let msgType = ProtocolTypes(rawValue: message.piuri)
      else { return }

      Task.detached { [weak self] in
          do {
            switch msgType {
              case .didcommIssueCredential:
                  let issueCredential = try IssueCredential(fromMessage: message)
                  _ = try await agent.processIssuedCredentialMessage(message: issueCredential)
            }
          } catch {}
      }
  })
```

```kotlin
agent.handleReceivedMessagesEvents().collect { list ->
    list.forEach { message ->
        if (message.piuri == ProtocolType.DidcommIssueCredential.value) {
                agent.processIssuedCredentialMessage(
                    IssueCredential.fromMessage(
                    message
                )
            )
        }
    }
}
```

## Request a verification from the Verifier Cloud agent to the Holder (JWT W3C Credential)

Now that the Holder has received a credential, it can be used in a verification workflow between a Holder and a Verifier. This requires the following steps:

1. Verifier creates a proof request  
2. Holder receives the proof request  
3. Holder creates a proof presentation and shares this with the verifier  
4. Verifier verifies the proof presentation

:::info

In the example, we demonstrate two verification flows:

1. Verification with an established connection between the Holder and the Verifier.  
2. Connectionless verification in which the Holder and Verifier do not have a pre-established connection. :::

### Verifier agent

5. To run this section, we will use [the connection](https://hyperledger-identus.github.io/docs/home/quick-start#establish-connection-on-the-verifier-cloud-agent) we created between the Holder and the Verifier.

```shell
curl --location \
--request POST 'http://localhost:9000/cloud-agent/present-proof/presentations' \
--header 'Content-Type: application/json' \
--data-raw '{
    "connectionId": [[connectionId]],
    "proofs": [
        {
            "schemaId": [[schemaId]],
            "trustIssuers": [
                [[PUBLISHED PRISM DID FROM THE ISSUER]]
            ]
        }
    ],
    "options": {
        "challenge": "A challenge for the holder to sign",
        "domain": "domain.com"
    }
}'
```

* This API request will return a `presentationRequestId,` which the verifier can use later to check the current status  of the request.  
5. To run this section, we'll use the presentation invitation endpoint to create a request presentation invitation, which the holder can scan to receive the invitation or the verifier can share directly.

```shell
curl --location \
--request POST 'http://localhost:9000/cloud-agent/present-proof/presentations/invitation' \
--header 'Content-Type: application/json' \
--data-raw '{
    "goalCode": [[goalCode]],
    "goal": [[goal]],
    "credentialFormat": "JWT",
    "proofs": [
        {
            "schemaId": [[schemaId]],
            "trustIssuers": [
                [[PUBLISHED PRISM DID FROM THE ISSUER]]
            ]
        }
    ],
    "options": {
        "challenge": "A challenge for the holder to sign",
        "domain": "domain.com"
    }
}'
```

* This API request will return an `invitationId` along with an Out-Of-Band (OOB) message. The OOB message includes a Request Presentation in JSON format as an attachment and is encoded as a base64 URL-encoded string, which can be shared with the holder.

### Accept request presentation invitation for connectionless verification **Holder**

For connectionless verification, the Holder needs to accept the invitation containing the Request Presentation. **Demo application** 

1. In the browser at localhost:3000, navigate to the "Request Presentation" section.  
2. Paste the invitation URL received from the Issuer into the provided input field.  
3. Click on "Accept Invitation" to process the request presentation.  
1. In the Swift mobile app, go to the "Request Presentation" section.  
2. Enter the invitation URL received from the Issuer.  
3. Tap on "Accept Invitation" to process the request presentation.  
1. In the Android mobile app, navigate to the "Request Presentation" section.  
2. Input the invitation URL provided by the Issuer.  
3. Tap "Accept Invitation" to process the request presentation.

Code examples 

```javascript
const parsed = await props.agent.parseOOBInvitation(new URL([[OOB URL]]));
await props.agent.acceptDIDCommInvitation(parsed);
```

```
  let message = try agent.parseOOBInvitation(url: oobUrl)
  try await agent.acceptDIDCommInvitation(invitation: message)
```

```kotlin
val invitation = agent.parseInvitation(oobUrl)
agent.acceptOutOfBandInvitation(invitation)
```

### Holder: Receives the Presentation proof request

6. The Holder needs an Edge Agent running with the message listener active. It will receive the presentation proof request from the Verifier Cloud agent for the correct type of messages as detailed below:

Code examples 

```ts
props.agent.addListener(ListenerKey.MESSAGE,async (newMessages:SDK.Domain.Message[]) => {
    //newMessages can contain any didcomm message that is received, including
    //Credential Offers, Issued credentials and Request Presentation Messages
    const requestPresentations = newMessages.filter((message) => message.piuri === "https://didcomm.atalaprism.io/present-proof/3.0/request-presentation");

    if (requestPresentations.length) {
        for(const requestPresentation of requestPresentations) {
            const lastCredentials = await props.pluto.getAllCredentials();
            const lastCredential = lastCredentials.at(-1);
            const requestPresentationMessage = RequestPresentation.fromMessage(requestPresentation);
            try {
            if (lastCredential === undefined) throw new Error("last credential not found");

            const presentation = await props.agent.createPresentationForRequestProof(requestPresentationMessage, lastCredential)
            await props.agent.sendMessage(presentation.makeMessage())
            } catch (err) {
            console.log("continue after err", err)
            }
        }
    }
})
```

```
agent
  .handleMessagesEvents()
  .sink(receiveCompletion: { _ in }, receiveValue: { [weak self] in
    guard
          let message,
          message.direction == .received,
          let msgType = ProtocolTypes(rawValue: message.piuri)
      else { return }

      Task.detached { [weak self] in
        do {
          switch msgType {
          case .didcommRequestPresentation:
              let credential = try await agent.verifiableCredentials().map { $0.first }.first().await()
              guard let credential else {
                  throw UnknownError.somethingWentWrongError()
              }
              let presentation = try await agent.createPresentationForRequestProof(
                  request: try RequestPresentation(fromMessage: message),
                  credential: credential
              )
              _ = try await agent.sendMessage(message: try presentation.makeMessage())
          }
        } catch {}
      }
  })
```

```kotlin
agent.handleReceivedMessagesEvents().collect { list ->
    list.forEach { message ->
        if (message.piuri == ProtocolType.DidcommRequestPresentation.value && !presentationDone) {
            viewModelScope.launch {
                presentationDone = true
                agent.getAllCredentials().collect {
                    val credential = it.first()
                    val presentation = agent.preparePresentationForRequestProof(
                        RequestPresentation.fromMessage(message),
                        credential
                    )
                    mercury.sendMessage(presentation.makeMessage())
                }
            }
        }
    }
}
```

### Verifier: Will then check on the API if the Presentation Request has been completed or not.

```shell
curl --location \
--request GET 'http://localhost:9000/cloud-agent/present-proof/presentations/[[presentationRequestId]]' \
--header 'Accept: application/json'
```

7. The response body establishes the completion of the request and can be verified for correctness.

# Connections {#connections}

## Overview

Connections are the foundation of secure communication in Hyperledger Identus. Before credentials can be issued or proofs can be verified, agents must establish a secure, authenticated connection using the DIDComm protocol. This section explains how to create connection invitations, accept them, and manage the connection lifecycle between issuers, holders, and verifiers.

A connection is a stateful relationship between two parties that enables secure communication.

The [Connection protocol](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#connection-protocol) is required to establish secure connections between agents, allowing them to exchange information and interact.

## Roles

The connection protocol has two roles:

1. [Inviter](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#inviter): A subject that initiates a connection request by sending a [connection invitation](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#connection-invitation).  
2. [Invitee](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#invitee): A subject that receives a connection invitation and accepts it by sending a [connection request](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#connection-request).

## Prerequisites

1. Inviter and Invitee Cloud agents up and running

## Cloud agent endpoints

The protocol uses the following REST API endpoints:

1. [`/connections`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Connections-Management):  
   - [`POST`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Connections-Management/operation/createConnection): Creates a new connection and returns an invitation  
   - [`GET`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Connections-Management/operation/getConnections): Returns a list of connections  
2. [`GET /connections/{connectionId}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Connections-Management/operation/getConnection): Returns an existing connection record by id  
3. [`POST /connection-invitations`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Connections-Management/operation/acceptConnectionInvitation): Accepts an externally received invitation

:::info Please check the full [Cloud agent API](https://hyperledger-identus.github.io/docs/agent-api) specification for more detailed information. :::

## Inviter flow

1. Generate and share a new Out-of-Band (OOB) invitation (connection gets created in `InvitationGenerated` state)  
2. Receive a connection request from the Invitee (connection moves to `ConnectionRequestReceived` state)  
3. Accept the connection request (connection moves to `ConnectionResponsePending` state)  
4. Send the connection response via the DIDComm Agent (connection achieves `ConnectionResponseSent` state)

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

1. Receive the OOB invitation (`InvitationReceived` state)  
2. Accept the invitation (connection is created in `ConnectionRequestPending` state)  
3. Send the connection request via [DIDComm](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#didcomm) (connection achieves `ConnectionRequestSent` state)  
4. Receive the connection response (connection achieves `ConnectionResponseReceived` state)

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

# DIDs {#dids}

## Overview

Decentralized identifiers (DIDs) are the cornerstone of self-sovereign identity. In Hyperledger Identus, PRISM DIDs are created offline, optionally published to the Cardano blockchain, and can be updated or deactivated throughout their lifecycle. This section covers the complete DID management process, from creation through deactivation.

Understanding DID lifecycle management is essential for issuing credentials (DIDs are used as issuer identifiers) and for holders who need DIDs to receive and present credentials.

---

## Creating a DID

PRISM DIDs are a type of [decentralized identifier (DID)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#decentralized-identifier) used across the Atala PRISM product suite.

It is a variation of a [sidetree protocol](https://identity.foundation/sidetree/spec/) and uses the Cardano blockchain as the underlying ledger for [DID resolution](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-resolution) and operation publication. Please refer to the [PRISM method specification](https://github.com/input-output-hk/prism-did-method-spec/blob/main/w3c-spec/PRISM-method.md) for a more detailed explanation of how it works.

PRISM DIDs can be created entirely offline without interacting with the blockchain by constructing a DID create-operation, a [protocol buffer (protobuf)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#protocol-buffer) message with a set of public keys and services. Once the create-operation gets constructed, deriving a DID from this operation is possible, which is well-defined by the [PRISM DID method](https://github.com/input-output-hk/prism-did-method-spec/blob/main/w3c-spec/PRISM-method.md).

## Roles

1. [DID Controller](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-controller) is the organization or individual who has control of the DID.

## Prerequisites

1. DID Controller Cloud agent up and running

## Overview

For this example, a PRISM DID gets created and stored inside the Cloud agent along with the private keys. It is not automatically published. The Agent will keep track of private keys used for the create-operation and the content of the operation itself.

The Cloud agent provides two endpoint groups to facilitate PRISM DID usage.

- `/dids/*` facilitate of low-level interactions between DID operations and the blockchain. The DID controllers will handle key management independently and use these endpoints for blockchain interaction.  
    
- `/did-registrar/*` Facilitates a higher-level interaction with PRISM DID, where the Cloud agent handles key management concerns.

## Endpoints

The example uses the following endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| [`GET /did-registrar/dids`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID-Registrar/operation/listManagedDid) | List all DIDs stored in the Cloud agent | DID Controller |
| [`POST /did-registrar/dids`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID-Registrar/operation/createManagedDid) | Create a new PRISM DID to be managed by the Cloud agent | DID Controller |
| [`GET /dids/{didRef}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID/operation/getDid) | Resolve a DID to DID document representation | DID Controller |

## DID controller interactions

### Check existing DID on the Cloud agent

```shell
curl --location --request GET 'http://localhost:8080/cloud-agent/did-registrar/dids' \
  --header "apikey: $API_KEY" \
  --header 'Accept: application/json'
```

The result should show an empty list, as no DIDs exist on this Cloud agent instance.

### Create the Cloud agent managed DID using DID registrar endpoint

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

### List the created DID

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

### Resolution of the created DID

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

## Roles

1. DID Controller is the organization or individual who has control of the DID.

## Prerequisites

1. DID Controller Cloud agent up and running  
2. DID Controller has a DID created by the Cloud agent (see [Create DID](https://hyperledger-identus.github.io/docs/tutorials/create.md))

## Overview

Publishing a DID requires the DID create-operation and the DID `MASTER` key pairs, which the Cloud agent already created under the hood. When the DID Controller requests a publication of their DID, the Cloud agent uses the DID `MASTER` key to sign the operation and submit the signed operation to the blockchain. After the operation submission to the blockchain, a specific number of confirmation blocks must get created before the DID operation is processed and published. (see [PRISM DID method \- Processing of DID operation](https://github.com/input-output-hk/prism-did-method-spec/blob/main/w3c-spec/PRISM-method.md#processing-of-operations))

## Endpoints

The example uses the following endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| [`GET /did-registrar/dids/{didRef}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID-Registrar/operation/getManagedDid) | Get the DID stored in the Cloud agent | DID Controller |
| [`POST /did-registrar/dids/{didRef}/publications`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID-Registrar/operation/publishManagedDid) | Publish the DID stored in the Cloud agent to the blockchain | DID Controller |
| [`GET /dids/{didRef}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID/operation/getDid) | Resolve a DID to DID document representation | DID Controller |

## DID controller interactions

### Check the status of an unpublished DID

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

### Request a publication of a DID

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

### Wait until the DID operation is confirmed

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

### Resolve a short-form DID

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

## Roles

1. DID Controller is the organization or individual who has control of the DID.

## Prerequisites

1. DID Controller Cloud agent is up and running  
2. DID Controller has a DID created by the Cloud agent (see [Create DID](https://hyperledger-identus.github.io/docs/tutorials/create.md))  
3. DID Controller has a DID published to the blockchain (see [Publish DID](https://hyperledger-identus.github.io/docs/tutorials/publish.md))

## Overview

The Cloud agent allows the DID Controller to update the DID easily. This update mechanism is implementation specific and links the DID update-operation from the last confirmed operation observed on the blockchain.

When updating a DID, there is a waiting period for the update to be confirmed on the blockchain. If the DID Controller updates the DID before the previous update is confirmed, it can create a situation where the sequence of updates splits into two separate paths, which is not allowed according to the protocol. This happens because the Cloud agent connects the update operation to the latest confirmed update. Once the pending update operation is confirmed, any other pending operation that does not link to the latest confirmed operation will be discarded. The subsequent updates continuing from that operation will also be discarded. However, the Cloud agent has a safeguard in place to prevent this issue by rejecting multiple updates submitted on the same DID while previous updates are still being processed.

Please refer to the `SECURE_DEPTH` parameter in [PRISM method \- protocol parameters](https://github.com/input-output-hk/prism-did-method-spec/blob/main/w3c-spec/PRISM-method.md#versioning-and-protocol-parameters) for the number of confirmation blocks. At the time of writing, this number is 112 blocks.

This example shows the DID update capability on the Cloud agent and the steps to verify that the update has been confirmed and applied.

## Endpoints

The example uses the following endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| [`POST /did-registrar/dids/{didRef}/updates`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID-Registrar/operation/updateManagedDid) | Update a PRISM DID | DID Controller |
| [`GET /dids/{didRef}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID/operation/getDid) | Resolve a DID to DID document representation | DID Controller |

## DID controller interactions

### Check the current state of the DID document

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

### Add a new key and remove the existing key

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

### Wait for the confirmation and observe the change on the DID document

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

## Future work

The example only shows the case of a successful update. In case of failure, the Cloud agent will provide the capability to retrieve the low-level operation status and failure detail in the future.

---

## Deactivating a DID

DID deactivation is an important feature that provides greater control for managing digital identity. DID deactivation can be helpful if the [DID](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#decentralized-identifier) is compromised or unused. This feature is crucial for the security and risk management of identity owners.

Similar to [DID update](https://hyperledger-identus.github.io/docs/tutorials/update.md), deactivating a PRISM DID is a process of putting deactivate-operation on the blockchain so that other participants know that the DID is no longer active. The PRISM DID method only allows published DID to be deactivated.

## Roles

1. DID Controller is the organization or individual who has control of the DID.

## Prerequisites

1. DID Controller Cloud agent up and running  
2. DID Controller has a DID created on the Cloud agent (see [Create DID](https://hyperledger-identus.github.io/docs/tutorials/create.md))  
3. DID Controller has a DID published to the blockchain (see [Publish DID](https://hyperledger-identus.github.io/docs/tutorials/publish.md))

## Overview

DID deactivation operates similarly to the DID update, where deactivate-operation publishes to the blockchain, and confirmation blocks must be created before participants consider it deactivated. Once the DID is deactivated, all content in the [DID document](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-document) gets deleted, and no operation will affect the DID afterward. The same concept also holds for PRISM DID deactivation in that the fork can occur if any subsequent operation occurs before the operation is confirmed. Please refer to the `SECURE_DEPTH` parameter in [PRISM method \- protocol parameters](https://github.com/input-output-hk/prism-did-method-spec/blob/main/w3c-spec/PRISM-method.md#versioning-and-protocol-parameters) for the number of confirmation blocks. At the time of writing, this number is 112 blocks.

DID deactivation is easily performed with the Cloud agent. Under the hood, the Cloud agent uses the `MASTER` keys to sign the intended operation and automatically post the operation to the blockchain. This example shows the DID deactivation and steps to observe the changes to the DID using the Cloud agent.

## Endpoints

The example uses the following endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| [`POST /did-registrar/dids/{didRef}/deactivations`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID-Registrar/operation/deactivateManagedDid) | Deactivate a PRISM DID | DID Controller |
| [`GET /dids/{didRef}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/DID/operation/getDid) | Resolve a DID to DID document representation | DID Controller |

## DID controller interactions

### Check the current state of the DID document

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

### Requesting the DID deactivation to PRSIM Agent

The active status comes from the last step. The DID deactivation can be performed by calling `POST /did-registrar/dids/{didRef}/deactivations` and replacing `{didRef}` with the DID to deactivate.

```shell
curl --location --request POST 'http://localhost:8080/cloud-agent/did-registrar/dids/{didRef}/deactivations' \
--header "apikey: $API_KEY" \
--header 'Accept: application/json'
```

Under the hood, the Cloud agent constructs the DID deactivate-operation from the last confirmed operation observed on the blockchain. The DID Controller should receive a response about the scheduled operation, waiting for confirmation on the blockchain. If this deactivate-operation gets confirmed on the blockchain and not discarded as a fork, the DID becomes deactivated.

### Wait for the confirmation and observe the change in the DID metadata

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

# Schemas {#schemas}

## Overview

Credential schemas define the structure and attributes of verifiable credentials. They specify which claims a credential contains, their data types, and validation rules. Schemas ensure semantic interoperability – when a verifier receives a credential, the schema helps them understand what the credential claims mean.

Hyperledger Identus supports W3C Verifiable Credentials JSON Schema 2022 and AnonCreds schemas. This section explains schema concepts, attributes, and operations for creating, updating, and managing schemas.

---

## Credential schema introduction

## Abstract

This document describes the purpose, supported formats, and technical details of the Credential Schema implementation in the Identus Platform.

## Introduction

[Credential Schema](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#credential-schema) is a data template for [Verifiable Credentials](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifiable-credential). It contains [claims](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#claims) (attributes) of the Verifiable Credentials, credential schema author, type, name, version, and proof of authorship. By putting schema definitions on a public blockchain, they are available for all verifiers to examine to determine the semantic interoperability of the Credential.

The Identus Platform supports the following specifications of the credential schemas:

- [Verifiable Credentials JSON Schema 2022](https://www.w3.org/TR/vc-json-schema/)  
- [AnonCreds Schema](https://hyperledger.github.io/anoncreds-spec/#term:schemas)

The signed credential schema allows doing following verifications:

- semantic verification of the verifiable credentials  
- authorship verification

The author can use credential schema to issue the following types of verifiable credentials:

- JSON Verifiable Credential  
- JSON-LD Verifiable Credential  
- Anoncred Verifiable Credential  
- all types above but encoded as JWT

Limitations and constraints of the Identus Platform:

- The Issuer does not sign the Credential Schema  
- The Issuer does not publish the Credential Schema to the VDR (the Cardano blockchain)

## Terminology

### Credential schema

The Credential Schema is a template that defines a set of attributes the [Issuer](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#issuer) uses to issue the Verifiable Credential.

### Schema registry

The registry is where the Credential Schema is published and available for parties.

### Issuer, Holder, Verifier

These are well-known roles in the [SSI](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#self-sovereign-identity) domain.

## Credential schema attributes

### guid (UUID)

It is the globally unique identifier of the credential schema. It is bound to the `author`, `version`, and `id` fields as it is composed of the bytes of the `longId` string.

### id (UUID)

The locally unique identifier of the schema.

### longId (String)

Resource identifier of the given credential schema composed from the author's DID reference, id, and version fields. **Example:** `{author}/{id}?version={version}`

**Note:** According to the [W3C specification](https://w3c-ccg.github.io/vc-json-schemas/#id), this field is locally unique and combines the Issuer `DID`, `uuid`, and `version`.

**For example:** `did:example:MDP8AsFhHzhwUvGNuYkX7T/06e126d1-fa44-4882-a243-1e326fbe21db?version=1.0`

---

### type (String)

It is a type of the supported JSON Schema of the credential schema. It describes the JSON Schema of the Credential Schema described in this document.

**JWT Credential Schema Example:**

```json
{
  "type": "https://w3c-ccg.github.io/vc-json-schemas/schema/2.0/schema.json"
}
```

**AnonCred Credential Schema Example:**

```json
{
  "type" : "AnoncredSchemaV1"
}
```

---

### name (String)

It is a human-readable name for the schema. **Example:**

```json
{
  "name": [
    "DrivingLicence"
  ]
}
```

---

### description (String)

It is a human-readable description of the schema.

**Note:** This field may get removed later as it's not a part of W3C specification but rather the internal field of the JSON schema.

---

### version (String)

It is a version of the schema that contains the revision of the credential schema in [SemVer](https://semver.org/) format. **Example:**

```json
{
  "version": "1.0.0"
}
```

The version field must be the schema evolution and describe the impact of the changes. For the breaking changes, the `major` version must get increased. In the current implementation, the Identus Platform doesn't validate whether the new version is backward compatible. This logic may get implemented later, so the Issuer is responsible for correctly setting the credential schema's next version.

---

### author (DID)

DID of the identity which authored the credential schema. **Example:**

```json
{
  "author": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff"
}
```

---

### authored (DateTime)

[RFC3339](https://www.rfc-editor.org/rfc/rfc3339) date of when the credential schema creation. A piece of Metadata. **Example:**

```json
{
  "authored": "2022-03-10T12:00:00Z"
}
```

---

### schema (JSON Schema)

A valid [JSON-SCHEMA](https://json-schema.org/) where the credential schema semantic gets defined. JSON Schema must be composed according to the [Metaschema](https://json-schema.org/draft/2020-12/schema) schema. **Example:**

```json
{
  "$id": "https://example.com/driving-license-1.0",
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "description": "Driving License",
  "type": "object",
  "properties": {
    "emailAddress": {
      "type": "string",
      "format": "email"
    },
    "givenName": {
      "type": "string"
    },
    "familyName": {
      "type": "string"
    },
    "dateOfIssuance": {
      "type": "string",
      "format": "date-time"
    },
    "drivingLicenseID": {
      "type": "string"
    },
    "drivingClass": {
      "type": "integer"
    }
  },
  "required": [
    "emailAddress",
    "familyName",
    "dateOfIssuance",
    "drivingLicenseID",
    "drivingClass"
  ],
  "additionalProperties": true
}
```

---

### schema (AnonCred Schema)

A valid [ANONCRED-SCHEMA](https://hyperledger.github.io/anoncreds-spec/#term:schemas) where the credential schema semantic gets defined. **Example:**

```json
{
    "name":"anoncred-birthday-cert",
    "version":"1.0.0",
    "description":"Birthday certificate",
    "type":"AnoncredSchemaV1",
    "author":"did:prism:e0266ee8d80a00163e5f922dc2567ab9611724a00db92423301154282169dff9",
    "tags":[
       "birth",
       "certificate"
    ],
    "schema":{
       "$schema":"https://json-schema.org/draft/2020-12/schema",
       "type":"object",
       "properties":{
          "name":{
             "type":"string",
             "minLength":1
          },
          "version":{
             "type":"string",
             "minLength":1
          },
          "attrNames":{
             "type":"array",
             "items":{
                "type":"string",
                "minLength":1
             },
             "minItems":1,
             "maxItems":125,
             "uniqueItems":true
          },
          "issuerId":{
             "type":"string",
             "minLength":1
          }
       },
       "name":"Birth Certificate Schema",
       "version":"1.0",
       "attrNames":[
          "location",
          "birthday"
       ],
       "issuerId":"did:prism:e0266ee8d80a00163e5f922dc2567ab9611724a00db92423301154282169dff9"
    },
    "required":[
       "name",
       "version"
    ],
    "additionalProperties":true
 }
```

---

### tags (String\[\])

It is a set of tokens that allow one to look up and filter the credential schema records. This field is not a part of the W3C specification. Its usage by the Identus Platform for filtering the records. **Example:**

```json
{
  "tags": [
    "id",
    "driving"
  ]
}
```

### proof (object)

The proof field is a JOSE object containing the credential schema's signature, including the following fields:

- type  
- created  
- verificationMethod  
- proofPurpose  
- proofValue  
- domain  
- jws

**Example:**

```json
{
  "proof": {
    "type": "Ed25519Signature2018",
    "created": "2022-03-10T12:00:00Z",
    "verificationMethod": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff#key-1",
    "proofPurpose": "assertionMethod",
    "proofValue": "FiPfjknHikKmZ...",
    "jws": "eyJhbGciOiJFZERTQSIsImI2NCI6ZmFsc2UsImNyaXQiOlsiYjY0Il0sImt0eSI6Ik...",
    "domain": "prims.atala.com"
  }
}
```

---

## References

- [Verifiable Credentials JSON Schema 2022](https://www.w3.org/TR/vc-json-schema/)  
- [Verifiable Credential Data Integrity 1.0](https://www.w3.org/TR/vc-data-integrity/)

---

## Creating a credential schema

The Identus Platform exposes REST API for creation, fetching, and searching the [credential schema](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#credential-schema) records.

The OpenAPI specification and ReDoc documentation describe the endpoint.

In this document, you can find step-by-step instructions for creating the credential schema.

## Step-by-step guide

The following guide demonstrates how to create a driving license credential schema.

### Define the JSON Schema for the Verifiable Credential

Assume that you need a credential schema for the driving license, and the [verifiable credential](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifiable-credential) must have the following fields:

- emailAddress \- the email address of the driver  
- givenName \- the first name of the driver  
- familyName \- the family name of the driver  
- dateOfIssuance \- date of the driver's license issuance  
- drivingLicenseID \- ID of the driving license  
- drivingClass \- driving class that denotes which types of vehicles the driver is allowed to go. Also, let's assume that the driving license might have additional optional claims.

For the above fields, the JSON Schema definition must be:

```json
{
  "$id": "https://example.com/driving-license-1.0.0",
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "description": "Driving License",
  "type": "object",
  "properties": {
    "emailAddress": {
      "type": "string",
      "format": "email"
    },
    "givenName": {
      "type": "string"
    },
    "familyName": {
      "type": "string"
    },
    "dateOfIssuance": {
      "type": "string",
      "format": "date-time"
    },
    "drivingLicenseID": {
      "type": "string"
    },
    "drivingClass": {
      "type": "integer"
    }
  },
  "required": [
    "emailAddress",
    "familyName",
    "dateOfIssuance",
    "drivingLicenseID",
    "drivingClass"
  ],
  "additionalProperties": true
}
```

The fields `$id` and `$schema` must correspond values that describe

- the identity of the given JSON Schema as a **correctly formatted URL** `https://example.com/driving-license-1.0.0` and  
- the meta schema fixed to `https://json-schema.org/draft/2020-12/schema` which is the only supported value

All the claims are listed under the `properties` object with corresponding `type`s and `format`s according to JSON Specification.

`additionalProperties` is set to true, meaning adding other fields to the verifiable credential is possible.

### Create the credential schema record

1. Open your preferred REST API client, such as Postman or Insomnia, or use the client stub generated based on the OpenAPI specification.  
     
2. In the client, create a new POST request to either `/cloud-agent/schema-registry/schemas` or `/cloud-agent/schema-registry/schemas/did-url` endpoints. They both take the same payload.  
     
   1. `/cloud-agent/schema-registry/schemas` creates a schema that can later be resolved via HTTP URL  
   2. `/cloud-agent/schema-registry/schemas/did-url` creates a schema that can later be resolved via [DID URL](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-url), the DID includes a service endpoint with the location of the schema registry.

Note that the value of the `author` field must match the short form of a PRISM DID that has been created using the same agent. An unpublished DID is sufficient. Please refer to the [Create DID](http://../dids/create.md) documentation page for more details on how to create a PRISM DID.

In the request body, create a JSON object:

```json
{
  "name": "driving-license",
  "version": "1.0.0",
  "description": "Driving License Schema",
  "type": "https://w3c-ccg.github.io/vc-json-schemas/schema/2.0/schema.json",
  "author": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff",
  "tags": [
    "driving",
    "license"
  ],
  "schema": {
    "$id": "https://example.com/driving-license-1.0.0",
    "$schema": "https://json-schema.org/draft/2020-12/schema",
    "description": "Driving License",
    "type": "object",
    "properties": {
      "emailAddress": {
        "type": "string",
        "format": "email"
      },
      "givenName": {
        "type": "string"
      },
      "familyName": {
        "type": "string"
      },
      "dateOfIssuance": {
        "type": "string",
        "format": "date-time"
      },
      "drivingLicenseID": {
        "type": "string"
      },
      "drivingClass": {
        "type": "integer"
      }
    },
    "required": [
      "emailAddress",
      "familyName",
      "dateOfIssuance",
      "drivingLicenseID",
      "drivingClass"
    ],
    "additionalProperties": true
  }
}
```

3. Send the POST request to create the new schema. The response should contain a GUID that uniquely identifies the new schema. Curl example is the following:

```shell
curl -X 'POST' \
  'http://localhost:8080/cloud-agent/schema-registry/schemas' \
  -H 'accept: application/json' \
  -H "apikey: $API_KEY" \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "driving-license",
  "version": "1.0.0",
  "description": "Driving License Schema",
  "type": "https://w3c-ccg.github.io/vc-json-schemas/schema/2.0/schema.json",
  "author": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff",
  "tags": [
    "driving",
    "license"
  ],
  "schema": {
    "$id": "https://example.com/driving-license-1.0.0",
    "$schema": "https://json-schema.org/draft/2020-12/schema",
    "description": "Driving License",
    "type": "object",
    "properties": {
      "emailAddress": {
        "type": "string",
        "format": "email"
      },
      "givenName": {
        "type": "string"
      },
      "familyName": {
        "type": "string"
      },
      "dateOfIssuance": {
        "type": "string",
        "format": "date-time"
      },
      "drivingLicenseID": {
        "type": "string"
      },
      "drivingClass": {
        "type": "integer"
      }
    },
    "required": [
      "emailAddress",
      "familyName",
      "dateOfIssuance",
      "drivingLicenseID",
      "drivingClass"
    ],
    "additionalProperties": true
  }
}'
```

...and response might be the following:

```json
{
  "guid": "3f86a73f-5b78-39c7-af77-0c16123fa9c2",
  "id": "f2bfbf78-8bd6-4cc6-8b39-b3a25e01e8ea",
  "longId": "did:prism:agent/f2bfbf78-8bd6-4cc6-8b39-b3a25e01e8ea?version=1.0.0",
  "name": "driving-license",
  "version": "1.0.0",
  "description": "Driving License Schema",
  "type": "https://w3c-ccg.github.io/vc-json-schemas/schema/2.0/schema.json",
  "author": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff",
  "authored": "2023-03-14T14:41:46.713943Z",
  "tags": [
    "driving",
    "license"
  ],
  "schema": {
    "$id": "https://example.com/driving-license-1.0.0",
    "$schema": "https://json-schema.org/draft/2020-12/schema",
    "description": "Driving License",
    "type": "object",
    "properties": {
      "emailAddress": {
        "type": "string",
        "format": "email"
      },
      "givenName": {
        "type": "string"
      },
      "familyName": {
        "type": "string"
      },
      "dateOfIssuance": {
        "type": "string",
        "format": "date-time"
      },
      "drivingLicenseID": {
        "type": "string"
      },
      "drivingClass": {
        "type": "integer"
      }
    },
    "required": [
      "emailAddress",
      "familyName",
      "dateOfIssuance",
      "drivingLicenseID",
      "drivingClass"
    ],
    "additionalProperties": true
  },
  "kind": "CredentialSchema",
  "self": "/schema-registry/schemas/3f86a73f-5b78-39c7-af77-0c16123fa9c2"
}
```

or in case of DID url, the response will be created schema wrapped in [Prism Envelope](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#prism-envelope)

```json
{
   "resource":"eyJhdXRob3IiOiJkaWQ6cHJpc206ZTAyNjZlZThkODBhMDAxNjNlNWY5MjJkYzI1NjdhYjk2MTE3MjRhMDBkYjkyNDIzMzAxMTU0MjgyMTY5ZGZmOSIsImF1dGhvcmVkIjoiMjAyNC0wOS0yNVQxMDozNzoxNi4wOTM2MDlaIiwiZGVzY3JpcHRpb24iOiJEcml2aW5nIExpY2Vuc2UgU2NoZW1hIiwiZ3VpZCI6IjVjOTNmYTAwLWUwM2UtMzlkZC05NDdmLTI2NWI4YzFlYWQ4YiIsImlkIjoiNjhmMGQ4MDctYTcyYi00OTY2LTg1NWItMmIzNGJjMjYzNzAyIiwibmFtZSI6ImRyaXZpbmctbGljZW5zZSIsInJlc29sdXRpb25NZXRob2QiOiJkaWQiLCJzY2hlbWEiOnsiJGlkIjoiaHR0cHM6Ly9leGFtcGxlLmNvbS9kcml2aW5nLWxpY2Vuc2UtMS4wLjAiLCIkc2NoZW1hIjoiaHR0cHM6Ly9qc29uLXNjaGVtYS5vcmcvZHJhZnQvMjAyMC0xMi9zY2hlbWEiLCJhZGRpdGlvbmFsUHJvcGVydGllcyI6dHJ1ZSwiZGVzY3JpcHRpb24iOiJEcml2aW5nIExpY2Vuc2UiLCJwcm9wZXJ0aWVzIjp7ImRhdGVPZklzc3VhbmNlIjp7ImZvcm1hdCI6ImRhdGUtdGltZSIsInR5cGUiOiJzdHJpbmcifSwiZHJpdmluZ0NsYXNzIjp7InR5cGUiOiJpbnRlZ2VyIn0sImRyaXZpbmdMaWNlbnNlSUQiOnsidHlwZSI6InN0cmluZyJ9LCJlbWFpbEFkZHJlc3MiOnsiZm9ybWF0IjoiZW1haWwiLCJ0eXBlIjoic3RyaW5nIn0sImZhbWlseU5hbWUiOnsidHlwZSI6InN0cmluZyJ9LCJnaXZlbk5hbWUiOnsidHlwZSI6InN0cmluZyJ9fSwicmVxdWlyZWQiOlsiZW1haWxBZGRyZXNzIiwiZmFtaWx5TmFtZSIsImRhdGVPZklzc3VhbmNlIiwiZHJpdmluZ0xpY2Vuc2VJRCIsImRyaXZpbmdDbGFzcyJdLCJ0eXBlIjoib2JqZWN0In0sInRhZ3MiOlsiZHJpdmluZyIsImxpY2Vuc2UiXSwidHlwZSI6Imh0dHBzOi8vdzNjLWNjZy5naXRodWIuaW8vdmMtanNvbi1zY2hlbWFzL3NjaGVtYS8yLjAvc2NoZW1hLmpzb24iLCJ2ZXJzaW9uIjoiMS4wLjAifQ==",
   "url":"did:prism:e0266ee8d80a00163e5f922dc2567ab9611724a00db92423301154282169dff9?resourceService=agent-base-url&resourcePath=schema-registry/schemas/did-url/5c93fa00-e03e-39dd-947f-265b8c1ead8b?resourceHash=d1557ede168f0f91097933aa2080edaf2f14fddd8a7362a22add97e431c4efe2"
}
```

### Retrieve the created schema

To retrieve the newly created schema, create a new GET request to the `/cloud-agent/schema-registry/schemas/{guid}` endpoint, where `{guid}` is the GUID returned in the response from the previous step. Send the GET request to retrieve the schema. Curl example is the following:

```shell
curl -X 'GET' \
  'http://localhost:8080/cloud-agent/schema-registry/schemas/3f86a73f-5b78-39c7-af77-0c16123fa9c2' \
  -H 'accept: application/json' \
  -H "apikey: $API_KEY"
```

or if you need to resolve a schema created via DID url, the endpoint will look like this `/cloud-agent/schema-registry/schemas/did-url/{guid}`

```
curl -X 'GET' \
  'http://localhost:8080/cloud-agent/schema-registry/schemas/did-url/3f86a73f-5b78-39c7-af77-0c16123fa9c2' \
  -H 'accept: application/json' \
  -H "apikey: $API_KEY"

```

The response for HTTP URL request should contain the JSON object representing the schema you just created.

```json
{
  "guid": "3f86a73f-5b78-39c7-af77-0c16123fa9c2",
  "id": "f2bfbf78-8bd6-4cc6-8b39-b3a25e01e8ea",
  "longId": "did:prism:agent/f2bfbf78-8bd6-4cc6-8b39-b3a25e01e8ea?version=1.0.0",
  "name": "driving-license",
  "version": "1.0.0",
  "description": "Driving License Schema",
  "type": "https://w3c-ccg.github.io/vc-json-schemas/schema/2.0/schema.json",
  "author": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff",
  "authored": "2023-03-14T14:41:46.713943Z",
  "tags": [
    "driving",
    "license"
  ],
  "schema": {
    "$id": "https://example.com/driving-license-1.0.0",
    "$schema": "https://json-schema.org/draft/2020-12/schema",
    "description": "Driving License",
    "type": "object",
    "properties": {
      "emailAddress": {
        "type": "string",
        "format": "email"
      },
      "givenName": {
        "type": "string"
      },
      "familyName": {
        "type": "string"
      },
      "dateOfIssuance": {
        "type": "string",
        "format": "date-time"
      },
      "drivingLicenseID": {
        "type": "string"
      },
      "drivingClass": {
        "type": "integer"
      }
    },
    "required": [
      "emailAddress",
      "familyName",
      "dateOfIssuance",
      "drivingLicenseID",
      "drivingClass"
    ],
    "additionalProperties": true
  },
  "kind": "CredentialSchema",
  "self": "/schema-registry/schemas/3f86a73f-5b78-39c7-af77-0c16123fa9c2"
}
```

and for DID URL request, response will include the same schema wrapped in [Prism envelope](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#prism-envelope) response

```json
{
   "resource":"eyJhdXRob3IiOiJkaWQ6cHJpc206ZTAyNjZlZThkODBhMDAxNjNlNWY5MjJkYzI1NjdhYjk2MTE3MjRhMDBkYjkyNDIzMzAxMTU0MjgyMTY5ZGZmOSIsImF1dGhvcmVkIjoiMjAyNC0wOS0yNVQxMDozNzoxNi4wOTM2MDlaIiwiZGVzY3JpcHRpb24iOiJEcml2aW5nIExpY2Vuc2UgU2NoZW1hIiwiZ3VpZCI6IjVjOTNmYTAwLWUwM2UtMzlkZC05NDdmLTI2NWI4YzFlYWQ4YiIsImlkIjoiNjhmMGQ4MDctYTcyYi00OTY2LTg1NWItMmIzNGJjMjYzNzAyIiwibmFtZSI6ImRyaXZpbmctbGljZW5zZSIsInJlc29sdXRpb25NZXRob2QiOiJkaWQiLCJzY2hlbWEiOnsiJGlkIjoiaHR0cHM6Ly9leGFtcGxlLmNvbS9kcml2aW5nLWxpY2Vuc2UtMS4wLjAiLCIkc2NoZW1hIjoiaHR0cHM6Ly9qc29uLXNjaGVtYS5vcmcvZHJhZnQvMjAyMC0xMi9zY2hlbWEiLCJhZGRpdGlvbmFsUHJvcGVydGllcyI6dHJ1ZSwiZGVzY3JpcHRpb24iOiJEcml2aW5nIExpY2Vuc2UiLCJwcm9wZXJ0aWVzIjp7ImRhdGVPZklzc3VhbmNlIjp7ImZvcm1hdCI6ImRhdGUtdGltZSIsInR5cGUiOiJzdHJpbmcifSwiZHJpdmluZ0NsYXNzIjp7InR5cGUiOiJpbnRlZ2VyIn0sImRyaXZpbmdMaWNlbnNlSUQiOnsidHlwZSI6InN0cmluZyJ9LCJlbWFpbEFkZHJlc3MiOnsiZm9ybWF0IjoiZW1haWwiLCJ0eXBlIjoic3RyaW5nIn0sImZhbWlseU5hbWUiOnsidHlwZSI6InN0cmluZyJ9LCJnaXZlbk5hbWUiOnsidHlwZSI6InN0cmluZyJ9fSwicmVxdWlyZWQiOlsiZW1haWxBZGRyZXNzIiwiZmFtaWx5TmFtZSIsImRhdGVPZklzc3VhbmNlIiwiZHJpdmluZ0xpY2Vuc2VJRCIsImRyaXZpbmdDbGFzcyJdLCJ0eXBlIjoib2JqZWN0In0sInRhZ3MiOlsiZHJpdmluZyIsImxpY2Vuc2UiXSwidHlwZSI6Imh0dHBzOi8vdzNjLWNjZy5naXRodWIuaW8vdmMtanNvbi1zY2hlbWFzL3NjaGVtYS8yLjAvc2NoZW1hLmpzb24iLCJ2ZXJzaW9uIjoiMS4wLjAifQ==",
   "url":"did:prism:e0266ee8d80a00163e5f922dc2567ab9611724a00db92423301154282169dff9?resourceService=agent-base-url&resourcePath=schema-registry/schemas/did-url/5c93fa00-e03e-39dd-947f-265b8c1ead8b?resourceHash=d1557ede168f0f91097933aa2080edaf2f14fddd8a7362a22add97e431c4efe2"
}
```

Schemas created for HTTP URL (`/cloud-agent/schema-registry/schemas`) will not be resolvable by endpoint that returns schemas created for DID URL (`/cloud-agent/schema-registry/schemas/did-url`) and vice verca.

The Cloud agent instance's triple `author`, `id`, and `version` are unique. So, having a single [DID](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#decentralized-identifier) reference that the author uses, creating the credential schema with the same `id` and `version` is impossible.

### Update the credential schema

To upgrade the credential schema, you need to perform the following steps:

1. Start from the first step and change the JSON Schema  
2. Change the `version` according to the nature of your change  
3. Create a new credential schema record with a higher version

---

## Updating a credential schema

The Identus Platform exposes REST API for creation, fetching, and searching the credential schema records.

The OpenAPI specification and ReDoc documentation describe the endpoint.

In this document, you can find step-by-step instructions for updating the credential schema.

After creation, updating the credential schema record is not possible. If you need to create a similar schema but with additional fields or a different description, or metadata, you need to create the credential schema record with the same `id` and a higher `version`.

## Step-by-step guide

The following guide demonstrates how to update a driving license credential schema.

### Define the updated JSON Schema for the Verifiable Credential

Assume that you need to update the credential schema from the previous tutorial. So, there is an existing driving license, and the [verifiable credential](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifiable-credential) must additionally include two fields:

- bloodType \- the blood type of the driver  
- organDonor \- indicates whether or not the person is an organ donor

The blood type on a driver's license is represented using the ABO blood group system, and potentially represented as A+, A-, B+, B-, AB+, AB-, O+, or O-. So, assume that this set of values must be enforced by the schema definition using the following regex:

```
^(A|B|AB|O)[+-]?$
```

At the same time, the organ donor must represent a binary value: `true`/`false`, `yes`/`no`, depending on the jurisdiction, and it also might be `unknown` and must be enforced by the schema definition using the `enum` keyword:

```
  enum:
    - true
    - false
    - yes
    - no
    - unknown
```

**Note**: As the original credential schema allows `additionalProperties` to be defined, we assume that two additional claims must get added to the `required` attributes.

As the change to the credential schema is backward compatible, the next version can be `1.1.0`

---

The JSON Schema changes must be defined as follows:

```json
{
   "$id": "https://example.com/driving-license-1.1.0",
   "$schema": "https://json-schema.org/draft/2020-12/schema",
   "description": "Driving License",
   "type": "object",
   "properties": {
      "emailAddress": {
         "type": "string",
         "format": "email"
      },
      "givenName": {
         "type": "string"
      },
      "familyName": {
         "type": "string"
      },
      "dateOfIssuance": {
         "type": "string",
         "format": "date-time"
      },
      "drivingLicenseID": {
         "type": "string"
      },
      "drivingClass": {
         "type": "integer"
      },
      "bloodType": {
         "type": "string",
         "pattern": "^(A|B|AB|O)[+-]?$"
      },
      "donorStatus": {
         "type": "string",
         "enum": [
            "true",
            "false",
            "yes",
            "no"
         ]
      }
   },
   "required": [
      "emailAddress",
      "familyName",
      "dateOfIssuance",
      "drivingLicenseID",
      "drivingClass",
      "bloodType",
      "donorStatus"
   ],
   "additionalProperties": true
}
```

### Update the credential schema record

1. Open your preferred REST API client, such as Postman or Insomnia, or use the client stub generated based on the OpenAPI specification.  
     
2. In the client, create a new PUT request to either `/cloud-agent/schema-registry/schemas/{id}` or `/cloud-agent/schema-registry/schemas/did-url/{id}` endpoint, where `id` is a locally unique credential schema id, formatted as a URL, they both take same payload.  
     
   1. When updating a schema, it is imporant to consider if the schema is `HTTP URL` resolvable or `DID URL` resolvable, the `id` in this endpoint needs to be used accordingly, if schema is `HTTP URL` resolvable, then only `/cloud-agent/schema-registry/schemas/{id}` can be used to update it, and same with regards to `DID URL`

Note that the value of the `author` field must match the short form of a PRISM DID that has been created using the same agent. An unpublished DID is sufficient. Please refer to the [Create DID](http://../dids/create.md) documentation page for more details on how to create a PRISM DID.

In the request body, create a JSON object:

```json
{
  "name": "driving-license",
  "version": "1.1.0",
  "description": "Driving License Schema",
  "type": "https://w3c-ccg.github.io/vc-json-schemas/schema/2.0/schema.json",
  "author": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff",
  "schema": {
     "$id": "https://example.com/driving-license-1.1.0",
     "$schema": "https://json-schema.org/draft/2020-12/schema",
     "description": "Driving License",
     "type": "object",
     "properties": {
        "emailAddress": {
           "type": "string",
           "format": "email"
        },
        "givenName": {
           "type": "string"
        },
        "familyName": {
           "type": "string"
        },
        "dateOfIssuance": {
           "type": "string",
           "format": "date-time"
        },
        "drivingLicenseID": {
           "type": "string"
        },
        "drivingClass": {
           "type": "integer"
        },
        "bloodType": {
           "type": "string",
           "pattern": "^(A|B|AB|O)[+-]?$"
        },
        "donorStatus": {
           "type": "string",
           "enum": [
              "true",
              "false",
              "yes",
              "no"
           ]
        }
     },
     "required": [
        "emailAddress",
        "familyName",
        "dateOfIssuance",
        "drivingLicenseID",
        "drivingClass",
        "bloodType",
        "donorStatus"
     ],
     "additionalProperties": true
  },
  "tags": [
    "driving",
    "license"
  ]
}
```

The curl example might be the following:

```shell
curl -X 'PUT' \
  'http://localhost:8080/cloud-agent/schema-registry/schemas/f2bfbf78-8bd6-4cc6-8b39-b3a25e01e8ea' \
  -H 'accept: application/json' \
  -H "apikey: $API_KEY" \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "driving-license",
  "version": "1.1.0",
  "description": "Driving License Schema",
  "type": "https://w3c-ccg.github.io/vc-json-schemas/schema/2.0/schema.json",
  "author": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff",
  "schema": {
     "$id": "https://example.com/driving-license-1.1.0",
     "$schema": "https://json-schema.org/draft/2020-12/schema",
     "description": "Driving License",
     "type": "object",
     "properties": {
        "emailAddress": {
           "type": "string",
           "format": "email"
        },
        "givenName": {
           "type": "string"
        },
        "familyName": {
           "type": "string"
        },
        "dateOfIssuance": {
           "type": "string",
           "format": "date-time"
        },
        "drivingLicenseID": {
           "type": "string"
        },
        "drivingClass": {
           "type": "integer"
        },
        "bloodType": {
           "type": "string",
           "pattern": "^(A|B|AB|O)[+-]?$"
        },
        "donorStatus": {
           "type": "string",
           "enum": [
              "true",
              "false",
              "yes",
              "no"
           ]
        }
     },
     "required": [
        "emailAddress",
        "familyName",
        "dateOfIssuance",
        "drivingLicenseID",
        "drivingClass",
        "bloodType",
        "donorStatus"
     ],
     "additionalProperties": true
  },
  "tags": [
    "driving",
    "license"
  ]
}'
```

...and response might be the following:

```json
{
   "guid": "3f86a73f-5b78-39c7-af77-0c16123fa9c2",
   "id": "f2bfbf78-8bd6-4cc6-8b39-b3a25e01e8ea",
   "longId": "did:prism:agent/f2bfbf78-8bd6-4cc6-8b39-b3a25e01e8ea?version=1.1.0",
   "name": "driving-license",
   "version": "1.1.0",
   "description": "Driving License Schema",
   "type": "https://w3c-ccg.github.io/vc-json-schemas/schema/2.0/schema.json",
   "author": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff",
   "authored": "2023-03-14T14:41:46.713943Z",
   "tags": [
      "driving",
      "license"
   ],
   "schema": {
      "$id": "https://example.com/driving-license-1.1.0",
      "$schema": "https://json-schema.org/draft/2020-12/schema",
      "description": "Driving License",
      "type": "object",
      "properties": {
         "emailAddress": {
            "type": "string",
            "format": "email"
         },
         "givenName": {
            "type": "string"
         },
         "familyName": {
            "type": "string"
         },
         "dateOfIssuance": {
            "type": "string",
            "format": "date-time"
         },
         "drivingLicenseID": {
            "type": "string"
         },
         "drivingClass": {
            "type": "integer"
         },
         "bloodType": {
            "type": "string",
            "pattern": "^(A|B|AB|O)[+-]?$"
         },
         "donorStatus": {
            "type": "string",
            "enum": [
               "true",
               "false",
               "yes",
               "no"
            ]
         }
      },
      "required": [
         "emailAddress",
         "familyName",
         "dateOfIssuance",
         "drivingLicenseID",
         "drivingClass",
         "bloodType",
         "donorStatus"
      ],
      "additionalProperties": true
   },
   "kind": "CredentialSchema",
   "self": "/schema-registry/schemas/3f86a73f-5b78-39c7-af77-0c16123fa9c2"
}
```

If you are updating schema that is DID URL resolvable, the response will be in a forom of [Prism Envelope](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#prism-envelope), like this:

````json
{
   "resource":"eyJhdXRob3IiOiJkaWQ6cHJpc206ZTAyNjZlZThkODBhMDAxNjNlNWY5MjJkYzI1NjdhYjk2MTE3MjRhMDBkYjkyNDIzMzAxMTU0MjgyMTY5ZGZmOSIsImF1dGhvcmVkIjoiMjAyNC0wOS0yNVQxMDozNzoxNi4wOTM2MDlaIiwiZGVzY3JpcHRpb24iOiJEcml2aW5nIExpY2Vuc2UgU2NoZW1hIiwiZ3VpZCI6IjVjOTNmYTAwLWUwM2UtMzlkZC05NDdmLTI2NWI4YzFlYWQ4YiIsImlkIjoiNjhmMGQ4MDctYTcyYi00OTY2LTg1NWItMmIzNGJjMjYzNzAyIiwibmFtZSI6ImRyaXZpbmctbGljZW5zZSIsInJlc29sdXRpb25NZXRob2QiOiJkaWQiLCJzY2hlbWEiOnsiJGlkIjoiaHR0cHM6Ly9leGFtcGxlLmNvbS9kcml2aW5nLWxpY2Vuc2UtMS4wLjAiLCIkc2NoZW1hIjoiaHR0cHM6Ly9qc29uLXNjaGVtYS5vcmcvZHJhZnQvMjAyMC0xMi9zY2hlbWEiLCJhZGRpdGlvbmFsUHJvcGVydGllcyI6dHJ1ZSwiZGVzY3JpcHRpb24iOiJEcml2aW5nIExpY2Vuc2UiLCJwcm9wZXJ0aWVzIjp7ImRhdGVPZklzc3VhbmNlIjp7ImZvcm1hdCI6ImRhdGUtdGltZSIsInR5cGUiOiJzdHJpbmcifSwiZHJpdmluZ0NsYXNzIjp7InR5cGUiOiJpbnRlZ2VyIn0sImRyaXZpbmdMaWNlbnNlSUQiOnsidHlwZSI6InN0cmluZyJ9LCJlbWFpbEFkZHJlc3MiOnsiZm9ybWF0IjoiZW1haWwiLCJ0eXBlIjoic3RyaW5nIn0sImZhbWlseU5hbWUiOnsidHlwZSI6InN0cmluZyJ9LCJnaXZlbk5hbWUiOnsidHlwZSI6InN0cmluZyJ9fSwicmVxdWlyZWQiOlsiZW1haWxBZGRyZXNzIiwiZmFtaWx5TmFtZSIsImRhdGVPZklzc3VhbmNlIiwiZHJpdmluZ0xpY2Vuc2VJRCIsImRyaXZpbmdDbGFzcyJdLCJ0eXBlIjoib2JqZWN0In0sInRhZ3MiOlsiZHJpdmluZyIsImxpY2Vuc2UiXSwidHlwZSI6Imh0dHBzOi8vdzNjLWNjZy5naXRodWIuaW8vdmMtanNvbi1zY2hlbWFzL3NjaGVtYS8yLjAvc2NoZW1hLmpzb24iLCJ2ZXJzaW9uIjoiMS4wLjAifQ==",
   "url":"did:prism:e0266ee8d80a00163e5f922dc2567ab9611724a00db92423301154282169dff9?resourceService=agent-base-url&resourcePath=schema-registry/schemas/did-url/5c93fa00-e03e-39dd-947f-265b8c1ead8b?resourceHash=d1557ede168f0f91097933aa2080edaf2f14fddd8a7362a22add97e431c4efe2"
}
---

## Deleting a credential schema


Unfortunately, after publishing (especially in the [Verifiable Data Registry (VDR)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifiable-data-registry), deleting the credential schema is impossible.

The Identus Platform doesn't publish the credential schema in the VDR. This capability will get implemented in the later version of the platform. That's why the platform does not expose the REST API for deletion.

If you need to `delete` the credential schema, you can ask the database administrator or delete it from the
Postgres instance by `guid`.

For example:

```sql
DELETE
FROM credential_schema
WHERE guid = '3f86a73f-5b78-39c7-af77-0c16123fa9c2'
````

---

# Credential definitions {#credential-definitions}

## Overview

Credential definitions are specific to AnonCreds and build upon credential schemas. While schemas define the structure of credentials, credential definitions contain the cryptographic material needed for privacy-preserving features like zero-knowledge proofs and selective disclosure. Each credential definition is tied to a specific schema and includes the issuer's public keys for that credential type.

This section covers creating and managing AnonCreds credential definitions for privacy-preserving credentials.

---

## Credential definition introduction

## Abstract

This document details the structure, supported formats, and technical intricacies of Anoncred Credential Definitions within the Identus Platform.

## Introduction

An Anoncred Credential Definition serves as a standardized format for any given Anoncred Verifiable Credential. By embedding essential attributes unique to each type of credential, it lays the groundwork for diverse categories of verifiable credentials. Integrating this definition on a public blockchain ensures its availability and verifiability for all stakeholders.

The Identus Platform endorses the Anoncred Credential Definition, conforming to the [Hyperledger AnonCreds specification](https://hyperledger.github.io/anoncreds-spec/#term:schemas).

## AnonCred credential definition attributes

### name (String)

A descriptive and readable name indicating the type or category of the credential.

**Example:**

```json
{
"name": "{{CREDENTIAL_NAME}}"
}
```

---

### description (String)

A succinct descriptor providing an overview of the credential definition's purpose or category.

**Example:**

```json
{
"description": "{{CREDENTIAL_DESCRIPTION}}"
}
```

---

### version (String)

Specifies the version of the credential definition, using the [SemVer](https://semver.org/) protocol.

**Example:**

```json
{
"version": "{{VERSION_NUMBER}}"
}
```

---

### tag (String)

A unique identifier or tag associated with the credential definition.

**Example:**

```json
{
"tag": "{{TAG_IDENTIFIER}}"
}
```

---

### author (DID)

The decentralized identifier (DID) of the entity that created the credential definition.

**Example:**

```json
{
"author": "{{ISSUER_DID_SHORT}}"
}
```

---

### schemaId (URI)

A distinct reference to retrieve the schema from the Schema Registry.

**Example:**

```json
{
"schemaId": "{{SCHEMA_REGISTRY_URI}}"
}
```

---

### signatureType (String)

Indicates the type of signature applied to the credential definition.

**Example:**

```json
{
"signatureType": "{{SIGNATURE_TYPE}}"
}
```

---

### supportRevocation (Boolean)

Specifies if the credential definition incorporates revocation capabilities.

**Example:**

```json
{
"supportRevocation": "{{BOOLEAN_VALUE}}"
}
```

---

## Conclusion

The Anoncred Credential Definition is a versatile tool that offers a standardized approach for an array of verifiable credentials. By ensuring its correct incorporation within the Identus Platform, the issuance and validation processes of various credentials can be streamlined and made more efficient.

## References

- [Hyperledger AnonCreds specification](https://hyperledger.github.io/anoncreds-spec/#term:schemas)

**Note:** Throughout the implementation phase within the Identus Platform, it's crucial to replace placeholders (such as `{{CREDENTIAL_NAME}}`, `{{VERSION_NUMBER}}`, and others) with their real, intended values.

---

## Creating a credential definition

The Cloud agent exposes REST API for creation, fetching, and searching the [credential definition](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#credential-definition) records.

The OpenAPI specification and ReDoc documentation describe the endpoint.

In this document, you can find step-by-step instructions for creating the credential definition.

## Prerequisites

Before creating a credential definition, one must first create and then publish [prism DID](http://../dids/create.md), and then [create a credential schema](http://../schemas/create.md) to be used for the credential definition. Credential schema for credential definition **must** have a type of `AnoncredSchemaV1` as shown in [this](http://../schemas/credential-schema#schema-anoncred-schema) example.

## Step-by-step guide

The following guide demonstrates how to create a birth certificate credential definition.

### Define the Credential Definition for the Verifiable Credential

Assume you are aiming to define a credential for birth certificates. This credential definition has specific properties and ties to a schema in the Cloud agent.

Here's a sample content of the credential definition:

```json
{
  "name": "Birth Certificate location",
  "description": "Birth certificate Anoncred Credential Definition",
  "version": "1.0.0",
  "tag": "Licence",
  "author": "{{ISSUER_DID_SHORT}}",
  "schemaId": "http://host.docker.internal:8080/cloud-agent/schema-registry/schemas/{{SCHEMA_ID}}",
  "signatureType": "CL",
  "supportRevocation": true
}
```

### Create the Credential Definition Record

1. Use your preferred REST API client, such as Postman or Insomnia, or utilize a client stub that's generated based on the OpenAPI specification.  
     
2. In your API client, initiate a new POST request to either `/credential-definition-registry/definitions` or `/credential-definition-registry/definitions/did-url`  endpoints. They both take the same payload  
     
   1. `/credential-definition-registry/definitions` creates a credential definition that can later be resolved via HTTP URL  
   2. `/credential-definition-registry/definitions/did-url` creates a credential definition that can later be resolved via [DID URL](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-url), the DID includes a service endpoint with the location of the credential definition registry.

Please note: The `author` field value should align with the short form of a PRISM DID previously created by the same agent. It's okay if this DID is unpublished. You can refer to the [Create DID](http://../dids/create.md) documentation for more comprehensive details on crafting a PRISM DID.

3. Construct the request body using the following JSON object:

```json
{
  "name": "Birth Certificate location",
  "description": "Birth certificate Anoncred Credential Definition",
  "version": "1.0.0",
  "tag": "Licence",
  "author": "{{ISSUER_DID_SHORT}}",
  "schemaId": "http://host.docker.internal:8080/cloud-agent/schema-registry/schemas/{{SCHEMA_ID}}",
  "signatureType": "CL",
  "supportRevocation": true
}
```

4. Transmit the POST Request to Create the New Credential Definition

Once you've crafted your POST request, send it. Upon success, the server should respond with a GUID that uniquely identifies the new credential definition. The response bodies will be the same for HTTP URL endpoint and DID URL endpoint, as well as request bodies, the only difference will be the URL, and how this credential definitions will be resolved later, via HTTP URL or DID URL respectivly.

For ease of reference, here's a `curl` example:

```shell
curl -X 'POST' \
  'http://localhost:8080/credential-definition-registry/definitions/' \
  -H 'accept: application/json' \
  -H "apikey: $API_KEY" \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "Birth Certificate location",
  "description": "Birth certificate Anoncred Credential Definition",
  "version": "1.0.0",
  "tag": "Licence",
  "author": "{{ISSUER_DID_SHORT}}",
  "schemaId": "http://host.docker.internal:8080/cloud-agent/schema-registry/schemas/{{SCHEMA_ID}}",
  "signatureType": "CL",
  "supportRevocation": true
}
```

A potential response could be:

```json
{
  "guid": "3f86a73f-5b78-39c7-af77-0c16123fa9c2",
  "id": "f2bfbf78-8bd6-4cc6-8b39-b3a25e01e8ea",
  "longId": "did:prism:agent/f2bfbf78-8bd6-4cc6-8b39-b3a25e01e8ea?version=1.0.0",
  "name": "Birth Certificate location",
  "version": "1.0.0",
  "description": "Birth certificate Anoncred Credential Definition",
  "tag": "Licence",
  "author": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff",
  "authored": "2023-03-14T14:41:46.713943Z",
  "schemaId": "http://host.docker.internal:8080/cloud-agent/schema-registry/schemas/{{SCHEMA_ID}}",
  "signatureType": "CL",
  "supportRevocation": true,
  "kind": "CredentialDefinition",
  "self": "/credential-definition-registry/definitions/3f86a73f-5b78-39c7-af77-0c16123fa9c2"
}
```

### Retrieve the Created Credential Definition

To obtain details of the newly created credential definition, send a GET request to either `/credential-definition-registry/definitions/{guid}` or `/credential-definition-registry/definitions/did-url/{guid}` endpoints. Replace `{guid}` with the unique GUID returned from the previous creation step. Note that if you've created a credential definitoin via HTTP URL endpoint, you can retrieve it via `/credential-definition-registry/definitions/{guid}` and if you've created credential definition via DID URL endpoint, it can only be retrieved via `/credential-definition-registry/definitions/did-url/{guid}`

To exemplify this process, use the following `curl` command:

```shell
curl -X 'GET' \
  'http://localhost:8080/credential-definition-registry/definitions/3f86a73f-5b78-39c7-af77-0c16123fa9c2' \
  -H 'accept: application/json' \
  -H "apikey: $API_KEY"
```

or in case of DID URL

```shell
curl -X 'GET' \
  'http://localhost:8080/credential-definition-registry/definitions/did-url/3f86a73f-5b78-39c7-af77-0c16123fa9c2' \
  -H 'accept: application/json' \
  -H "apikey: $API_KEY"
```

You should receive a response containing the JSON object representing the credential definition you've just established:

```json
{
  "guid": "3f86a73f-5b78-39c7-af77-0c16123fa9c2",
  "id": "f2bfbf78-8bd6-4cc6-8b39-b3a25e01e8ea",
  "longId": "did:prism:agent/f2bfbf78-8bd6-4cc6-8b39-b3a25e01e8ea?version=1.0.0",
  "name": "Birth Certificate location",
  "version": "1.0.0",
  "description": "Birth certificate Anoncred Credential Definition",
  "tag": "Licence",
  "author": "did:prism:4a5b5cf0a513e83b598bbea25cd6196746747f361a73ef77068268bc9bd732ff",
  "authored": "2023-03-14T14:41:46.713943Z",
  "schemaId": "http://host.docker.internal:8080/cloud-agent/schema-registry/schemas/{{SCHEMA_ID}}",
  "signatureType": "CL",
  "supportRevocation": true,
  "kind": "CredentialDefinition",
  "self": "/credential-definition-registry/definitions/3f86a73f-5b78-39c7-af77-0c16123fa9c2"
}
```

Or in case of DID URL, the respoinse is [Prism Envelope](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#prism-envelope)

```json
{
"resource": "eyJhdXRob3IiOiJkaWQ6cHJpc206ZDYyYzFlNmJlZDdiYWYzYjgwNzFiZmE5NzUyNDg0Zjg5ODRlNzUzMWZjMmM1MGJiOTQ4OTE4YWYwNWFiMjAxOSIsImF1dGhvcmVkIjoiMjAyNC0wOS0yN1QxMjozNjoxNS42NDcwNTRaIiwiZGVmaW5pdGlvbiI6eyJpc3N1ZXJJZCI6ImRpZDpwcmlzbTpkNjJjMWU2YmVkN2JhZjNiODA3MWJmYTk3NTI0ODRmODk4NGU3NTMxZmMyYzUwYmI5NDg5MThhZjA1YWIyMDE5Iiwic2NoZW1hSWQiOiJodHRwOi8vbG9jYWxob3N0OjgwODUvc2NoZW1hLXJlZ2lzdHJ5L3NjaGVtYXMvZGFjZjNiOGUtODllYi0zYWQ2LWExNDYtMTIyZGRhN2Q2MjY0L3NjaGVtYSIsInRhZyI6IkxpY2VuY2UiLCJ0eXBlIjoiQ0wiLCJ2YWx1ZSI6eyJwcmltYXJ5Ijp7Im4iOiIxMDU2NTQwMzY4NTExMTkxOTg0NDI4MDQ5MDUxOTcxMzg3NzI2NjA5NjEzNjI1NjQ5NDI3MjY0OTIyMjEyMDY1Mzc3ODc0Mjc2NjQ3NjE2MTA1Mjg5MTEyNjg2NTI1ODI5MDI4MzM2MzE4MDQ0NjI5ODg0NDQ3NzI4Mzk5NzY5NDk3Njc3NTg1MDY1NDE2NjA4NDU4ODcxMTEyMDY3NjMwMTU1MjQyNzk5NDcwNzg5MDE3NDUwMjE3NTQ0NzU3NzcyMzc0OTk0MzQ4MTcwODU5MTM3ODQ0NzkwMzY4NzIyOTM2NTY3NzY0Mjk2NTUwMDQ5NzQwODc0NTExMTE4MTQ3ODE0MDIxNzAxNTU2MDY2NDYwMDE2NDQ2NDY5ODY3OTY1MjcyMDMwODEzNDY1ODU3NzQzMjE1NTYwNDUxMjU0NzI0MzU3ODI4NDk0NjAxMzI2MzI2MTczOTgzMzQ3ODk4NTMwODkxMjgzODk2MzY0MjQyNzY0MDY0MzM0MDQ1MTg4Nzk2NzkzMjg3MTgyMjgwMjg0OTI0ODkzMzIwNDg5MjcwMzg0MTg4Nzc3NzQ3MDYwNjg4Mjc1ODg3MzYzMTA1Mzg3MDE3MDAzMzYxMzkxNTcxNjgyMjk4OTY1NDYzNzAxMDYyODcwMTA1MDg5MjkzNTcyMDQ0MzA3ODQ2NzQ3OTQ5NDg3OTMyMTA2MjA2ODIxNzc1MTc1NTA3ODk5MTg2MzMyMTE2NjA5MzU4ODE2OTYzNDUyMzU2NjQwNTMxMTMzNjExMTkyMDY2NDk0MTA1ODEwNzg0NDg2ODAwNjA0Mjc2NDE4MzIxMzciLCJyIjp7ImJpcnRoZGF5IjoiNDk3NzU1NDY2NjkwNTI5MDcxMzE1NTI5NjE1OTY2NTE1MzQ3MzM2NjUyMzExNzg4OTYzODE5MjE4MzU5MTAyOTQ0NzczMjQ1ODgxNzEyMjcxNjAxMTgxNTA2MTQ2MzMwMjYwMTI5MDEyMTUzMzQ1NjU3MzU1NDI5MzM4MDg5ODU5ODc5MDQ5NDczNTAyODUxMjk1NDM4NTIyMzY5NzM3NzQ1NDc3MzkzODI5ODU1NzI0NDk3MzY4NjYyNjI5NTEzNDYzODgwODI3ODI2NjYxNDcyNjc3ODc5NDA2NTE1MDAzMzcwMTA0NjU0MzMyOTYwNzU1NTU0OTY3NDM0NTQwODIzNzcwODg3NTk2MTE4Mjk2NjkyODg1ODY4OTU2MDAwNzc1MjkxNDIyNzQ1ODEzMzUzODMxMzMxNDk4OTk3ODc5MjUzNTIxNTg1Nzg2NDExODk0NTY4MTE5NzI1MjAzNjg3MDg4MDU0NDgzODc3NDEzNDI5MjQ5NDc2MjYwNDU3MjIwMjA1Nzc1MzA1NTA4NzcxMjYyMjM0MTI1ODMyOTA1OTUzMDM4MTg1MDc2NjUzMTQzNDcwNDQzNTQ2Mzc0NDk4MTM3NTc2NzY0Mzk3NjU5NjY4NDc4MTU0Njg5MzY5NzUyMDY0MDg1MTYwMzM0ODQxNTQ2MjYxMzM2MzAyODE5NTI0MDI1MDkwMDcwOTEzNDQwMjA2NzI3NzEyODIzNTIzMjI2MDQ2NTY0Nzk3MTA1MjA2NTU1OTY0ODcxMzMxMzAxNjc2ODQyMTIyODMzMTM5NjEwNjM1ODg5ODkyODg4NTY3OTk5MDUiLCJsb2NhdGlvbiI6Ijk2NTAwODMyMjE1NTE5Mjc1MTAwMDc4MjgzMjkyNTU2NTM2MzYyMjQ3ODM4NzcxNDczMzAzMDMwMTQwMTA0ODM2OTUyODI5ODcxMTYxODUxMTgxODIyMDg1Mzc3Nzg4NTI5NDg4ODg3NTA5OTEyODIzNTE0NzIzOTM0MTg4Mzc1MjAxMjE5NzQ2MDYyMjExNzU0MTgxOTgzNjMxNDIzOTMwNTgwMTM2Njg1ODYwNjc3NzAxNDMwMTIwNjM2NTc5MTUwNzIwMzA5OTg2NDM1NzEzNDIwMzIxMjQyODIwNzI2NzIxNzM4MjE4Mjg1NzM1OTU0NTE0ODI3MTQ2MTk0NjAzMzUyMDExNTg3NzEwNDYxODEwMTcxOTI2NzQ2OTM5ODEzMzYxOTAzODYxNzIwODg0NjcxOTMzMjkwNTk1MDM2NjA2MzYyMTk1NTk4MTQ4NTM3NTkzOTQxNzg1NTI3NTA2Nzg1ODkwMTc4OTU0MTMwMzQ5MTI3NjA0NjQ2NjY3Nzg3Nzc5ODgxMDM0OTYwNjM2ODE4NDU3ODMzMTAxMTM0ODYzNDkxOTA1NDAyMzM1NzIwNjg0MjM4MTM1NjcwNDg4ODgzNzU5Nzk0NTAwMDc4MDkxMzMyNjI3NzEwMzc4NTQ3MTEwMDM5MTk1NTQ2MjE0NDUwMzAzOTc3NTI0NDU4ODgzMTI1NzIyNjY2OTYxMDczODA2MDE2NTg1NjA5Nzg2MTIwODI3MDcwNzY1ODA4MTg0NDAyMzYyMDk1NDkyOTc4MDAxOTU3MDQxMjA2NjU3MTgwMTA0ODA0ODE1NDE5MTg1OTE0MzY1IiwibWFzdGVyX3NlY3JldCI6IjkwMTQyODA1NjM0MTQxNTAwODgxMDI5NzU2NjQ0MDYzMzIyNDAyNTg4OTg5NTMwMzA4NzExNjI1ODk3NDc1MDcyMDUzNDIwMjU0MDQxNTY0MjUzMjA1ODczMjMyNDg0NTc2NDk5OTk0MDUzODg1MjY4NjcxODY0MzU3NTc4NTY5NjU4MTQyMTc3NDEzOTc5MjcyNTU3MDQ0MDAxMzc3MzU0MDQwMTk3ODAxMzgwMzEwMTQ5ODAzMTMxMDc0ODAwOTM2Nzc5NjU5MjU3NTcwNDkxODk3MzI2ODYyNDMxMDUzNDE3NTE0NTEwNzQ3MDI4ODY2OTE3ODA4MDA5NTQyOTM1NDI2NDQ4NzQwMjI0MjAwNDAzMjIwNTY4MDA5ODgxMDI5ODIwNDUzMjE0MTU5MzY2MzU4NTc4NzE3Mzc5NTMwNzQ4NDk1NzM3MDQ5NjMwNDM5NjIwODcxODk2NTMwODQ3ODAzMjE3NDUyODk4OTI2NDYxNjQ1NTAxODU5MzAxMjM5MjAyOTA5NzY5NDUyMTIwNDQ0NDUyNzAzNzgyNDIyOTQ1NjE1MjY3Nzc4MzI0NzkyNTYxNTYzODg5MzQ5NDI3NzU3Mjk5OTc3MTA5MDE5NjMwOTAxMzk5ODg2NTg2NTU3NTE0NTg5MTM0Nzk5MTk3OTY3MzIxMzQ3Nzc1Mjk5OTIxMDU0MjE0NTM5MDQ3MjA5NTAwNjMyNTY5NDM3Njc1MDQ5NzQxMDQxNDE4OTczNDEyMjYwMzUxODI5Nzk0MDk5ODIzMzg5OTU1MDQ1NjU0MjMwMTA5MTU0NzA1NjU3MDk3NjczODc0In0sInJjdHh0IjoiNTQ4NTk3NDYwMTc5NjcxNzIwMjg0MjIxOTMwODU0NjI4MDMyOTQyNDMwMDI2NTM2NDk3NTY3MDcyOTEzMzIyOTc1NzE3NzgwMzY3MjAwOTUyMjEwNzk2NDY3MTUxMjI4OTg1OTU0MTM3MDQzMDIyNTQ4MjA0NzU0NDE4NTc0OTU1NDA0ODgyNjc1MzI2Mzg5NDU0NjAzMzkwNjE3MzAyMzYyMjMwMTM3NDI1ODgyODcxMTA2NjQ1Nzg0OTg3MjgwNDUwNjIxODc0MjQxNDkzOTQzNDkyMzc5NjczMTk3OTYxNzk2Nzc4NzYwNTI4MzI5NDU0MTc4MzM2MDIyMjM4OTYwNjQ1NDgxNzc0NDAyMzY3NDAwMTMwNzk2OTc1MzM2OTU2NjI3MzM3MzMzMjUxMzY4MTY3MjkzMTk1NDU1MTA1MTUyOTM4MTU2MzU1NDg4MDI5ODk4NjUyOTM1Mzc1MTI5NTMyNTI3NjUzNjgyMzE1MzcwNjA2MTkxNzkxMzkyMjUyODI5NzkyMTc3MDc3ODA3MDU1MjAwNzU2NjI5NjI3NDI2NDc5MDg3MTI2NDk3MDQ0NDU5MDY2ODE3MTExODYwMjUzMjQ1NTc5NzMyMDMyNzk3NjQ0NjM0ODY2MTU0NDI2NjIwNzE2ODc3NjcxNDI5NjIyNzAzMzU5NzEzNTM1MjQwNjUzMjcwNzEyOTc4NDk1NDI0NjYyNTkxNDk3MTM0NjkyODM5MDMxMDYwMTMxODkyMjE4ODQ0MDcxNjg2MjIyODE0MzM4NTY2MDIyNjM0MTQ3ODY0MDc5OTg4Mzc4MjQ1OTE4NTkiLCJzIjoiNzkxNDQwOTkzMTkzMjI4NzQ4Nzg3NDA2MTM4MzQ3Nzg1NTI1NjUwODgyNzE3NTg4MzcxODgzMzk4NDg2NTg3OTc1Nzc0ODQ1MDY1NTE3Mjk4NzM2NTAxMjU4MTUyNjg1NDI1MDA1OTU3MTUwNjk1MDE3MDUwMzc2NzE4MDQ2MjUyNTM2NDI4MTQ0MzEyMzMzMDgzMTI5MDMyNDE2NzI3MzI0NzgxNTMwMTI4MTE0MDQ3MTU1ODc3Mzg5Njg1MTE1MTMyODU4Njk3NTYwNzgwMjE2MTAxOTI1MTc3NzYxNzE5OTkxODc3OTE4OTAyMzQ2MDA2MDg5OTU2MTExNTAwNzQ5MDkwMzMxNzY5Njk4NjUzNzIwNTkyNTc5ODEzMzY4Nzk5NzI0MDg3ODAwMjAwNTM3MzYwNTg3NjQwNTc3ODYyMjAwMzE4NTIxODI2NjAyNTA3ODA5Mjc3NzUwMTY0ODUyNTY1MjQwMzgwMDU0MTM1NDY5NzUxNDEwNDk4MTQ3NzMxNDIxNzU0OTE2NDIwMjgzNTM2NzY1NDA3OTE3NDcxOTgyMDQxMDkyOTYwODUxMzA2NjM0NzgzMDY0NzA5MTY2NDc1MDc3MTk4MTAxNDIwODQ5MzA2NzkyMTU3NjMzMTUwNDkwNjgyMDE2NDk1NDMwNDAwMjI5MDEyOTM2MTg2Mjk2MzMyMDcwMzA2OTE2MDM5NDk5ODQ5Njk5MTgxNDQ2ODYxNzgyNTM3OTU5ODc0MDAzNzc3MzE3NjY4MTI3NDg4MjE3MzcxOTIyNDc3ODMzMDAxODMyODAwMjEyNDU5MzkzNTgxNjMiLCJ6IjoiNzI5ODc2NDE0MjA1NzkyNTk2ODc4NTQwMTAzODUxODM5NjY3ODIyMDE5MzQwODMxNjgxMTgxOTI1MTY3ODcwMDU4OTk0ODg4MDE4OTg4Nzc2NTczMDEyMDQ2NTc3ODI4OTY5OTA5NTE4MTIwNzMwMTc4MTQ2ODQ2NDU0NjczNDk3NTQ5MTAyNjAxNzY0NzYzMDUyMjExMTcwNDMyNzQyNTI4Nzg1OTU1NjIyOTE4NzMyMjQwMjk5NDg2NjY5OTc5MjI1ODk5NzMwMzUwMDMyMjM0NTIyMzA5MjU3MjcxNDE5MDc1MDg4MDg5MzkzNjQxOTYxOTA0Njg2MDAwODgwNTQ5ODIxMTEyNTIyMTEyMDA3MzUwODA5NTY2NjYzODM5NjA0NjEwMzQ5NDM4MTgxMzY0MDE5MzU2NTE4NTcyOTA3Mjk5OTY4NzU2NDE4MjIzNDM1ODYxNjE2MzkwNDIzMzYxMzM0NTE1NDk4NDcxMDk4NjA2NjY0NTczOTE1ODQ0NTg2MjYxMTQxNzQ1MzYwMTkzNjc4MDA4MDk3OTE5MTIxNDgxMDgwODM5OTAwMTMyMTY2NTU4NjAxMDAyMzQxMzc5NTczMzc3NjcwNDk2MDc2ODMxMzU0MzE2MjEzNDY3MDA1MDcyMDgxMDgwODcyODIxMzEwMzQ2NTc1MjgzMDYwNDEwMjA2NzUxNzk1NjM1MDY1NDM5MTQ5OTI1NTc5MjIzMjM4NTI0NzA5OTg5NzgzMTA0Njc3NTc0MDE1NjY5MjMxNjMxNDAwMjI5NTc2OTM1NTc3NzE5MTI1MDIwNDczOTcwNjU3MjgifSwicmV2b2NhdGlvbiI6eyJnIjoiMSAxMjI1OTQ3NDcyOTc2QjVBQTgyMjlDMjI4RUFFNUI3NThDMDlGNkIwN0I1NDU4MEJDNzYzNTMwNkJFQjI1ODkyIDEgMDlBMzEzOEIzMzZGMUZCQUJEMEIxM0E5QTNBNzM1RDMyQjRFN0YzREZCNTE1QzkwRkQwNzVGNTY5QTQ5RTUyMSAyIDA5NUU0NURERjQxN0QwNUZCMTA5MzNGRkM2M0Q0NzQ1NDhCN0ZGRkY3ODg4ODAyRjA3RkZGRkZGN0QwN0E4QTgiLCJnX2Rhc2giOiIxIDI0OTA1M0Y0NUJCQTg3MDA3NzhDRjE1ODQxREZERUIwQUY1OTQzRTFFMkYyRUVFMDNFQUI2RDgwOEFDMkYxRUUgMSAwNzQyQ0U3QjM3MjAzNTc1QThDNUQzMjc1NzY3RDkxNUQ2RUVDRkVDMzc5NzkzOTNGNjYxRkY1NDE0QzdENEJBIDEgMThEN0ExMEUzNjJGQTE5MkYxRDJFNTE3OUQ1ODBGMTk0RTM5NDEzMTE1MTE3MDBGQkE3OUE0QTIxMkUxN0Y5RiAxIDA3QzNDQTRFMjBDOTkyQTEwQ0NDNzI0NDlBQUU5NjRFMDFEREZCNTBGQ0VEQkUxRTZDOEQwRUMzOThGOTU3MUMgMiAwOTVFNDVEREY0MTdEMDVGQjEwOTMzRkZDNjNENDc0NTQ4QjdGRkZGNzg4ODgwMkYwN0ZGRkZGRjdEMDdBOEE4IDEgMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMCIsImgiOiIxIDE5RUMwQkExMTRFMTBBN0Y5RkVCMkU3MzMyNTZDQzk4NTU0RkVEQzFGRjgzN0REQUEzNDQxN0U1NzYyNTg0NzkgMSAwMTYzN0VGQTBCQ0E2QjNENjZDMzE1RUU4QUJGNzNCMEU5MUVCNTA1REE1QUZCRDk2MTA5M0QwMjk5RjRGQUVEIDIgMDk1RTQ1RERGNDE3RDA1RkIxMDkzM0ZGQzYzRDQ3NDU0OEI3RkZGRjc4ODg4MDJGMDdGRkZGRkY3RDA3QThBOCIsImgwIjoiMSAxQUMwQUY5NTBEN0UyRkE0MDNDN0UwRkI5RThDNTE4N0Q3QTBBMzFBMzgzQkFFNDVGRUEyMUFGNjZFOEM1MTkxIDEgMTMyNTZGMDBCODQyOTNGMjA3M0VCOUVFRTFCMUY4MDU4MTAyNkMzMkE1RjdGQThDNEExQTEwODMyNkE2Mzc0NSAyIDA5NUU0NURERjQxN0QwNUZCMTA5MzNGRkM2M0Q0NzQ1NDhCN0ZGRkY3ODg4ODAyRjA3RkZGRkZGN0QwN0E4QTgiLCJoMSI6IjEgMUFEMTUwOTJDMTNDNUI0NUY4MDk3OENFQ0ZCM0Y5NUUyMTNBMURBQ0NBNDMxRkEzNkE3MUU4OTg4MUE2RkVDMCAxIDI0NEEwRTMyODkyMjcxNTE5NjYwMEQ2NkYxRTU3MzA4NUE0MURDM0UxRkU4RjZEODU0OTY1RTNFMkFFNkY2RTIgMiAwOTVFNDVEREY0MTdEMDVGQjEwOTMzRkZDNjNENDc0NTQ4QjdGRkZGNzg4ODgwMkYwN0ZGRkZGRjdEMDdBOEE4IiwiaDIiOiIxIDBGMEZDRUJDNTE1MzI3MjEyNDI2QzIwNkY2ODQ4N0U5M0EyNjc1NTdCRjg5QUJENkIxNTBDMzA2MUM1RDNDRjcgMSAwN0I1OUVFMThFREY1RThFQzEzM0ZFMDY1N0RDRjEzMTAxMUYwQzFERUM1RjQ2NDU3MUMxMTIyMDc3RjMwMDVEIDIgMDk1RTQ1RERGNDE3RDA1RkIxMDkzM0ZGQzYzRDQ3NDU0OEI3RkZGRjc4ODg4MDJGMDdGRkZGRkY3RDA3QThBOCIsImhfY2FwIjoiMSAyNDAzOTI2NjBDNjYxMDhCQjgzNDkwMDk1NzBGQTQ0MkFBRDAzQTY1MTU2NkQwMUE4MkVFRDQwMDNEMzY1QzY3IDEgMEMyQTE3Q0Q5MDM4QzE4QzU3Q0QyNDY4RTA2RENFNzU4NEE4Q0I4RTgwMzkzQjNBMjBCRkZERTY5MEQ0QzM2MCAxIDIzQ0ZFREE0NkJDMThEQUMzODdCRDdDMUQ2RjM1MzYyMEY2M0E0REI0OEM5MDBEQ0QxRkYzMEEzNkRDNjQwNEYgMSAwREJCQzQ3MUUyMzUwQ0I1QkU2RkFGN0RDOTBDQkQ3Q0MxRTkyOUUwQ0MzOUJCRDlBOUNBOEJFM0Y3REVCNkIwIDIgMDk1RTQ1RERGNDE3RDA1RkIxMDkzM0ZGQzYzRDQ3NDU0OEI3RkZGRjc4ODg4MDJGMDdGRkZGRkY3RDA3QThBOCAxIDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAiLCJodGlsZGUiOiIxIDAxQjQ4Mzg3QjlGRDlCQUQwRTg1RTJCMDFCNDI5OEJCNzNDQ0JBMjE5NzdDQTk4RDZFRDQ2MDM2ODNEODFDNjAgMSAxRTQwMzE1RDYyNTY1RDg4MjBFM0RGODRFMEUzRTE4RkI4OTFDNkJDNTJFOTJEMzhCNTIzMDNFOENFNDI4Q0U2IDIgMDk1RTQ1RERGNDE3RDA1RkIxMDkzM0ZGQzYzRDQ3NDU0OEI3RkZGRjc4ODg4MDJGMDdGRkZGRkY3RDA3QThBOCIsInBrIjoiMSAwMjNCRDYzRDM0RkE0NjYwN0REMDgyN0Q2MjlDMzdCOEVBMEZFMjJFOUQwM0JCOTgzNjlBMTI1RjczRDc0OERGIDEgMEZFMEE3Nzg1MEYyRkNBOEFEREFCMTlCQTY0QUYzMEVFM0JBNTNDRTMwRTUxOEY3MzcxRkNFMTQ1NjQ1MTg0MCAyIDA5NUU0NURERjQxN0QwNUZCMTA5MzNGRkM2M0Q0NzQ1NDhCN0ZGRkY3ODg4ODAyRjA3RkZGRkZGN0QwN0E4QTgiLCJ1IjoiMSAwMkY0OUVBNzFGMjEzNkRERjNBNDkyRjMzRjFDRTYzMEFFNzQ1OTc3NDVDQkFEQkZGQzAyQjczNTVEOURBREJGIDEgMEU2OTNENjlFNUUzRDYxRjhGMDlFQ0UwRDJBMDgwOTUzQzkyNzMxOUY2OTQ0NTRGOTJFRjI0RDBBMDIwMjU0RCAxIDIxMTQxRDg0MTU2QkQxMkUzNEFDRDFDMkY3QTA2NzRCQkU0NUMwRjUyRjBFM0M3RUZEMjNDOURCNURDMDc2MjMgMSAxOUNENzY3NUQ5MTk0MzlFMjAxRDQzREFCNDdDREVDQUM1MTE0QkEzQzQyRTE4QkY1REQ5M0Y5N0UxMkNBNkMyIDIgMDk1RTQ1RERGNDE3RDA1RkIxMDkzM0ZGQzYzRDQ3NDU0OEI3RkZGRjc4ODg4MDJGMDdGRkZGRkY3RDA3QThBOCAxIDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAiLCJ5IjoiMSAwOUU5REJGQkI4QTUxNTZBQjdENjc5QkNCOEZCNkE0RkU3NDJENTVDMURBQzAzNzA2QTQ1QUQyOUFCM0VFNzBGIDEgMTg0OEI3RkYxOTQ4MUFEQjY4RkRFMDI0MEExRkFDOTVBODc0OTg3NUVFOTg4OUZENzlCMjU5NTRERUJDODI4QSAxIDEwODIzRkNDMkI0MTNFRDJFRUEzQkQ4RjJCRjNBMUZGMjc3QjdFQTRGNkI1RjBGMTZDODQ3M0QyRjc1RTc0MTUgMSAxMTJDRjc0NkVDNzVDMEU2MDY3MzJBQTQ1ODZCNDM3MDVDMUY2MEJDREQ5RjBDMTUwQzVGMDYyMkQ3NTNCMTU1IDIgMDk1RTQ1RERGNDE3RDA1RkIxMDkzM0ZGQzYzRDQ3NDU0OEI3RkZGRjc4ODg4MDJGMDdGRkZGRkY3RDA3QThBOCAxIDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAwMDAifX19LCJkZWZpbml0aW9uSnNvblNjaGVtYUlkIjoiUHVibGljQ3JlZGVudGlhbERlZmluaXRpb25WMSIsImRlc2NyaXB0aW9uIjoiQmlydGggY2VydGlmaWNhdGUgQW5vbmNyZWQgQ3JlZGVudGlhbCBEZWZpbml0aW9uIiwiZ3VpZCI6ImVmNGE1ZGRjLTA2MTItMzRmNi1hZjI4LWM1ZDE0N2FhNDM4MCIsImlkIjoiYjc2YWUwOTMtN2U0ZS00NWE4LThmOTEtMTJhMmMzMGNjYjYzIiwia2V5Q29ycmVjdG5lc3NQcm9vZiI6eyJjIjoiMzA3NDE2NTUyNjQzOTM2NzgxODg3MDMzMzY5MTYxOTk3NTUzNDMyMjQ1OTE3OTA4MDk3Mjg2MTcxMTcwOTYyOTAzNTY5NDQ5MDU3NjYiLCJ4cl9jYXAiOltbImxvY2F0aW9uIiwiMzc2ODI3NzMxMDQ2MzM0MTkzOTYwMzU4OTkwMzgxMDA3NDc5Mzc0MjM2NDQ5OTg5ODA5MDY0MDg3NDg4MDk4MTQ2MDgzMjc5MjI5ODA5NDQ0MjM2OTQzODU2MTk0MjAzMjM0OTE0NTAxMzI4NTc5NDQwMjg3NzM1MTc5Mjk4NjMzNDg5MzIwNTU5NzE1MTk1MzU0MTYwMDg5MzY0MjA3MzMxMzQyNjAwODI2NDAzNDI5MjM0OTUxNjU3MDI4NTI0Nzc3ODc4NzQ2MzAyMDE0OTA3MDg0NTg3MTI1Nzg5NzMyMDY0NDA1NTQ2Mjc5NDczOTIyMTEyNDg2NzU3Nzk2MTQ0MTAwMDcyMjc2NjQ3NTc4NTQ2OTAxMjEwMDAzNzc2NDA4MTg1NTE1NzI5MzM4OTgzODY3NDQyNTEyMzI3NzYxMzYzMDAxODg0Mjc4NjI0MzYyOTQ5ODc3OTQ5MzA0MzAxMjkxODA2NTk5ODkyNzA2OTE3OTQ5OTM0NDI3ODQzODAxMTgxODQxMjcwOTM0ODQ1MTc0NDE2OTIwNzE2MTM3OTU2NjA0NDA4MDc0NTA0OTk4NjIxMjc1NTM0MTIyMjYzMTIyMTUyNDUxMzAyNzAyNzE2ODYwNDc5MDQ3NDk0MTE2NDc3NDAzMjkzOTk4NzUwNTcxNTQ3MjI5NTQ1NTEwNzM1MDYzODUyMDc3Mjg0OTYwMTgwMDU1MDA4NjA5Njg3Mjg0MjE2MzMwMDI4MzY1Njc2OTAzMDIzMzg2NDI3MDk2NTc0MDExNzMzNDAxMTY2NDI5MjA0NzQ2MzI2MDk2MjU2NjQ5MTE1NzUyMjczOTAyMDM5NDM0Mzc2Mjc2MTI3MTMyMzEzOTE3OTI3NTUzOTU4NDUxNDY5ODYwMTk1NjkzNDIwMjIxMzI1NTg1NTg2NzMxIl0sWyJiaXJ0aGRheSIsIjI1NTAwMDM2ODMzNDA5MzQxOTUzOTE4MDAyODI0MTA1MTEwMzA4OTA3NDMzODQzMTQ5OTE3ODc3MTUxNDc5NDY2MzAxOTQ5NjY4MzA1NTgxNTc0NDE2Nzk1OTkyMDYzNzAwMDM3NzY4NDIyMzIxNzk2NDE1OTA3NjY1MzQ4NzI2MTE2OTkzNzgxNDgxODIwNzAzODU4MTA0NDgzOTk0MjI4MDQ1NjUxODMzODY4MjgzMjg1NDkwMzIxMzk3NTQzMjQyNzY5OTU3ODY0ODMzMDAxNjk0OTA2MjUzOTIwOTY1NDU4MzcyNzcxNjczOTExNTk0ODQyNjAzNDM5NTMzMzQwMDc5MDQ2NjM1ODMzNDA1NzcyNDAwMzQyMjQ5MTA4NjA5OTkyMTk3ODY5NjY3MzE5MjA1MzQ5ODg4NTUyNDAzNDc0Mzc0NDgwMzMyMTU2NjQyMjI2OTQ3MDUzNDQwNTEzNzg5NjcwNzY5OTU1MzQ5NjM0NzMyOTkwOTgzMDk5NTkyOTIzNzQ0NTc1OTE5MTE2Mjk3MDY3NjU4OTE0ODgwODAyOTMyODc3OTkwNTQwNDYzMDAxMDE4OTU5MTg0MzA4NzE3OTMwNzQ1OTk2OTE0Mjc2MzA2MzQzNTkwODA3MDk2NTM1MDYxOTI2ODY3MTU0NjQ2NDgzNDU4ODQ1NjY2NjQxNjU4MjA4NDM3MzA1MDM4OTc0NjQ4MDkzMTUyNjg1NTk0NDEwNzk2Mzg2OTI5MDM4MDcwOTQ2ODg1ODYxMzYyMDI2NjE0MTUxMDMxNjQ1OTY1NzMwOTgxNjY5NDc4MTQyOTEyMTMzNzQ1MDc3NTM3NDY4NDQ0MzU0MjA2NTA3NTU4OTE5NTQ5OTUxNTQ0MzI4MTkwMDQwMzA2MzQ3NDc0NzcwNzY1NTU5MDA1MTA4ODgwOSJdLFsibWFzdGVyX3NlY3JldCIsIjQ5MDA3ODIyMDM2OTkwODI2ODQxMTkwMzM3MTU4ODI4Nzc1NzE0NjM3MDc3OTg5Njg0MTA5NTc0NjA4ODQ3MjA2MzIxMTA2ODQxMzcwOTkxNTQ2NTMwMjYwMDIzNDU1MDk4OTcyNjcyNjUyNDQzMDQ1NzI2MTM3MTk2Nzc5NzUxNjQ2MDQyOTk3NjgyMzYyMTQxMTA1MjY5NTQ2MTkxMjMwNzcyODc5MzczNjcxNTY1ODk5NjAxMjQ2MjYwMDMzNjY4OTQyODY1MjgyMTg3NzA3OTc2NDcyNjg3NzU5NTA1NjQ4NjgyNzc0NTI3NzAzNzQyNjQxODc2MTgwOTU4OTI3MzMxNjcwNDAxMzI4ODI4Mzc2MjMxMTk4NzI3ODUyMzU4NTUxNzUzNTEwNTk1MzQwNjI4MTA0MjkxMDU5ODI4NDU2NTUwOTQyNjI0MjA3OTA4ODMyNzQ5NjM5ODQwODIxNzEyNTU1Mjg2MDE5Mjk2MTY4OTQ0NjEzNjg3NDM5MzU5NTMxNjA2NzQ3MDc3Mjc3MDE4MDE5MzU1MjM4NzUyMzUwMTYzODE3MzQ5ODI1ODY4ODQ0NTk0ODU5NDQ3Mzg0NDA0NjMxMDk3NjkzNTQ5NTk0NjkwNDcyMTI3Nzg1MTg4NDI5MTAxNjE5MDI4MTc1NjI3MzIzOTUzNTAyOTUwNzk3NTIwMDQ0OTE2MDY5MDg4MjczODU3NzYwNzU5MDkxNTQyNDQ4ODc4MzU5NTc1NDk5MjM1MzIzODc4NDA1MjIwNDc5MzMxMzE5MDE0ODg2NDk5NjE0MzI2MTA3MTM2MjA1ODg4Njg3ODU4Njg4NTgxNTI4NjQxMzI4ODg1NzkwNTc0MjAwNjUxMzA0NzU4NTE0MTAzNzAzNDM2NDAwMDM0NTQ4MjAxMDc5NDM4MzQ2NjU5OCJdXSwieHpfY2FwIjoiNDk2Njg1Mjk0NTI0Mjc4Njg0NDc0OTQ1MDQ0MzI5OTU5MjI0NTA4NzY1NjMzNTg4MDQzNDUwNDgxNTE4MzQ5ODk4NDAxMjk3MzQ2OTk0MjQ5ODEwMjIzODM3MzE0MDc0NDI1NDQ4NzU1OTYwMjg2MTU3ODY4NTk0NDU2MDE2Mjc3NTcyMjAxMDgwNDQzMTA3NjQxNjM5MzExNDAyMDUyOTM3NjczMzI5MDM4NjU0Nzc2MDI1MTA0OTYyMTEwMTQzNTg5NjA3NjEyOTM2NDI4MDc3MjQ1Nzg4Mjk2OTc2MzU5ODk5MjMwMjQwNTEyMjc5MzU0MDg2MTMwNjIyNjYzMjgzMTA4Njk4MTM0NjU0NTMzMTQ1NjY4NDU1NjExODU2MDY2Mjc2OTgxMjUwMDAyMTQ2ODgwNTk0NzM3MzU2MjA2MDE1NzUyMDAxNjE3ODU2OTI0MDk3NjU3ODU2NjkzNTYwODA1ODQ0MDQ3NTY3ODE2NTczNDUxNTg1NTUzODMzMzQ0ODE0NDMzMzMxNzk2ODc5Mzg5NjgyODM3MjQ3OTU0MDUwOTcwNzE0Mjg2NDQwNDg2NjY5MDE4MjMzODkxNTA0ODE0ODk3NDAyODYxNzMxMDg2NjEzMTU5MDM0NDEzNTIzMzk2NjE0NjE4NjQ3NjczNzgzMDIyMTAzMTQxNTg0MTAwMDY0NTE0NjA4ODYxMTY2Nzg3NjYzODMxMjE5MTM0NTYwNDAyNTEzMjQ5MDg0NzA5NDM2ODM5NTM5NTQ3MzQwNzMyNDYyOTE4MDg3NzU3MTYyOTQ2Mzk5MTMzNzA0MTA0MzMwNzUxNzU3OTMxMTE0OTkwNzA5NjU3NDQ3MjU0OTM1OTc3MDExNjQzMTc5NjU4MTg5MzI3MDg4NDY5NjExMzcxMTg1OTY2MzIwNzU3ODM1In0sImtleUNvcnJlY3RuZXNzUHJvb2ZKc29uU2NoZW1hSWQiOiJQcm9vZktleUNyZWRlbnRpYWxEZWZpbml0aW9uVjEiLCJuYW1lIjoiQmlydGggQ2VydGlmaWNhdGUgbG9jYXRpb24iLCJyZXNvbHV0aW9uTWV0aG9kIjoiZGlkIiwic2NoZW1hSWQiOiJodHRwOi8vbG9jYWxob3N0OjgwODUvc2NoZW1hLXJlZ2lzdHJ5L3NjaGVtYXMvZGFjZjNiOGUtODllYi0zYWQ2LWExNDYtMTIyZGRhN2Q2MjY0L3NjaGVtYSIsInNpZ25hdHVyZVR5cGUiOiJDTCIsInN1cHBvcnRSZXZvY2F0aW9uIjp0cnVlLCJ0YWciOiJMaWNlbmNlIiwidmVyc2lvbiI6IjEuMC4wIn0=",
"url": "did:prism:d62c1e6bed7baf3b8071bfa9752484f8984e7531fc2c50bb948918af05ab2019?resourceService=agent-base-url&resourcePath=credential-definition-registry/definitions/did-url/ef4a5ddc-0612-34f6-af28-c5d147aa4380?resourceHash=ca8ea2c80ff1e07978e2ed59245186fbb9992daedbecb651535ad5996be372c1"

```

Remember, in the Cloud agent, the combination of author, id, and version uniquely identifies each credential definition. Thus, using the same agent DID as the author, you cannot establish another credential definition with identical id and version values.

### Update the Credential Definition

To update or upgrade an existing credential definition, follow the steps outlined below:

1. Begin with the first step and make necessary modifications to the Credential Definition.  
2. Update the `version` value to reflect the changes made. This is important to ensure that each version of the credential definition remains distinct.  
3. Create a new credential definition entry with the updated version and schema.

## Note: When you make changes to an existing credential definition, it's essential to version the new entry accurately. This ensures clarity and avoids potential conflicts or misunderstandings among different versions of the same definition.

## Deleting a credential definition

Unfortunately, once published (especially in the [Verifiable Data Registry (VDR)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifiable-data-registry), deleting the credential definition becomes unfeasible.

In the Identus Platform, credential definitions aren't published to the VDR. This functionality will be incorporated in subsequent versions of the platform. Hence, the platform currently doesn't provide a REST API for deletion.

If you need to `delete` the credential definition, it's advisable to contact the database administrator or directly remove it from the Postgres instance using its `guid`.

For example:

````sql
DELETE
FROM credential_definition
WHERE guid = '3f86a73f-5b78-39c7-af77-0c16123fa9c2'

---

# Credentials

## Overview

This section covers the complete credential lifecycle: issuance, holding, presentation, and revocation. Hyperledger Identus supports multiple credential formats (JWT, SD-JWT, AnonCreds) and issuance methods (DIDComm, connectionless, OID4VCI).

Credential issuance involves an issuer offering a credential to a holder, who accepts and stores it. Credential presentation involves a verifier requesting proof, and a holder creating a verifiable presentation. The tutorials in this section guide you through each method and format.

---

## Issuing credentials (DIDComm)

# Issue credentials (DIDComm)

In the Identus Platform, the [Issue Credentials Protocol](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#issue-credential-protocol) allows you
to create, retrieve, and manage issued [verifiable credentials (VCs)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifiable-credentials)
between a VC issuer and a VC holder.

## Roles

In the Issue Credentials Protocol, there are two roles:

1. The [Issuer](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#issuer) is responsible for creating a new credential offer, sending it to a
   Holder, and issuing the VC once the offer is accepted.
2. The [Holder](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#holder) is responsible for accepting a credential offer from an issuer and
   receiving the VC.

The Issuer and Holder interact with the Identus Cloud agent API to perform the operations defined in the protocol.

## Prerequisites

Before using the Issuing Credentials protocol, the following conditions must be present:

<Tabs groupId="vc-formats">
<TabItem value="jwt" label="JWT">

1. Issuer and Holder Cloud agents up and running
2. A connection must be established between the Issuer and Holder Cloud agents (
   see [Connections](https://hyperledger-identus.github.io/docs/connections/connection.md))
3. The Issuer must have a published PRISM DID, and the [DID document](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-document) must have at
   least one `assertionMethod` key for issuing credentials (see [Create DID](https://hyperledger-identus.github.io/docs/dids/create.md)
   and [Publish DID](https://hyperledger-identus.github.io/docs/dids/publish.md))
4. The Issuer must have created a VC schema as described in [https://hyperledger-identus.github.io/docs/schemas/create.md).
5. The Holder must have a PRISM DID, and the DID document must have at least one `authentication` key for presenting the
   proof.

</TabItem>
<TabItem value="anoncreds" label="AnonCreds">

1. Issuer and Holder Cloud agents up and running
2. A connection must be established between the Issuer and Holder Cloud agents (
   see [Connections](https://hyperledger-identus.github.io/docs/connections/connection.md))
3. The Issuer must have created an AnonCreds Credential Definition as
   described in [https://hyperledger-identus.github.io/docs/credentialdefinition/create.md).

</TabItem>
<TabItem value="sdjwt" label="SDJWT">

- 📌 **Note:** Currently we only support `Ed25519` curve

1. Issuer and Holder Cloud agents up and running
2. A connection must be established between the Issuer and Holder Cloud agents (
   see [Connections](https://hyperledger-identus.github.io/docs/connections/connection.md))
3. The Issuer must have a published PRISM DID, and the [DID document](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-document) must have at
   least one `assertionMethod` key for issuing credentials and the curve must be `Ed25519` (
   see [Create DID](https://hyperledger-identus.github.io/docs/dids/create.md) and [Publish DID](https://hyperledger-identus.github.io/docs/dids/publish.md))
4. The Issuer must have created a VC schema as described in [https://hyperledger-identus.github.io/docs/schemas/create.md).
5. The Holder must have a PRISM DID, and the DID document must have at least one `authentication` key for presenting the
   proof and the curve must be `Ed25519`.

</TabItem>
</Tabs>

## Overview

The protocol described is a VC issuance process between two Identus Cloud agents, the Issuer and the Holder.

The protocol consists of the following main parts:

1. The Issuer creates a new credential offer using
   the [`/issue-credentials/credential-offers`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/createCredentialOffer)
   endpoint, which includes information such as the schema identifier and claims.
2. The Holder can then retrieve the offer using
   the [`/issue-credentials/records`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/getCredentialRecords)
   endpoint and accept the offer using
   the [`/issue-credentials/records/{recordId}/accept-offer`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/acceptCredentialOffer)
   endpoint.
3. The Issuer then uses
   the [`/issue-credentials/records/{recordId}/issue-credential`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/issueCredential)
   endpoint to issue the credential, which gets sent to the Holder via [DIDComm](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#didcomm). The
   Holder receives the credential, and the protocol is complete.

The claims provide specific information about the individual, such as their name or qualifications.

This protocol is applicable in various real-life scenarios, such as educational credentialing, employment verification,
and more.
In these scenarios, the Issuer could be a school, an employer, etc., and the Holder could be a student or an employee.
The VCs issued during this protocol could represent a diploma, a certificate of employment, etc.

## Endpoints

| Endpoint                                                                                                                           | Description                                                                              | Role           |
|------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------|
| [`/issue-credentials/credential-offers`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/createCredentialOffer)               | This endpoint allows you to create a new credential offer                                | Issuer         |
| [`/issue-credentials/records`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/getCredentialRecords)                          | This endpoint allows you to retrieve a collection of all the existing credential records | Issuer, Holder |
| [`/issue-credentials/records/{recordId}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/getCredentialRecord)                | This endpoint allows you to retrieve a specific credential record by its `id`            | Issuer, Holder |
| [`/issue-credentials/records/{recordId}/accept-offer`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/acceptCredentialOffer) | This endpoint allows you to accept a credential offer                                    | Holder         |
| [`/issue-credentials/records/{recordId}/issue-credential`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/issueCredential)   | This endpoint allows you to issue a VC for a specific credential record.                 | Issuer         |

:::info
Please check the full [Cloud agent API](https://hyperledger-identus.github.io/docs/agent-api) specification for more detailed information.
:::

## Issuer interactions

This section describes the Issuer role's available interactions with the Cloud agent.

### Creating a Credential Offer

To start the process, the issuer needs to create a credential offer.
To do this, make a `POST` request to
the [`/issue-credentials/credential-offers`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/createCredentialOffer)
endpoint with a JSON payload that includes the following information:

<Tabs groupId="vc-formats">
<TabItem value="jwt" label="JWT">
**NOTE**: After v1.40.0 the API was changed to be compliant with VCDM 1.1 and group the JWT-VC properties in the object `jwtVcPropertiesV1`

1. `claims` (deprecated): The data stored in a verifiable credential. Claims get expressed in a key-value format. The
   claims contain the data that the issuer attests to, such as name, address, date of birth, and so on.
2. `jwtVcPropertiesV1.claims`: The data stored in a verifiable credential. Claims get expressed in a key-value format.
   The claims contain the data that the issuer attests to, such as name, address, date of birth, and so on.
3. `issuingDID` (deprecated): The DID referring to the issuer to issue this credential from.
4. `jwtVcPropertiesV1.issuingDID`: The DID referring to the issuer to issue this credential from.
5. `jwtVcPropertiesV1.issuingKid`: The key ID of the DID to sign the JWT VC.
6. `jwtVcPropertiesV1.validityPeriod`: The validity period of the JWT VC in seconds.
7. `connectionId`: The unique ID of the connection between the holder and the issuer to offer this credential over.
8. `schemaId` (deprecated): An optional field that, if specified, contains a valid URL to an existing VC schema.
9. `jwtVcPropertiesV1.credentialSchema.id`: The required field that, if specified, contains a valid URI to an existing
   VC schema.
10. `jwtVcPropertiesV1.credentialSchema.type`: The required field that must equal to `JsonSchemaValidator2018`
11. `credentialFormat`: The format of the credential that will be issued - `JWT` in this case. When not specified, the
    default value is `JWT`.

:::note
The `issuingDID` and `connectionId` properties come from completing the pre-requisite steps listed above
:::

Once the request initiates, a new credential record for the issuer gets created with a unique ID. The state of this
record is now `OfferPending`.

<Tabs groupId="api-versions">
<TabItem value="deprecated" label="Deprecated API">

```shell
# Issuer POST request to create a new credential offer (deprecated, but still supported)
curl -X 'POST' \
  'http://localhost:8080/cloud-agent/issue-credentials/credential-offers' \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -H "apikey: $API_KEY" \
    -d '{
          "connectionId": "9d075518-f97e-4f11-9d10-d7348a7a0fda",
          "credentialFormat": "JWT",
          "claims": {
            "emailAddress": "alice@wonderland.com",
            "givenName": "Alice",
            "familyName": "Wonderland",
            "dateOfIssuance": "2020-11-13T20:20:39+00:00",
            "drivingLicenseID": "12345",
            "drivingClass": 3
          },
          "issuingDID": "did:prism:9f847f8bbb66c112f71d08ab39930d468ccbfe1e0e1d002be53d46c431212c26",
          "schemaId": "http://localhost:8080/cloud-agent/schema-registry/schemas/3f86a73f-5b78-39c7-af77-0c16123fa9c2"
        }'
````

```shell
# Issuer POST request to create a new credential offer
curl -X 'POST' \
  'http://localhost:8080/cloud-agent/issue-credentials/credential-offers' \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -H "apikey: $API_KEY" \
    -d '{
          "connectionId": "9d075518-f97e-4f11-9d10-d7348a7a0fda",
          "credentialFormat": "JWT"
          "jwtVcPropertiesV1": {
            "claims": {
               "emailAddress": "alice@wonderland.com",
               "givenName": "Alice",
               "familyName": "Wonderland",
               "dateOfIssuance": "2020-11-13T20:20:39+00:00",
               "drivingLicenseID": "12345",
               "drivingClass": 3
             },
             "issuingDID": "did:prism:9f847f8bbb66c112f71d08ab39930d468ccbfe1e0e1d002be53d46c431212c26",
             "credentialSchema": {
                "id: "http://localhost:8080/cloud-agent/schema-registry/schemas/3f86a73f-5b78-39c7-af77-0c16123fa9c2",
                "type": "JsonSchemaValidator2018"
             }
          }
        }'
```

1. `connectionId`: The unique ID of the connection between the holder and the issuer to offer this credential over.  
2. `credentialFormat`: The format of the credential that will be issued \- `AnonCreds` in this case.  
3. `claims` (deprecated): The data stored in a verifiable credential.  
4. `anoncredsVcPropertiesV1.claims`: The data stored in a verifiable credential. AnonCreds claims get expressed in a flat, "string \-\> string", key-value pair format. The claims contain the data that the issuer attests to, such as name, address, date of birth, and so on.  
5. `credentialDefinitionId` (deprecated): The unique ID of the [credential definition](https://hyperledger-identus.github.io/docs/credentialdefinition/credential-definition.md) that has been created by the issuer as a prerequisite. Please refer to the [Create AnonCreds Credential Definition](https://hyperledger-identus.github.io/docs/credentialdefinition/credential-definition.md) doc for details on how to create a credential definition.  
6. `anoncredsVcPropertiesV1.credentialDefinitionId`: The unique ID of the credential definition. :::note If the credential definition was created via HTTP URL endpoint, then this credential definition will be referenced to that credential via HTTP URL, and if this credential definition was created via DID URL endpoint, then it will be referenced via DID URL, How to create credential definition for HTTP URL or DID URL is explained in [credential definition creation guide](https://hyperledger-identus.github.io/docs/credentialdefinition/create.md) :::  
7. `issuingDID` (deprecated): The DID referring to the issuer to issue this credential from  
8. `anoncredsVcPropertiesV1.issuingDID`: The DID referring to the issuer to issue this credential from :::note The `connectionId` and `credentialDefinitionId` properties come from completing the pre-requisite steps listed above :::

Once the request initiates, a new credential record for the issuer gets created with a unique ID. The state of this record is now `OfferPending`.

```shell
# Issuer POST request to create a new credential offer
curl -X 'POST' \
  'http://localhost:8080/cloud-agent/issue-credentials/credential-offers' \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -H "apikey: $API_KEY" \
    -d '{
          "connectionId": "9d075518-f97e-4f11-9d10-d7348a7a0fda",
          "credentialFormat": "AnonCreds",
          "claims": {
            "emailAddress": "alice@wonderland.com",
            "givenName": "Alice",
            "familyName": "Wonderland",
            "dateOfIssuance": "2020-11-13T20:20:39+00:00",
            "drivingLicenseID": "12345",
            "drivingClass": "3"
          },
          "issuingDID": "did:prism:9f847f8bbb66c112f71d08ab39930d468ccbfe1e0e1d002be53d46c431212c26",
          "credentialDefinitionId": "5d737816-8fe8-3492-bfe3-1b3e2b67220b"
        }'
```

```shell
# Issuer POST request to create a new credential offer
curl -X 'POST' \
  'http://localhost:8080/cloud-agent/issue-credentials/credential-offers' \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -H "apikey: $API_KEY" \
    -d '{
          "connectionId": "9d075518-f97e-4f11-9d10-d7348a7a0fda",
          "credentialFormat": "AnonCreds",
          "anoncredsVcPropertiesV1": {
             "claims": {
               "emailAddress": "alice@wonderland.com",
               "givenName": "Alice",
               "familyName": "Wonderland",
               "dateOfIssuance": "2020-11-13T20:20:39+00:00",
               "drivingLicenseID": "12345",
               "drivingClass": "3"
             },
             "issuingDID": "did:prism:9f847f8bbb66c112f71d08ab39930d468ccbfe1e0e1d002be53d46c431212c26",
             "credentialDefinitionId": "5d737816-8fe8-3492-bfe3-1b3e2b67220b"
          }
        }'
```

1. `connectionId`: The unique ID of the connection between the holder and the issuer to offer this credential over.  
2. `credentialFormat`: The format of the credential that will be issued \- `SDJWT` in this case.  
3. `claims` (deprecated): The data stored in a verifiable credential. Claims get expressed in a key-value format. The claims contain the data that the issuer attests to, such as name, address, date of birth, and so on.  
4. `sdJwtVcPropertiesV1.claims`: The data stored in a verifiable credential.  
5. `issuingDID` (deprecated): The DID referring to the issuer to issue this credential from  
6. `sdJwtVcPropertiesV1.issuingDID`: The DID referring to the issuer to issue this credential from  
7. `schemaId` (deprecated): An optional field that, if specified, contains a valid URL to an existing VC schema.  
8. `sdJwtVcPropertiesV1.credentialSchema.id`: The required field that, if specified, contains a valid URI to an existing VC schema.  
9. `sdJwtVcPropertiesV1.credentialSchema.type`: The required field that must equal to `JsonSchemaValidator2018`

:::note The `issuingDID` and `connectionId` properties come from completing the pre-requisite steps listed above :::

:::note Claims can also include the `exp` Expiration Time attribute, which is part of JWT claims. `exp` attribute is disclosable if specified and can have a value in epoch time (in seconds), indicating when the SDJWT credential expires for more details [RFC5719](https://datatracker.ietf.org/doc/html/rfc7519#page-9) ::: Once the request initiates, a new credential record for the issuer gets created with a unique ID. The state of this record is now `OfferPending`.

```shell
# Issuer POST request to create a new credential offer
curl -X 'POST' \
  'http://localhost:8080/cloud-agent/issue-credentials/credential-offers' \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -H "apikey: $API_KEY" \
    -d '{
          "connectionId": "9d075518-f97e-4f11-9d10-d7348a7a0fda",
          "credentialFormat": "SDJWT",
          "claims": {
            "emailAddress": "alice@wonderland.com",
            "givenName": "Alice",
            "familyName": "Wonderland",
            "dateOfIssuance": "2020-11-13T20:20:39+00:00",
            "drivingLicenseID": "12345",
            "drivingClass": 3,
            "exp" : 1883000000
          },
          "issuingDID": "did:prism:9f847f8bbb66c112f71d08ab39930d468ccbfe1e0e1d002be53d46c431212c26",
          "schemaId": "http://localhost:8080/cloud-agent/schema-registry/schemas/3f86a73f-5b78-39c7-af77-0c16123fa9c2"
        }'
```

```shell
# Issuer POST request to create a new credential offer
curl -X 'POST' \
  'http://localhost:8080/cloud-agent/issue-credentials/credential-offers' \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -H "apikey: $API_KEY" \
    -d '{
          "connectionId": "9d075518-f97e-4f11-9d10-d7348a7a0fda",
          "credentialFormat": "SDJWT",
          "sdJwtVcPropertiesV1": {
             "claims": {
               "emailAddress": "alice@wonderland.com",
               "givenName": "Alice",
               "familyName": "Wonderland",
               "dateOfIssuance": "2020-11-13T20:20:39+00:00",
               "drivingLicenseID": "12345",
               "drivingClass": 3,
               "exp" : 1883000000
             },
             "issuingDID": "did:prism:9f847f8bbb66c112f71d08ab39930d468ccbfe1e0e1d002be53d46c431212c26",
             "credentialSchema": {
                "id: "http://localhost:8080/cloud-agent/schema-registry/schemas/3f86a73f-5b78-39c7-af77-0c16123fa9c2",
                "type": "JsonSchemaValidator2018"
             }
          }
        }'
```

### Sending the Offer to the Holder

The next step for the Issuer is to send the offer to the holder using DIDComm. To do this, the Issuer agent will process the offer and send it to the holder agent. This process is automatic. The state of the Issuer's record will change to `OfferSent`.

### Issuing the Credential

Once the holder has approved the offer and sent a request to the Issuer, the Issuer will receive the request via DIDComm and update the record state to `RequestReceived.`

The Issuer can then use the [`/issue-credentials/records/{recordId}/issue-credential`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/issueCredential) endpoint to issue the credential to the holder.

```shell
# Issuer POST request to issue the credential
# make sure you have `issuer_record_id` extracted from created credential offer
# and the record achieved `RequestReceived` state
curl -X POST \
    "http://localhost:8080/cloud-agent/issue-credentials/records/$issuer_record_id/issue-credential" \
    -H "Content-Type: application/json" \
    -H "apikey: $API_KEY"
```

When this endpoint gets called, the state of the record will change to `CredentialPending,` and after processing, it will change to `CredentialGenerated.`

Finally, the Issuer agent will send the credential to the holder via DIDComm, and the state of the record will change to `CredentialSent`. At this point, the Issuer's interactions with the holder are complete.

```
---
title: Issuer flow
---
stateDiagram-v2
  [*] --> OfferPending: create credential offer (`/issue-credentials/credential-offers`)
  OfferPending --> OfferSent: send offer (auto via Cloud agent DIDComm)
  OfferSent --> RequestReceived: receive request (auto via Cloud agent DIDComm)
  RequestReceived --> CredentialPending: issue credential (`/issue-credentials/records/{recordId}/issue-credential`)
  CredentialPending --> CredentialGenerated: process issued credential (auto via Cloud agent)
  CredentialGenerated --> CredentialSent: send credential (auto via Cloud agent)
```

## Holder interactions

This section describes the Holder role's available interactions with the Cloud agent.

### Receiving the VC Offer

The Holder will receive the offer from the Issuer via DIDComm, and a new credential record with a unique ID gets created in the `OfferReceived` state.

This process is automatic for the Cloud agent.

You could check if a new credential offer is available using [`/issue-credentials/records`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/getCredentialRecords) request and check for any records available in `OfferReceived` state:

```shell
# Holder GET request to retrieve credential records
curl "http://localhost:8090/cloud-agent/issue-credentials/records" \
    -H "Content-Type: application/json" \
    -H "apikey: $API_KEY"
```

### Approving the VC Offer

To accept the offer, the Holder can make a `POST` request to the [`/issue-credentials/records/{recordId}/accept-offer`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/acceptCredentialOffer) endpoint with a JSON payload that includes the following information:

1. `holder_record_id`: The unique identifier of the issue credential record known by the holder's Cloud agent.  
2. `subjectId`: This field represents the unique identifier for the subject of the verifiable credential. It is a short-form PRISM [DID](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#decentralized-identifier) string, such as `did:prism:subjectIdentifier`.

```shell
# Holder POST request to accept the credential offer
curl -X POST "http://localhost:8090/cloud-agent/issue-credentials/records/$holder_record_id/accept-offer" \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -H "apikey: $API_KEY" \
    -d '{
          "subjectId": "did:prism:subjectIdentifier"
     }'
```

1. `holder_record_id`: The unique identifier of the issue credential record known by the holder's Cloud agent.

```shell
# Holder POST request to accept the credential offer
curl -X POST "http://localhost:8090/cloud-agent/issue-credentials/records/$holder_record_id/accept-offer" \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -H "apikey: $API_KEY" \
    -d '{}'
```

1. `holder_record_id`: The unique identifier of the issue credential record known by the holder's Cloud agent.  
     
2. `subjectId`: This field represents the unique identifier for the subject of the verifiable credential. It is a short-form PRISM [DID](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#decentralized-identifier) string, such as `did:prism:subjectIdentifier`.  
     
3. `keyId` Option parameter  
     
   1. when keyId is not provided the SDJWT VC is not binded to Holder/Prover key

```shell
# Holder POST request to accept the credential offer
curl -X POST "http://localhost:8090/cloud-agent/issue-credentials/records/$holder_record_id/accept-offer" \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -H "apikey: $API_KEY" \
    -d '{
          "subjectId": "did:prism:subjectIdentifier"
     }'
```

   A SD-JWT Verifiable Credential (VC) without a `cnf` key could possibly look like below

```json
 {
  "_sd": [
    "CrQe7S5kqBAHt-nMYXgc6bdt2SH5aTY1sU_M-PgkjPI",
    "JzYjH4svliH0R3PyEMfeZu6Jt69u5qehZo7F7EPYlSE",
    "PorFbpKuVu6xymJagvkFsFXAbRoc2JGlAUA2BA4o7cI",
    "TGf4oLbgwd5JQaHyKVQZU9UdGE0w5rtDsrZzfUaomLo",
    "XQ_3kPKt1XyX7KANkqVR6yZ2Va5NrPIvPYbyMvRKBMM",
    "XzFrzwscM6Gn6CJDc6vVK8BkMnfG8vOSKfpPIZdAfdE",
    "gbOsI4Edq2x2Kw-w5wPEzakob9hV1cRD0ATN3oQL9JM",
    "jsu9yVulwQQlhFlM_3JlzMaSFzglhQG0DpfayQwLUK4"
  ],
  "iss": "https://issuer.example.com",
  "iat": 1683000000,
  "exp": 1883000000,
  "sub": "user_42",
  "_sd_alg": "sha-256"
}
```

   2. `keyId`: This is optional field but must be specified to choose which key bounds to the verifiable credential. For more information on key-binding, [ietf-oauth-selective-disclosure-jwt](https://datatracker.ietf.org/doc/draft-ietf-oauth-selective-disclosure-jwt). Currently, we only support the EdDSA algorithm and curve Ed25519. The specified keyId should be of type Ed25519. The purpose of the keyId should be authentication.

```shell
# Holder POST request to accept the credential offer with keyId
curl -X POST "http://localhost:8090/cloud-agent/issue-credentials/records/$holder_record_id/accept-offer" \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -H "apikey: $API_KEY" \
    -d '{
          "subjectId": "did:prism:subjectIdentifier",
          "keyId": "key-1"    
     }'
```

   A SD-JWT Verifiable Credential (VC) that includes a `cnf` key could possibly look like below

```json
 {
  "_sd": [
    "CrQe7S5kqBAHt-nMYXgc6bdt2SH5aTY1sU_M-PgkjPI",
    "JzYjH4svliH0R3PyEMfeZu6Jt69u5qehZo7F7EPYlSE",
    "PorFbpKuVu6xymJagvkFsFXAbRoc2JGlAUA2BA4o7cI",
    "TGf4oLbgwd5JQaHyKVQZU9UdGE0w5rtDsrZzfUaomLo",
    "XQ_3kPKt1XyX7KANkqVR6yZ2Va5NrPIvPYbyMvRKBMM",
    "XzFrzwscM6Gn6CJDc6vVK8BkMnfG8vOSKfpPIZdAfdE",
    "gbOsI4Edq2x2Kw-w5wPEzakob9hV1cRD0ATN3oQL9JM",
    "jsu9yVulwQQlhFlM_3JlzMaSFzglhQG0DpfayQwLUK4"
  ],
  "iss": "https://issuer.example.com",
  "iat": 1683000000,
  "exp": 1883000000,
  "sub": "user_42",
  "_sd_alg": "sha-256",
  "cnf": {
    "jwk": {
      "kty": "EC",
      "crv": "P-256",
      "x": "TCAER19Zvu3OHF4j4W4vfSVoHIP1ILilDls7vCeGemc",
      "y": "ZxjiWWbZMQGHVWKVQ4hbSIirsVfuecCE6t4jT9F2HZQ"
    }
  }
}
```

This request will change the state of the record to `RequestPending`.

### Receiving the VC Credential

Once the Holder has approved the offer and sent a request to the Issuer, the Holder agent will process the request and send it to the Issuer agent. The state of the Holder's record will change to `RequestSent`.

After the Issuer has issued the credential, the Holder will receive the credential via DIDComm, and the state of the Holder's record will change to `CredentialReceived`. This process is automatic for the Cloud agent.

The Holder can check the achieved credential using a GET request to [`/issue-credentials/records/{recordId}/`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/getCredentialRecord) endpoint.

```
---
title: Holder Flow
---
stateDiagram-v2
  [*] --> OfferReceived: receive offer (auto via Cloud agent)
  OfferReceived --> RequestPending: accept offer (`/issue-credentials/records/{recordId}/accept-offer`)
  RequestPending --> RequestSent: send request (auto via Cloud agent)
  RequestSent --> CredentialReceived: receive credential (auto via Cloud agent)
```

## Sequence diagram

The following diagram shows the end-to-end flow for an issuer to issue a VC to a holder.

---

## Issuing credentials (connectionless)

In the Identus Platform, the [Issue Credentials Protocol](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#issue-credential-protocol) allows you to create, retrieve, and manage issued [verifiable credentials (VCs)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifiable-credentials) between a VC issuer and a VC holder.

## Roles

In the Issue Credentials Protocol, there are two roles:

1. The [Issuer](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#issuer) is responsible for creating a new credential offer, sending it to a Holder, and issuing the VC once the offer is accepted.  
2. The [Holder](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#holder) is responsible for accepting a credential offer from an issuer and receiving the VC.

The Issuer and Holder interact with the Identus Cloud agent API to perform the operations defined in the protocol.

## Prerequisites

Before using the "Connectionless" Issuing Credentials protocol, the following conditions must be present:

1. Issuer Cloud agents is up and running  
2. The Issuer has a published PRISM DID, and the [DID document](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-document) must have at least one `assertionMethod` key for issuing credentials (see [Create DID](https://hyperledger-identus.github.io/docs/dids/create.md) and [Publish DID](https://hyperledger-identus.github.io/docs/dids/publish.md))  
3. The Holder is either another Cloud agent or Edge Agent SDK  
1. Issuer Cloud agents is up and running  
2. The Issuer has a published PRISM DID, and the [DID document](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-document) must have at least one `assertionMethod` key for issuing credentials (see [Create DID](https://hyperledger-identus.github.io/docs/dids/create.md) and [Publish DID](https://hyperledger-identus.github.io/docs/dids/publish.md))  
3. The Issuer must have created an AnonCreds Credential Definition as described in \[https://hyperledger-identus.github.io/docs/credentialdefinition/create.md).  
4. The Holder is either another Cloud agent or Edge Agent SDK  
- 📌 **Note:** Currently we only support `Ed25519` curve  
1. Issuer Cloud agents is up and running  
2. The Issuer has a published PRISM DID, and the [DID document](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#did-document) must have at least one `assertionMethod` key for issuing credentials (see [Create DID](https://hyperledger-identus.github.io/docs/dids/create.md) and [Publish DID](https://hyperledger-identus.github.io/docs/dids/publish.md))  
3. The Holder is either another Cloud agent or Edge Agent SDK  
4. The Holder must have a PRISM DID, and the DID document must have at least one `authentication` key for presenting the proof and the curve must be `Ed25519`.

## Overview

The protocol described is a VC issuance process between an Issuer (Identus Cloud agent) and Holder (Identus Edge Agent SDK).

The protocol consists of the following main parts:

1. The Issuer creates a new credential offer using the [`/issue-credentials/credential-offers/invitation`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/createCredentialOffer) endpoint, which includes information such as the schema identifier and claims. This returns a unique OOB (out-of-band) invitate code for the prospective Holder.  
2. The Holder accepts the OOB invite (see SDK `acceptInvitation`)  
3. The Issuer responds by sending the actual Credential Offer  
4. The Holder accepts the Credential Offer  
5. The Issuer sends the Verifiable Credential

The claims provide specific information about the individual, such as their name or qualifications.

This protocol is applicable in various real-life scenarios, such as educational credentialing, employment verification, and more. In these scenarios, the Issuer could be a school, an employer, etc., and the Holder could be a student or an employee. The VCs issued during this protocol could represent a diploma, a certificate of employment, etc.

## Endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| [`/issue-credentials/credential-offers/invitation`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/createCredentialOffer) | This endpoint allows you to create a new credential offer invitation | Issuer |
| [`/issue-credentials/credential-offers/accept-invitation`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/acceptCredentialOfferInvitation) | This endpoint allows you to accept the invitation | Holder |
| [`/issue-credentials/records`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/getCredentialRecords) | This endpoint allows you to retrieve a collection of all the existing credential records | Issuer, Holder |
| [`/issue-credentials/records/{recordId}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/getCredentialRecord) | This endpoint allows you to retrieve a specific credential record by its `id` | Issuer, Holder |
| [`/issue-credentials/records/{recordId}/accept-offer`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/acceptCredentialOffer) | This endpoint allows you to accept a credential offer | Holder |
| [`/issue-credentials/records/{recordId}/issue-credential`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/issueCredential) | This endpoint allows you to issue a VC for a specific credential record. | Issuer |

:::info Please check the full [Cloud agent API](https://hyperledger-identus.github.io/docs/agent-api) specification for more detailed information. :::

## Issuer interactions

This section describes the Issuer role's available interactions with the Cloud agent.

### Creating a Credential Offer

To start the process, the issuer needs to create a credential offer invitation. To do this, make a `POST` request to the [`/issue-credentials/credential-offers/invitation`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/createCredentialOffer) endpoint with a JSON payload that includes the following information:

1. `claims`: The data stored in a verifiable credential. Claims get expressed in a key-value format. The claims contain the data that the issuer attests to, such as name, address, date of birth, and so on.  
2. `issuingDID`: The DID referring to the issuer to issue this credential from  
3. `schemaId`: An optional field that, if specified, contains a valid URL to an existing VC schema. The Cloud agent must be able to dereference the specified URL (i.e. fetch the VC schema content from it), in order to validate the provided claims against it. When not specified, the claims fields is not validated and can be any valid JSON object. Please refer to the [Create VC schema](https://hyperledger-identus.github.io/docs/schemas/create.md) doc for details on how to create a VC schema.  
4. `credentialFormat`: The format of the credential that will be issued \- `JWT` in this case. When not specified, the default value is `JWT`.

:::note The `issuingDID` property comes from completing the pre-requisite steps listed above :::

Once the request initiates, a new credential record for the issuer gets created with a unique ID. The state of this record is now `OfferPending`.

```shell
# Issuer POST request to create a new credential offer
curl -X 'POST' \
  'http://localhost:8080/cloud-agent/issue-credentials/credential-offers/invitation' \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -H "apikey: $API_KEY" \
    -d '{
          "claims": {
            "emailAddress": "alice@wonderland.com",
            "givenName": "Alice",
            "familyName": "Wonderland",
            "dateOfIssuance": "2020-11-13T20:20:39+00:00",
            "drivingLicenseID": "12345",
            "drivingClass": 3
          },
          "credentialFormat": "JWT",
          "issuingDID": "did:prism:9f847f8bbb66c112f71d08ab39930d468ccbfe1e0e1d002be53d46c431212c26",
          "schemaId": "http://localhost:8080/cloud-agent/schema-registry/schemas/3f86a73f-5b78-39c7-af77-0c16123fa9c2"
        }'
```

1. `claims`: The data stored in a verifiable credential. AnonCreds claims get expressed in a flat, "string \-\> string", key-value pair format. The claims contain the data that the issuer attests to, such as name, address, date of birth, and so on.  
2. `issuingDID`: The DID referring to the issuer to issue this credential from  
3. `credentialDefinitionId`: The unique ID of the [credential definition](https://hyperledger-identus.github.io/docs/credentialdefinition/credential-definition.md) that has been created by the issuer as a prerequisite. Please refer to the [Create AnonCreds Credential Definition](https://hyperledger-identus.github.io/docs/credentialdefinition/credential-definition.md) doc for details on how to create a credential definition. :::note 📌 Note: If the credential definition was created via HTTP URL endpoint, then this credential definition will be referenced to that credential via HTTP URL, and if this credential definition was created via DID URL endpoint, then it will be referenced via DID URL, How to create credential definition for HTTP URL or DID URL is explained in [credential definition creation guide](https://hyperledger-identus.github.io/docs/credentialdefinition/create.md) :::  
4. `credentialFormat`: The format of the credential that will be issued \- `AnonCreds` in this case.

:::note The `issuingDID` and `credentialDefinitionId` properties come from completing the pre-requisite steps listed above :::

Once the request initiates, a new credential record for the issuer gets created with a unique ID. The state of this record is now `OfferPending`.

```shell
# Issuer POST request to create a new credential offer
curl -X 'POST' \
  'http://localhost:8080/cloud-agent/issue-credentials/credential-offers/invitation' \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -H "apikey: $API_KEY" \
    -d '{
          "claims": {
            "emailAddress": "alice@wonderland.com",
            "givenName": "Alice",
            "familyName": "Wonderland",
            "dateOfIssuance": "2020-11-13T20:20:39+00:00",
            "drivingLicenseID": "12345",
            "drivingClass": "3"
          },
          "credentialFormat": "AnonCreds",
          "issuingDID": "did:prism:9f847f8bbb66c112f71d08ab39930d468ccbfe1e0e1d002be53d46c431212c26",
          "credentialDefinitionId": "5d737816-8fe8-3492-bfe3-1b3e2b67220b"
        }'
```

1. `claims`: The data stored in a verifiable credential. Claims get expressed in a key-value format. The claims contain the data that the issuer attests to, such as name, address, date of birth, and so on.  
2. `issuingDID`: The DID referring to the issuer to issue this credential from  
3. `schemaId`: An optional field that, if specified, contains a valid URL to an existing VC schema. The Cloud agent must be able to dereference the specified URL (i.e. fetch the VC schema content from it), in order to validate the provided claims against it. When not specified, the claims fields is not validated and can be any valid JSON object. Please refer to the [Create VC schema](https://hyperledger-identus.github.io/docs/schemas/create.md) doc for details on how to create a VC schema.  
4. `credentialFormat`: The format of the credential that will be issued \- `SDJWT` in this case.

:::note The `issuingDID` property comes from completing the pre-requisite steps listed above :::

- 📌 **Note:** Claims can also include the `exp` Expiration Time attribute, which is part of JWT claims. `exp` attribute is disclosable if specified and can have a value in epoch time (in seconds), indicating when the SDJWT credential expires for more details [RFC5719](https://datatracker.ietf.org/doc/html/rfc7519#page-9)

Once the request initiates, a new credential record for the issuer gets created with a unique ID. The state of this record is now `OfferPending`.

```shell
# Issuer POST request to create a new credential offer
curl -X 'POST' \
  'http://localhost:8080/cloud-agent/issue-credentials/credential-offers/invitation' \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -H "apikey: $API_KEY" \
    -d '{
          "claims": {
            "emailAddress": "alice@wonderland.com",
            "givenName": "Alice",
            "familyName": "Wonderland",
            "dateOfIssuance": "2020-11-13T20:20:39+00:00",
            "drivingLicenseID": "12345",
            "drivingClass": 3,
            "exp" : 1883000000
          },
          "credentialFormat": "SDJWT",
          "issuingDID": "did:prism:9f847f8bbb66c112f71d08ab39930d468ccbfe1e0e1d002be53d46c431212c26",
          "schemaId": "http://localhost:8080/cloud-agent/schema-registry/schemas/3f86a73f-5b78-39c7-af77-0c16123fa9c2"
        }'
```

### Sending the Offer to the Holder

The next step for the Issuer is to send the OOB invite Holder (by definition, this is "out of band", so not handled by Identus). Common ways to convey such OOB invites might be a QR code that is scanned, or via an existing channel of connection in an application.

### Issuing the Credential

Once the holder has approved the offer and sent a request to the Issuer, the Issuer will receive the request via DIDComm and update the record state to `RequestReceived.`

The Issuer can then use the [`/issue-credentials/records/{recordId}/issue-credential`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/issueCredential) endpoint to issue the credential to the holder.

```shell
# Issuer POST request to issue the credential
# make sure you have `issuer_record_id` extracted from created credential offer
# and the record achieved `RequestReceived` state
curl -X POST \
    "http://localhost:8080/cloud-agent/issue-credentials/records/$issuer_record_id/issue-credential" \
    -H "Content-Type: application/json" \
    -H "apikey: $API_KEY"
```

When this endpoint gets called, the state of the record will change to `CredentialPending,` and after processing, it will change to `CredentialGenerated.`

Finally, the Issuer agent will send the credential to the holder via DIDComm, and the state of the record will change to `CredentialSent`. At this point, the Issuer's interactions with the holder are complete.

```
---
title: Issuer flow
---
stateDiagram-v2
  [*] --> OfferPending: create credential offer (`/issue-credentials/credential-offers`)
  OfferPending --> OfferSent: send offer (auto via PRISM Agent DIDComm)
  OfferSent --> RequestReceived: receive request (auto via PRISM Agent DIDComm)
  RequestReceived --> CredentialPending: issue credential (`/issue-credentials/records/{recordId}/issue-credential`)
  CredentialPending --> CredentialGenerated: process issued credential (auto via PRISM Agent)
  CredentialGenerated --> CredentialSent: send credential (auto via PRISM Agent)
```

## Holder interactions

This section describes the Holder role's available interactions with the Cloud agent.

### Receiving the VC Offer

The Holder will receive the offer from the Issuer via DIDComm, and a new credential record with a unique ID gets created in the `OfferReceived` state.

This process is automatic for the Cloud agent.

You could check if a new credential offer is available using [`/issue-credentials/records`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/getCredentialRecords) request and check for any records available in `OfferReceived` state:

```shell
# Holder GET request to retrieve credential records
curl "http://localhost:8090/cloud-agent/issue-credentials/records" \
    -H "Content-Type: application/json" \
    -H "apikey: $API_KEY"
```

### Approving the VC Offer

To accept the offer, the Holder can make a `POST` request to the [`/issue-credentials/records/{recordId}/accept-offer`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/acceptCredentialOffer) endpoint with a JSON payload that includes the following information:

1. `holder_record_id`: The unique identifier of the issue credential record known by the holder's Cloud agent.  
2. `subjectId`: This field represents the unique identifier for the subject of the verifiable credential. It is a short-form PRISM [DID](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#decentralized-identifier) string, such as `did:prism:subjectIdentifier`.

```shell
# Holder POST request to accept the credential offer
curl -X POST "http://localhost:8090/cloud-agent/issue-credentials/records/$holder_record_id/accept-offer" \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -H "apikey: $API_KEY" \
    -d '{
          "subjectId": "did:prism:subjectIdentifier"
     }'
```

1. `holder_record_id`: The unique identifier of the issue credential record known by the holder's Cloud agent.

```shell
# Holder POST request to accept the credential offer
curl -X POST "http://localhost:8090/cloud-agent/issue-credentials/records/$holder_record_id/accept-offer" \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -H "apikey: $API_KEY" \
    -d '{}'
```

1. `holder_record_id`: The unique identifier of the issue credential record known by the holder's Cloud agent.  
     
2. `subjectId`: This field represents the unique identifier for the subject of the verifiable credential. It is a short-form PRISM [DID](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#decentralized-identifier) string, such as `did:prism:subjectIdentifier`.  
     
3. `keyId` Option parameter  
     
   1. when keyId is not provided the SDJWT VC is not binded to Holder/Prover key

```shell
# Holder POST request to accept the credential offer
curl -X POST "http://localhost:8090/cloud-agent/issue-credentials/records/$holder_record_id/accept-offer" \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -H "apikey: $API_KEY" \
    -d '{
          "subjectId": "did:prism:subjectIdentifier"
     }'
```

   A SD-JWT Verifiable Credential (VC) without a `cnf` key could possibly look like below

```json
 {
  "_sd": [
    "CrQe7S5kqBAHt-nMYXgc6bdt2SH5aTY1sU_M-PgkjPI",
    "JzYjH4svliH0R3PyEMfeZu6Jt69u5qehZo7F7EPYlSE",
    "PorFbpKuVu6xymJagvkFsFXAbRoc2JGlAUA2BA4o7cI",
    "TGf4oLbgwd5JQaHyKVQZU9UdGE0w5rtDsrZzfUaomLo",
    "XQ_3kPKt1XyX7KANkqVR6yZ2Va5NrPIvPYbyMvRKBMM",
    "XzFrzwscM6Gn6CJDc6vVK8BkMnfG8vOSKfpPIZdAfdE",
    "gbOsI4Edq2x2Kw-w5wPEzakob9hV1cRD0ATN3oQL9JM",
    "jsu9yVulwQQlhFlM_3JlzMaSFzglhQG0DpfayQwLUK4"
  ],
  "iss": "https://issuer.example.com",
  "iat": 1683000000,
  "exp": 1883000000,
  "sub": "user_42",
  "_sd_alg": "sha-256"
}
```

   2. `keyId`: This is optional field but must be specified to choose which key bounds to the verifiable credential. For more information on key-binding, [ietf-oauth-selective-disclosure-jwt](https://datatracker.ietf.org/doc/draft-ietf-oauth-selective-disclosure-jwt). Currently, we only support the EdDSA algorithm and curve Ed25519. The specified keyId should be of type Ed25519. The purpose of the keyId should be authentication.

```shell
# Holder POST request to accept the credential offer with keyId
curl -X POST "http://localhost:8090/cloud-agent/issue-credentials/records/$holder_record_id/accept-offer" \
    -H 'accept: application/json' \
    -H 'Content-Type: application/json' \
    -H "apikey: $API_KEY" \
    -d '{
          "subjectId": "did:prism:subjectIdentifier",
          "keyId": "key-1"    
     }'
```

   A SD-JWT Verifiable Credential (VC) that includes a `cnf` key could possibly look like below

```json
 {
  "_sd": [
    "CrQe7S5kqBAHt-nMYXgc6bdt2SH5aTY1sU_M-PgkjPI",
    "JzYjH4svliH0R3PyEMfeZu6Jt69u5qehZo7F7EPYlSE",
    "PorFbpKuVu6xymJagvkFsFXAbRoc2JGlAUA2BA4o7cI",
    "TGf4oLbgwd5JQaHyKVQZU9UdGE0w5rtDsrZzfUaomLo",
    "XQ_3kPKt1XyX7KANkqVR6yZ2Va5NrPIvPYbyMvRKBMM",
    "XzFrzwscM6Gn6CJDc6vVK8BkMnfG8vOSKfpPIZdAfdE",
    "gbOsI4Edq2x2Kw-w5wPEzakob9hV1cRD0ATN3oQL9JM",
    "jsu9yVulwQQlhFlM_3JlzMaSFzglhQG0DpfayQwLUK4"
  ],
  "iss": "https://issuer.example.com",
  "iat": 1683000000,
  "exp": 1883000000,
  "sub": "user_42",
  "_sd_alg": "sha-256",
  "cnf": {
    "jwk": {
      "kty": "EC",
      "crv": "P-256",
      "x": "TCAER19Zvu3OHF4j4W4vfSVoHIP1ILilDls7vCeGemc",
      "y": "ZxjiWWbZMQGHVWKVQ4hbSIirsVfuecCE6t4jT9F2HZQ"
    }
  }
}
```

This request will change the state of the record to `RequestPending`.

### Receiving the VC Credential

Once the Holder has approved the offer and sent a request to the Issuer, the Holder agent will process the request and send it to the Issuer agent. The state of the Holder's record will change to `RequestSent`.

After the Issuer has issued the credential, the Holder will receive the credential via DIDComm, and the state of the Holder's record will change to `CredentialReceived`. This process is automatic for the Cloud agent.

The Holder can check the achieved credential using a GET request to [`/issue-credentials/records/{recordId}/`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/getCredentialRecord) endpoint.

```
---
title: Holder Flow
---
stateDiagram-v2
  [*] --> OfferReceived: receive offer (auto via PRISM Agent)
  OfferReceived --> RequestPending: accept offer (`/issue-credentials/records/{recordId}/accept-offer`)
  RequestPending --> RequestSent: send request (auto via PRISM Agent)
  RequestSent --> CredentialReceived: receive credential (auto via PRISM Agent)
```

## Sequence diagram

TODO

---

## Issuing credentials (OID4VCI)

[OID4VCI](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#oid4vci) (OpenID for Verifiable Credential Issuance) is a protocol that extends OAuth2 to issue credentials. It involves a Credential Issuer server and an Authorization server working together, using the authorization and token endpoints on the Authorization Server to grant holders access to credentials on the Credential Issuer server. These servers may or may not be the same, depending on the implementation.

The Identus Cloud agent can act as a Credential Issuer server and integrate with any Authorization Server that follows the integration contract. The contract for the Authorization Server in the OID4VCI flow see the \[https://github.com/hyperledger-identus/cloud-agent/blob/main/docs/general/authserver-oid4vci-contract.md).

## Example: OID4VCI Authorization Code Issuance

Example is available in the \[https://github.com/hyperledger-identus/cloud-agent/tree/main/examples/st-oid4vci).

Following the instructions, the example demonstrates a single-tenant agent setup using an external Keycloak as the Issuer Authorization Server. The demo application walks through the authorization code issuance flow step-by-step.

**1\. Launching Local Example Stack**

```shell
docker-compose up
```

After running the `docker-compose up` command, all the containers should be running and initialized with the necessary configurations. The following logs should appear indicating that the stack is ready to execute the flow

```
   _   _   _        _ _
  | |_| |_| |_ _ __| | | ___
  | ' \  _|  _| '_ \_  _(_-<
  |_||_\__|\__| .__/ |_|/__/
              |_|
 2024-07-16_11:51:01.301 INFO  o.h.b.s.BlazeServerBuilder@L424:[ZScheduler-Worker-5] {} - http4s v0.23.23 on blaze v0.23.15 started at http://0.0.0.0:8085/

```

**2\. Building the demo application**

```shell
docker build -t identus-oid4vci-demo:latest ./demo
```

**3\. Running the demo application**

```shell
docker run --network <NETWORK_NAME> -it identus-oid4vci-demo:latest
```

The parameter `NETWORK_NAME` should be the same as the network name in docker-compose. This name can be discovered by running the `docker network ls` command.

The demo application acts as both issuer and Holder in the same script. See the source code for detailed steps on how to implement this flow. The demo application will interactively prompt the next step in the issuance flow. Keep continuing until this log appears asking the user to log in using the browser.

```
##############################

Open this link in the browser to login

http://localhost:9980/realms/students/protocol/openid-connect/auth?redirect_uri=.....

##############################

wating for authorization redirect ...
```

Open this URL in the browser. Enter `alice` for the username and `1234` for the password.

After a successful login, this log should appear indicating the demo application has received the credentials.

```
::::: Credential Received :::::
{
  "credential": "eyJ0eXAiOiJKV1QiLC...SK1vJK-fx6zjXw"
}
```

## Presenting proof (DIDComm)

The [Present Proof Protocol](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#present-proof-protocol) allows:

- a [Verifier](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifier) to request a verifiable credential presentation from a Holder/Prover  
- a [Holder/Prover](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#holder) responds by presenting a cryptographic proof to the Verifier

The protocol provides endpoints for a Verifier to request new proof presentations from Holder/Provers and for a Holder/Prover to respond to the presentation request using a specific verifiable credential they own.

## Roles

The present proof protocol has two roles:

1. Verifier: A subject requesting a proof presentation by sending a request presentation message, then verifying the presentation.  
2. Holder/Prover: A [subject](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#subject) that receives a [proof presentation](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#proof-presentation) request, prepares a proof, and sends it to the verifier by sending a proof presentation message.

## Prerequisites

Before using the Proof Presentation protocol, the following conditions must be present:

1. Holder/Prover and Verifier Cloud agents must be up and running  
2. A connection must be established between the Holder/Prover and Verifier Cloud agents (see [Connections](https://hyperledger-identus.github.io/docs/connections/connection.md))  
3. The Holder/Prover should hold a [verifiable credential (VC)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifiable-credential) received from an [Issuer](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#issuer) see [Issue](https://hyperledger-identus.github.io/docs/tutorials/issue.md).

## Overview

This protocol supports the presentation of verifiable claims between two Cloud agents, the Holder/Prover and the Verifier.

The protocol consists of the following main parts:

1. The Verifier creates a new proof presentation request using the [`/present-proof/presentations`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Present-Proof/operation/requestPresentation) endpoint. It includes the identifier of an existing `connection` between both parties, `domain`, and a `challenge` to protect from potential replay attacks.  
2. The Holder/Prover receives the presentation request from the Verifier and can retrieve the list of existing requests using the [`/present-proof/presentations`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Present-Proof/operation/getAllPresentation) endpoint.  
3. The Holder/Prover can then review and accept a specific request using the [`/present-proof/presentations/{presentationId}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Present-Proof/operation/updatePresentation) endpoint, providing the identifier of the `credential` record to use in the proof presentation.  
4. The Verifier receives the proof presentation from the Holder/Prover and can accept it using the [`/present-proof/presentations/{presentationId}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Present-Proof/operation/updatePresentation) endpoint, specifying `presentation-accept` as the action type.

## Endpoints

| Endpoint | Method | Description | Role |
| :---- | :---- | :---- | :---- |
| [`/present-proof/presentations`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Present-Proof/operation/requestPresentation) | POST | Creates and sends a new proof presentation request. | Verifier |
| [`/present-proof/presentations`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Present-Proof/operation/getAllPresentation) | GET | Retrieves the collection of all the existing presentation proof records \- sent or received. | Verifier, Holder/Prover |
| [`/present-proof/presentations/{id}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Present-Proof/operation/getPresentation) | GET | Retrieves a specific presentation proof record by `id`. | Verifier, Holder/Prover |
| [`/present-proof/presentations/{id}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Present-Proof/operation/updatePresentation) | PATCH | Updates an existing presentation proof record to, e.g., accept the request on the Holder/Prover side or accept the presentation on the Verifier side. | Verifier, Holder/Prover |

:::info For more detailed information, please, check the full [Cloud agent API](https://hyperledger-identus.github.io/docs/agent-api). :::

## Verifier interactions

This section describes the interactions available to the Verifier with the Cloud agent.

### Creating and sending a Presentation Request

The Verifier needs to create a proof presentation request to start the process. To do this, he makes a `POST` request to the [`/present-proof/presentations`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Present-Proof/operation/requestPresentation) endpoint with a JSON payload that includes the following information:

1. `connectionId`: This field represents the unique identifier of an existing connection between the verifier and the Holder/prover. It is for exchanging messages related to the protocol flow execution.  
2. `challenge` and `domain`: The Verifier provides the random seed challenge and operational domain, and the Holder/Prover must sign the generated proof to protect from replay attacks.

```shell
curl -X 'POST' 'http://localhost:8070/cloud-agent/present-proof/presentations' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
 -H "apikey: $API_KEY" \
  -d '{
        "connectionId": "872ddfa9-4115-46c2-8a1b-22c24c7431d7",
        "proofs":[],
        "options": {
          "challenge": "11c91493-01b3-4c4d-ac36-b336bab5bddf",
          "domain": "https://prism-verifier.com"
        }
      }'
```

```shell
curl -X 'POST' 'http://localhost:8070/cloud-agent/present-proof/presentations' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
 -H "apikey: $API_KEY" \
  -d '{
        "connectionId": "872ddfa9-4115-46c2-8a1b-22c24c7431d7",
        "anoncredPresentationRequest": {
          "requested_attributes": {
            "attribute1": {
              "name": "Attribute 1",
              "restrictions": [
                {
                  "cred_def_id": "credential_definition_id_of_attribute1"
                }
              ],
              "non_revoked": {
                 "from": 1635734400,
                 "to": 1735734400
               }
            }
          },
          "requested_predicates": {
            "predicate1": {
              "name": "age",
              "p_type": ">=",
              "p_value": 18,
              "restrictions": [
                {
                  "schema_id": "schema_id_of_predicate1"
                }
              ],
              "non_revoked": {
                "from": 1635734400
               }
            }
          },
          "name": "Example Presentation Request",
          "nonce": "1234567890",
          "version": "1.0"
        },
        "credentialFormat": "AnonCreds" 
      }'
```

a. `SD-JWT` The absence of the `cnf` key claim in the SD-JWT Verifiable Credential (VC) means that the Holder/Prover is unable to create a presentation and sign the `challenge` and `domain` supplied by the verifier

```shell
curl -X 'POST' 'http://localhost:8070/cloud-agent/present-proof/presentations' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
 -H "apikey: $API_KEY" \
  -d '{
        "connectionId": "872ddfa9-4115-46c2-8a1b-22c24c7431d7",
        "proofs":[],
        "credentialFormat": "SDJWT",
         "claims": {
            "emailAddress": {},
            "givenName": {},
    `       "region": {},
            "country": {}`
         }
      }'
```

b. `SD-JWT` The presence of the `cnf` key as a disclosable claim in the SD-JWT Verifiable Credential (VC) allows the Holder/Prover to create a presentation and sign the `challenge` and `domain` given by the verifier.

```shell
curl -X 'POST' 'http://localhost:8070/cloud-agent/present-proof/presentations' \
  -H 'accept: application/json' \
  -H 'Content-Type: application/json' \
 -H "apikey: $API_KEY" \
  -d '{
        "connectionId": "872ddfa9-4115-46c2-8a1b-22c24c7431d7",
        "proofs":[],
        "options": {
          "challenge": "11c91493-01b3-4c4d-ac36-b336bab5bddf",
          "domain": "https://prism-verifier.com"
        },
        "credentialFormat": "SDJWT",
         "claims": {
            "emailAddress": {},
            "givenName": {},
            "region": {},
            "country": {}
         }
      }'
```

SDJWT Specific attributes

1. `credentialFormat`: SDJWT.  
2. `claims`: The claims to be disclosed  by Holder/Prover.

Upon execution, a new presentation request record gets created with an initial state of `RequestPending`. The Verifier Cloud agent will send the presentation request message to the Cloud agent of the Holder/Prover through the specified DIDComm connection. The record state then is updated to `RequestSent`.

The Verifier can retrieve the list of presentation records by making a `GET` request to the [`/present-proof/presentations`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Present-Proof/operation/getAllPresentation) endpoint:

```shell
curl -X 'GET' 'http://localhost:8070/cloud-agent/present-proof/presentations' \
  -H 'accept: application/json' \
 -H "apikey: $API_KEY"
```

### Accept presentation proof received from the Holder/prover

Once the Holder/Prover has received a proof presentation request, he can accept it using an appropriate verifiable credential. The Cloud agent of the Verifier will receive that proof and verify it. Upon successful verification, the presentation record state gets updated to `PresentationVerified`.

The Verifier can then explicitly accept the specific verified proof presentation to change the record state to `PresentationAccepted` by making a `PATCH` request to the [`/present-proof/presentations/{id}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Present-Proof/operation/updatePresentation) endpoint:

```shell
curl -X 'PATCH' 'http://localhost:8070/cloud-agent/present-proof/presentations/{PRESENTATION_ID}' \
  -H 'Content-Type: application/json' \
  -H "apikey: $API_KEY" \
  -d '{
        "action": "presentation-accept"
      }'
```

```
---
title: Verifier Flow
---
stateDiagram-v2
  [*] --> RequestPending: new presentation request created by the Verifier
  RequestPending --> RequestSent: presentation request sent to the Holder/Prover PRISM Agent
  RequestSent --> PresentationReceived: presentation proof received from the Holder/Prover
  PresentationReceived --> PresentationVerified: presentation proof verified by the Verifier PRISM Agent
  PresentationVerified --> PresentationAccepted: verified presentation proof explicitly accepted by the Verifier
```

## Holder/Prover

This section describes the interactions available to the Holder/Prover with his Cloud agent.

### Reviewing and accepting a received presentation request

The Holder/Prover can retrieve the list of presentation requests received by its Cloud agent from different Verifiers making a `GET` request to the [`/present-proof/presentations`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Present-Proof/operation/getAllPresentation) endpoint:

```shell
curl -X 'GET' 'http://localhost:8090/cloud-agent/present-proof/presentations' \
  -H 'accept: application/json' \
  -H "apikey: $API_KEY"
```

The Holder/Prover can then accept a specific request, generate the proof, and send it to the Verifier Cloud agent by making a `PATCH` request to the [`/present-proof/presentations/{id}`](https://hyperledger-identus.github.io/docs/agent-api/#tag/Present-Proof/operation/updatePresentation) endpoint:

```shell
curl -X 'PATCH' 'http://localhost:8090/cloud-agent/present-proof/presentations/{PRESENTATION_ID}' \
  -H 'Content-Type: application/json' \
  -H "apikey: $API_KEY" \
  -d '{
        "action": "request-accept",
        "proofId": ["{CRED_RECORD_ID}"]
      }'
```

The Holder/Prover will have to provide the following information:

1. `presentationId`: The unique identifier of the presentation record to accept.  
2. `proofId`: The unique identifier of the verifiable credential record to use as proof.

```shell
curl -X 'PATCH' 'http://localhost:8090/cloud-agent/present-proof/presentations/{PRESENTATION_ID}' \
  -H 'Content-Type: application/json' \
  -H "apikey: $API_KEY" \
  -d '{
        "action": "request-accept",
        "anoncredPresentationRequest":{
          "credentialProofs":[
             {
                "credential":"3e849b98-f0fd-4cb4-ae96-9ea527a76267",
                "requestedAttribute":[
                   "age"
                ],
                "requestedPredicate":[
                   "age"
                ]
              }
          ]
        }
      }'
```

```shell
curl -X 'PATCH' 'http://localhost:8090/cloud-agent/present-proof/presentations/{PRESENTATION_ID}' \
  -H 'Content-Type: application/json' \
  -H "apikey: $API_KEY" \
  -d '{
        "action": "request-accept",
        "proofId": ["{CRED_RECORD_ID}"]
        "claims": {
          "emailAddress": {},
          "givenName": {},
          "address": {
            "region": {},
            "country": {}
          }
        },
        "credentialFormat": "SDJWT"
      }'
```

The Holder/Prover will have to provide the following information:

1. `presentationId`: The unique identifier of the presentation record to accept.  
2. `proofId`: The unique identifier of the verifiable credential record to use as proof.  
3. `credentialFormat`: SDJWT.  
4. `claims`: The Verifier requests specific claims to disclose. The path of these claims must match exactly with those in the SD-JWT Verifiable Credential (VC).  
- 📌 **Note:**  When a SD-JWT Verifiable Credential (VC) has nested claims such as region and country within an address object, as shown in the example above, it falls under the Holder's responsibility to supply the correct nested JSON structure for the claims attribute(s) that is being disclosed.  
- 📌 **Note:** The holder or prover of the claims is only required to disclose the attribute names and the correct JSON path. The actual values are not necessary. A special JSON placeholder `{}`, can be used instead.

The Holder/Prover will have to provide the following information:

1. `presentationId`: The unique identifier of the presentation record to accept.  
2. `anoncredPresentationRequest`: A list of credential unique identifier with the attribute and predicate the credential is answering for.

The record state is updated to `PresentationPending` and processed by the Holder/Prover Cloud agent. The agent will automatically generate the proof presentation, change the state to `PresentationGenerated`, and will eventually send it to the Verifier Agent, and change the state to `PresentationSent`.

```
---
title: Holder/Prover Flow
---
stateDiagram-v2
  [*] --> RequestReceived: presentation request received by the PRISM Agent
  RequestReceived --> PresentationPending: request accepted by the Holder/Prover
  PresentationPending --> PresentationGenerated: presentation proof generated by the PRISM Agent
  PresentationGenerated --> PresentationSent: generated proof sent to the Verifier PRISM Agent
```

## Sequence diagram

The following diagram shows the end-to-end flow for a verifier to request and verify a proof presentation from a Holder/prover.

---

## Credential revocation

Identus implements the revocation mechanism of JWT credentials according to [Verifiable Credentials Status List v2021](https://www.w3.org/TR/2023/WD-vc-status-list-20230427/). This open standard enables Identus to verify the revocation status of any credential that implements the revocation mechanism using the same specification.

## Overview

Every credential will contain the property `credentialStatus`, which will look like this:

```json
"credentialStatus": {
    "id": "http://localhost:8080/cloud-agent/cloud-agent/credential-status/27526236-3836-4061-9867-f69314e258b4#94567"
    "type": "StatusList2021Entry",
    "statusPurpose": "revocation",
    "statusListIndex": "94567",
    "statusListCredential": "http://localhost:8080/cloud-agent/cloud-agent/credential-status/27526236-3836-4061-9867-f69314e258b4"
  },
```

* `type` will always be `StatusList2021Entry`  
* `statusListCredential` is a publicly accessible URL that resolves a status list credential that looks like this:

```json
{
  "proof" : {...},
  "@context" : [
    "https://www.w3.org/2018/credentials/v1",
    "https://w3c.github.io/vc-bitstring-status-list/contexts/2021/v1.jsonld"
  ],
  "type" : [
    "VerifiableCredential",
    "StatusList2021Credential"
  ],
  "id" : "http://localhost:8085/credential-status/c2688c98-ecb5-4dd3-87e8-a689b8fa84dd",
  "issuer" : "did:prism:462c4811bf61d7de25b3baf86c5d2f0609b4debe53792d297bf612269bf8593a",
  "issuanceDate" : 1717793619,
  "credentialSubject" : {
    "type" : "StatusList2021",
    "statusPurpose" : "Revocation",
    "encodedList" : "H4sIAAAAAAAA_-3BMQ0AAAACIGf_0MbwARoAAAAAAAAAAAAAAAAAAADgbbmHB0sAQAAA"
  }
}

```

* `statusListIndex` is an index in a bit string at which the credential's revocation status can be verified.

The status list credential contains  `encodedList`, a base64-encoded bit string that contains the credential's revocation status.

## Verification

To verify the revocation status of the credential, one must follow these steps:

1. Resolve the Status list credential using the URL found at path: `credentialStatus.statusListCredential`  
2. Verify the embedded proof of the credential.  
3. Decode bit-string, which is in the JSON document of the Status list credential, found at path \- `credentialSubject.encodedList`  
4. Use the status list index from `credentialStatus.statusListIndex` to check if the bit at this index in the decoded bit-string from step 3 is on or off. If the bit is on, the credential is revoked. Otherwise, a revocation has yet to occur.

## Proof verification

Status list credential integrity can be verified using the embedded proof.

We currently support 2 types of proofs:

* `DataIntegrityProof` via crypto suite `eddsa-jcs-2022`. The exact steps on how to create a verify this proof are in the [Data Integrity EdDSA Cryptosuites v1.0](https://www.w3.org/TR/vc-di-eddsa/#eddsa-jcs-2022)  
  * Used for Ed25519 EC curves, meaning if the proof was created using Ed25519 private key  
* `EcdsaSecp256k1Signature2019`, the description of this cryptosuite see the \[https://w3c-ccg.github.io/lds-ecdsa-secp256k1-2019/)  
  * Used for secp256k1 EC curves, meaning if the proof was created using secp256k1 private key  
  * This proof type uses RDF canonicalization algorithm as a data normalization before singing and verifying, the variant of this algorithm used is `URDNA2015`

**Example of status list credential with `DataIntegrityProof`**

```json
{
  "proof" : {
    "type" : "DataIntegrityProof",
    "proofPurpose" : "assertionMethod",
    "verificationMethod" : "data:application/json;base64,eyJAY29udGV4dCI6WyJodHRwczovL3czaWQub3JnL3NlY3VyaXR5L211bHRpa2V5L3YxIl0sInR5cGUiOiJNdWx0aWtleSIsInB1YmxpY0tleU11bHRpYmFzZSI6InVNRll3RUFZSEtvWkl6ajBDQVFZRks0RUVBQW9EUWdBRUNYSUZsMlIxOGFtZUxELXlrU09HS1FvQ0JWYkZNNW91bGtjMnZJckp0UzRQWkJnMkxyNEQzUFdYR2xHTXB1aHdwSk84MEFpdzFXeVVHT1hONkJqSlFBPT0ifQ==",
    "created" : "2024-03-23T16:45:50.924279Z",
    "proofValue" : "ziKx1CJPKLy4U9kMmVzYct5xztq4oHRLPgMpAjh95zQxzBZorhLFmhZ85UPixJoQbaqkVaygLBnLARyxgGJGFNKFggaPSXHgJuG",
    "cryptoSuite" : "eddsa-jcs-2022"
  },
  "@context" : [
    "https://www.w3.org/2018/credentials/v1",
    "https://w3c.github.io/vc-bitstring-status-list/contexts/2021/v1.jsonld"
  ],
  "type" : [
    "VerifiableCredential",
    "StatusList2021Credential"
  ],
  "id" : "http://localhost:8080/cloud-agent/credential-status/27526236-3836-4061-9867-f69314e258b4",
  "issuer" : "did:prism:462c4811bf61d7de25b3baf86c5d2f0609b4debe53792d297bf612269bf8593a",
  "issuanceDate" : 1711212350,
  "credentialSubject" : {
    "id" : "",
    "type" : "StatusList2021",
    "statusPurpose" : "Revocation",
    "encodedList" : "H4sIAAAAAAAAAO3BMQEAAADCoPVPbQwfoAAAAAAAAAAAAAAAAAAAAIC3AYbSVKsAQAAA"
  }
}
```

**Example of status list credential with `EcdsaSecp256k1Signature2019` proof**

```json
{
  "proof" : {
    "type" : "EcdsaSecp256k1Signature2019",
    "proofPurpose" : "assertionMethod",
    "verificationMethod" : "data:application/json;base64,eyJAY29udGV4dCI6WyJodHRwczovL3czaWQub3JnL3NlY3VyaXR5L3YxIl0sInR5cGUiOiJFY2RzYVNlY3AyNTZrMVZlcmlmaWNhdGlvbktleTIwMTkiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia2V5X29wcyI6WyJ2ZXJpZnkiXSwia3R5IjoiRUMiLCJ4IjoiQ1hJRmwyUjE4YW1lTEQteWtTT0dLUW9DQlZiRk01b3Vsa2MydklySnRTND0iLCJ5IjoiRDJRWU5pNi1BOXoxbHhwUmpLYm9jS1NUdk5BSXNOVnNsQmpsemVnWXlVQT0ifX0=",
    "created" : "2024-06-07T20:53:40.177613Z",
    "jws" : "eyJiNjQiOmZhbHNlLCJjcml0IjpbImI2NCJdLCJhbGciOiJFUzI1NksifQ..-JD-ggFe1JgWd7NxUqOvmkHwuO-B2U9V-Nqn2jIsXg1TN8G_Suy__ZNSUwIRPdhFXKy3YXvisfrtheDdOemu7g"
  },
  "@context" : [
    "https://www.w3.org/2018/credentials/v1",
    "https://w3c.github.io/vc-bitstring-status-list/contexts/2021/v1.jsonld"
  ],
  "type" : [
    "VerifiableCredential",
    "StatusList2021Credential"
  ],
  "id" : "http://localhost:8085/credential-status/c2688c98-ecb5-4dd3-87e8-a689b8fa84dd",
  "issuer" : "did:prism:462c4811bf61d7de25b3baf86c5d2f0609b4debe53792d297bf612269bf8593a",
  "issuanceDate" : 1717793619,
  "credentialSubject" : {
    "type" : "StatusList2021",
    "statusPurpose" : "Revocation",
    "encodedList" : "H4sIAAAAAAAA_-3BMQ0AAAACIGf_0MbwARoAAAAAAAAAAAAAAAAAAADgbbmHB0sAQAAA"
  }
}

```

## Revocation

Only issuers of a credential can revoke a credential.

*Get the list of credentials*

```shell
curl -X 'GET' \
  'http://localhost:8080/cloud-agent/issue-credentials/records' \
  -H 'accept: application/json'
```

This endpoint will return the credentials issued. Every credential includes an ID.

*Revoke the credential*

```shell
curl -X 'PATCH' \
  'http://localhost:8080/cloud-agent/revoke-credential/<credential_id>' \
  -H 'accept: */*'
```

:::note [Present proof](https://hyperledger-identus.github.io/docs/tutorials/didcomm/issue.md) will fail the verification if one of the credentials the holder presents a revoked credential. :::

# Multi-tenancy {#multi-tenancy}

## Overview

Multi-tenancy enables a single Cloud agent instance to serve multiple organizations or user groups with complete data isolation. Each tenant has their own wallet containing DIDs, credentials, and connections, logically separated from other tenants. This section covers tenant onboarding, authentication methods (built-in and external IAM with Keycloak), access control, and tenant migration.

Understanding multi-tenancy is essential for enterprise deployments, SaaS platforms, or any scenario where you need to serve multiple distinct user groups from shared infrastructure.

---

## Tenant onboarding

In a multi-tenant setup, it's crucial to understand the various roles within the system. There are two key roles in tenant management: administrators and tenants. Administrators are in charge of managing wallets and tenants, while tenants are users who engage in standard SSI interactions with the Cloud agent.

## Roles

In tenant management, there are 2 roles:

1. [Administrator](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#administrator)  
2. [Tenant](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#tenant)

## Prerequisites

1. The Cloud agent up and running  
2. The Cloud agent is configured with the following environment variables:  
   1. `ADMIN_TOKEN=my-admin-token`  
   2. `API_KEY_ENABLED=true`  
   3. `API_KEY_AUTO_PROVISIONING=false`  
   4. `DEFAULT_WALLET_ENABLED=false`

## Overview

This is a guide on how to onboard a new tenant from scratch. This tutorial will demonstrate the creation of a new entity representing the tenant, the provisioning of a wallet, and enabling an authentication method for this tenant. Subsequently, the tenant will gain the capability to engage in SSI activities within an isolated wallet environment using the Cloud agent.

## Endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| `GET /wallets` | List the wallets on the Cloud agent | Administrator |
| `POST /wallets` | Create a new wallet on the Cloud agent | Administrator |
| `POST /iam/entities` | Create a new entity on the Cloud agent | Administrator |
| `POST /iam/apikey-authentication` | Create a new authentication for the entity | Administrator |
| `GET /did-registrar/dids` | List the DIDs inside the wallet | Tenant |

## Administrator interactions

### Check the existing wallets

When running the Cloud agent using the configurations above, the Agent should start with an empty state. Listing wallets on it should return empty results.

```shell
curl -X 'GET' \
  'http://localhost:8080/cloud-agent/wallets' \
  -H 'accept: application/json' \
  -H 'x-admin-api-key: my-admin-token'
```

Response Example:

```json
{
  "self": "/wallets",
  "kind": "WalletPage",
  "pageOf": "/wallets",
  "contents": []
}
```

### Create a new wallet

The wallet can be created using a `POST /wallets` endpoint. This wallet is going to act as a container for the tenant's assets (DIDs, VCs, Connections, etc.). The wallet seed may be provided during the wallet creation or omitted to let the Agent generate one randomly.

```shell
curl -X 'POST' \
  'http://localhost:8080/cloud-agent/wallets' \
  -H 'accept: application/json' \
  -H 'x-admin-api-key: my-admin-token' \
  -H 'Content-Type: application/json' \
  -d '{
    "seed": "c9994785ce6d548134020f610b76102ca1075d3bb672a75ec8c9a27a7b8607e3b9b384e43b77bb08f8d5159651ae38b98573f7ecc79f2d7e1f1cc371ce60cf8a",
    "name": "my-wallet"
  }'
```

Response Example:

```json
{
  "id": "99734c87-5c9d-4697-b5fd-dea4e9590ba7",
  "name": "my-wallet",
  "createdAt": "2023-01-01T00:00:00Z",
  "updatedAt": "2023-01-01T00:00:00Z"
}
```

### Create a new entity

A new entity can be created to represent a tenant. To create a new entity, send a `POST` request to the `/iam/entities` endpoint with the following parameters:

```shell
curl -X 'POST' \
  'http://localhost:8080/cloud-agent/iam/entities' \
  -H 'accept: application/json' \
  -H 'x-admin-api-key: my-admin-token' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "John",
    "walletId": "99734c87-5c9d-4697-b5fd-dea4e9590ba7"
  }'
```

Make sure to use the `walletId` from the previous step.

Response Example:

```json
{
  "kind": "Entity",
  "self": "/iam/entities/10000000-0000-0000-0000-000000000000",
  "id": "10000000-0000-0000-0000-000000000000",
  "name": "John",
  "walletId": "99734c87-5c9d-4697-b5fd-dea4e9590ba7",
  "createdAt": "2023-09-01T14:00:38.760045Z",
  "updatedAt": "2023-09-01T14:00:38.760047Z"
}
```

### Register `apikey` authentication method

With the new tenant now equipped with both a wallet and an entity, the final step involves setting up the entity's authentication method. Once this step is completed, the administrator should provide the tenant with an `apikey`, granting them access to utilize the Agent.

```shell
curl -X 'POST' \
  'http://localhost:8080/cloud-agent/iam/apikey-authentication' \
  -H 'accept: */*' \
  -H 'x-admin-api-key: my-admin-token' \
  -H 'Content-Type: application/json' \
  -d '{
    "entityId": "10000000-0000-0000-0000-000000000000",
    "apiKey": "my-tenant-token"
  }'
```

Make sure to use the `entityId` from the previous step.

HTTP code 201 returns in the case of the successful request execution.

## Tenant interactions

With the `apikey` provisioned by the administrator, the tenant is able to authenticate and use the Cloud agent.

### Perform a simple action to verify access to the Cloud agent

To prove that the tenant can be authenticated as the created entity and use the wallet, try listing the DIDs in the wallet using `apikey` header.

```shell
curl --location --request GET 'http://localhost:8080/cloud-agent/did-registrar/dids' \
  --header "apikey: my-tenant-token" \
  --header 'Accept: application/json'
```

The result should show 200 status with an empty list. This means that the wallet has been created and it does not contain any DIDs. Any interactions that the tenant performs should be scoped to only this wallet.

---

## Tenant onboarding with external IAM

In the [Tenant Onboarding](https://hyperledger-identus.github.io/docs/tutorials/tenant-onboarding.md) tutorial, we explored the basic [IAM](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#iam) functionality out of the box. Although it is usable and straightforward, more featureful tools are available for handling identity and access management. The agent can seamlessly connect with [Keycloak](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#keycloak-service) as an external IAM system, allowing the application built on top to utilize the capabilities that come with Keycloak.

The Cloud agent leverages standard protocols like [OIDC](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#oidc) and [UMA](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#uma) for authentication and access management. The user's identity gets established through the ID token, and wallet permissions are searchable using the [RPT (requesting party token)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#rpt).

## Roles

In tenant management with external IAM, there are 2 roles:

1. [Administrator](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#administrator)  
2. [Tenant](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#tenant)

## Prerequisites

1. Keycloak up and running  
2. Keycloak is configured as follows  
   1. A realm called `my-realm` is created  
   2. A client called `cloud-agent` under `my-realm` with **authorization** feature is created. (See [create client instruction](https://www.keycloak.org/home/latest/authorization_services/index.html#_resource_server_create_client))  
   3. Make sure the `cloud-agent` client has **direct access grants** enabled to simplify the login process for this tutorial  
3. the Cloud agent is up and running  
4. the Cloud agent is configured with the following environment variables:  
   1. `ADMIN_TOKEN=my-admin-token`  
   2. `DEFAULT_WALLET_ENABLED=false`  
   3. `KEYCLOAK_ENABLED=true`  
   4. `KEYCLOAK_URL=http://localhost:9980` (replace with appropriate value)  
   5. `KEYCLOAK_REALM=my-realm`  
   6. `KEYCLOAK_CLIENT_ID=cloud-agent`  
   7. `KEYCLOAK_CLIENT_SECRET=<KEYCLOAK_CLIENT_SECRET>` (replace with appropriate value)  
   8. `KEYCLOAK_UMA_AUTO_UPGRADE_RPT=false`

## Overview

This tutorial illustrates the process of provisioning a wallet resource for the new tenant and creating a tenant in Keycloak. The administrator can then create a UMA permission for the wallet, giving access to the tenant.

When setting up UMA permissions on the agent, the wallet resource, along with the UMA policy and permission are created on Keycloak according to a predefined convention. For flexibility in defining custom policy and permission models, administrators can manually create these UMA resources (resource, policy, permission) directly on Keycloak using a set of UMA endpoints called [Protection API](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#protection-api)  (see [Keycloak Protection API](https://www.keycloak.org/docs/latest/authorization_services/index.html#_service_protection_api)). However, using Protection API to manage permissions is out of the scope of this tutorial.

Once the registration is successful, the tenant can obtain an ID token from Keycloak using any available OIDC flow, such as the direct access grants (username & password). This ID token typically contains user claims such as username and subject ID. The tenant can use Keycloak's token endpoint to convert this token to an RPT (requesting party token), another token containing permissions information. The tenant can access the multi-tenant agent by providing the RPT in the `Authorization` header.

## Endpoints

### Agent endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| `GET /wallets` | List the wallets on the Cloud agent | Administrator |
| `POST /wallets` | Create a new wallet on the Cloud agent | Administrator |
| `POST /wallets/{walletId}/uma-permissions` | Create a uma-permission for a wallet | Administrator |
| `GET /did-registrar/dids` | List the DIDs inside the wallet | Tenant |

### Keycloak endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| `POST /admin/realms/{realm}/users` | Create a new user on Keycloak | Administrator |
| `POST /realms/{realm}/protocol/openid-connect/token` | Issue a new JWT token | Administrator, Tenant |

## Administrator interactions

### Check the existing wallets

When running Cloud agent using the configurations above, the Cloud agent should start with an empty state. Listing wallets on it should return empty results.

```shell
curl -X 'GET' \
  'http://localhost:8080/cloud-agent/wallets' \
  -H 'accept: application/json' \
  -H 'x-admin-api-key: my-admin-token'
```

Response Example:

```json
{
  "self": "/wallets",
  "kind": "WalletPage",
  "pageOf": "/wallets",
  "contents": []
}
```

### Create a new wallet

Create a wallet using a `POST /wallets` endpoint. This wallet will be a container for the tenant's assets (DIDs, VCs, Connections, etc.). Provide a wallet seed during the wallet creation or let the Agent generate one

```shell
curl -X 'POST' \
  'http://localhost:8080/cloud-agent/wallets' \
  -H 'accept: application/json' \
  -H 'x-admin-api-key: my-admin-token' \
  -H 'Content-Type: application/json' \
  -d '{
    "seed": "c9994785ce6d548134020f610b76102ca1075d3bb672a75ec8c9a27a7b8607e3b9b384e43b77bb08f8d5159651ae38b98573f7ecc79f2d7e1f1cc371ce60cf8a",
    "name": "my-wallet"
  }'
```

Response Example:

```json
{
  "id": "99734c87-5c9d-4697-b5fd-dea4e9590ba7",
  "name": "my-wallet",
  "createdAt": "2023-01-01T00:00:00Z",
  "updatedAt": "2023-01-01T00:00:00Z"
}
```

### User registration on Keycloak

There are multiple ways to complete this step. The goal is to ensure the user has registered on Keycloak. Keycloak offers great flexibility, allowing users to self-register, For this tutorial, we will generate the user manually using Keycloak admin API for simplicity.

The first step is to get an admin token from Keycloak using the username and password. This token allows the admin to perform operations on Keycloak, such as creating a new user. Running the provided command should return the admin access token.

```shell
curl -X 'POST' \
  'http://localhost:9980/realms/master/protocol/openid-connect/token' \
  -d "grant_type=password" \
  -d "client_id=admin-cli" \
  -d "username=$KEYCLOAK_ADMIN_USER" \
  -d "password=$KEYCLOAK_ADMIN_PASSWORD"
```

Make sure to replace the Keycloak variables with appropriate values.

Example token response (some fields omitted for readability)

```json
{
    "access_token": "eyJhbGciOi...7ocDHofUDQ",
    "refresh_token": "eyJhbGciOi...otsEEi4eQA",
    ...
}
```

After obtaining the `access_token` from Keycloak's admin, a new user can be created using this command:

```shell
curl -X 'POST' \
  'http://localhost:9980/admin/realms/my-realm/users' \
  -v \
  -H 'Authorization: Bearer eyJhbGciOi...7ocDHofUDQ' \
  -H 'Content-Type: application/json' \
  --data-raw "{
    \"username\": \"alice\",
    \"firstName\": \"Alice\",
    \"enabled\": true,
    \"credentials\": [{\"value\": \"1234\", \"temporary\": false}]
  }"
```

Make sure to use the correct `access_token` in the `Authorization` header from the previous command.

Example response log

```
< HTTP/1.1 201 Created
< Referrer-Policy: no-referrer
< X-Frame-Options: SAMEORIGIN
< Strict-Transport-Security: max-age=31536000; includeSubDomains
< X-Content-Type-Options: nosniff
< X-XSS-Protection: 1; mode=block
< Location: http://localhost:9980/admin/realms/my-realm/users/205e04b7-0158-41b0-89c3-f91c3a09f89b
< content-length: 0
```

The response should return status `201 Created` indicating the new user is registered with username `alice` with a password `1234`. The user ID can be observed from `Location` header of the response. This ID will be used for creating permission later in this tutorial.

For in-depth user management, please consult the official Keycloak administration documentation on [managing users section](https://www.keycloak.org/docs/latest/server_admin/index.html#assembly-managing-users_server_administration_guide).

### Grant the user permission to the wallet

Once the user and wallet have been successfully created, the permissions can be created giving the user access to the wallet. This can be done by invoking the `POST /wallets/{walletId}/uma-permissions` endpoint on the agent.

```shell
curl -X 'POST' \
  'http://localhost:8080/cloud-agent/wallets/99734c87-5c9d-4697-b5fd-dea4e9590ba7/uma-permissions' \
  -v \
  -H 'accept: */*' \
  -H 'x-admin-api-key: my-admin-token' \
  -H 'Content-Type: application/json' \
  -d '{
    "subject": "205e04b7-0158-41b0-89c3-f91c3a09f89b"
  }'
```

Make sure to use the correct `subject` for the user and the correct `walletId` from the step earlier. The user ID can be observed from the response header from the previous step or in the **User** menu in Keycloak Admin UI.

The response should return status `200 OK` in case of successful permission creation.

## Tenant interactions

After the user is registered on Keycloak and the required permission is created by admin, the tenant can log in and utilize the agent by using the token issued by Keycloak.

### Obtain access token from Keycloak

The first step is to authenticate via Keycloak through any applicable authentication method. Usually, the tenant will use some frontend application that follows the standard flow for logging in. For simplicity, we use a flow for username and password in this tutorial. The administrator has already set up the username and password for the tenant. To get the access token, the tenant can call the Keycloak token endpoint directly with those credentials.

Run the command to log in

```shell
curl -X 'POST' \
  'http://localhost:9980/realms/my-realm/protocol/openid-connect/token' \
  -d "grant_type=password" \
  -d "client_id=admin-cli" \
  -d "username=alice" \
  -d "password=1234"
```

Special attention on the `client_id`; this should be the actual `client_id` of the frontend application that logs the user in. For this tutorial, it is OK to use `admin-cli`.

Example token response (some fields omitted for readability)

```json
{
    "access_token": "eyJhbGciOi...7ocDHofUDQ",
    "refresh_token": "eyJhbGciOi...otsEEi4eQA",
    ...
}
```

### Request [RPT (requesting party token)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#rpt) from access token

After the access token is acquired, the next step is to get the RPT token, which holds information about the permissions. It is possible to request the RPT by running this command:

```shell
curl -X POST \
  'http://localhost:9980/realms/my-realm/protocol/openid-connect/token' \
  -H "Authorization: Bearer eyJhbGciOi...7ocDHofUDQ" \
  -d "grant_type=urn:ietf:params:oauth:grant-type:uma-ticket" \
  -d "audience=cloud-agent"
```

Example token response (some fields omitted for readability)

```json
{
    "access_token": "eyJhbGciOi...e7H6W8RUvA",
    "refresh_token": "eyJhbGciOi...W1_y1AF_YY",
    ...
}
```

After inspecting the response token, a new claim named `authorization` should appear in the JWT payload.

Example RPT payload (some fields omitted for readability)

```json
{
  ...
  "authorization": {
    "permissions": [
      {
        "rsid": "99734c87-5c9d-4697-b5fd-dea4e9590ba7",
        "rsname": "<WALLET_RESOURCE_NAME>"
      },
      ...
    ]
  },
  ...
}
```

### Perform a simple action to verify access to the Cloud agent

To prove that the tenant can access the wallet using RPT, try listing the DIDs in the wallet using RPT in the `Authorization` header.

```shell
curl --location --request GET 'http://localhost:8080/cloud-agent/did-registrar/dids' \
  -H 'Authorization: Bearer eyJhbGciOi...e7H6W8RUvA' \
  -H 'Accept: application/json'
```

Make sure to replace the token with RPT from previous step.

The result should show 200 status with an empty list. This means that the wallet has been created and does not contain any DIDs. All actions carried out by the tenant must be limited to this specific wallet.

### A note on RPT

In this tutorial, there is an additional step for the tenant to request the RPT from the access token. This process aligns with the standard UMA interaction, where the handling of RPT typically occurs on the client side. To simplify the experience, the agent has a feature allowing users to bypass this process. By setting the variable `KEYCLOAK_UMA_AUTO_UPGRADE_RPT=true`, tenants can utilize the access token obtained in step 1 directly in the `Authorization` header, eliminating the need for additional RPT request step.

---

## Tenant onboarding self-service

In the [Tenant Onboarding with External IAM](https://hyperledger-identus.github.io/docs/tutorials/tenant-onboarding-ext-iam.md) tutorial, we learned how [Keycloak](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#keycloak-service) helps with user access and how it works together with the agent. To set things up, the admin has to provision the required resources. However, relying on the admin for onboarding operations can be restrictive for some use cases. For example, some tenants might want to onboard on a self-service agent instance without admin intervention.

By leveraging Keycloak for a self-service agent instance, users can self-register or link to other [Identity Providers (IDPs)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#idp) to register an account. Once the account is registered, users can use it to set up their wallets. This tutorial will investigate the steps to facilitate this scenario where [administrator](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#administrator) intervention is unnecessary.

## Roles

In self-service tenant management with external IAM, there is only one role:

2. [Tenant](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#tenant)

## Prerequisites

1. Keycloak is up and running.  
2. Keycloak is configured as follows  
   1. A realm called `my-realm` is created  
   2. A client called `cloud-agent` under `my-realm` with **authorization** feature is created. (See [create client instruction](https://www.keycloak.org/docs/latest/authorization_services/index.html#_resource_server_create_client))  
   3. Make sure the `cloud-agent` client has **direct access grants** enabled to simplify the login process for this tutorial.  
   4. Make sure to [allow user self-registration](https://www.keycloak.org/docs/latest/server_admin/index.html#con-user-registration_server_administration_guide).  
3. The Cloud agent is up and running  
4. The Cloud agent is configured with the following environment variables:  
   1. `ADMIN_TOKEN=my-admin-token`  
   2. `DEFAULT_WALLET_ENABLED=false`  
   3. `KEYCLOAK_ENABLED=true`  
   4. `KEYCLOAK_URL=http://localhost:9980` (replace with appropriate value)  
   5. `KEYCLOAK_REALM=my-realm`  
   6. `KEYCLOAK_CLIENT_ID=cloud-agent`  
   7. `KEYCLOAK_CLIENT_SECRET=<KEYCLOAK_CLIENT_SECRET>` (replace with appropriate value)  
   8. `KEYCLOAK_UMA_AUTO_UPGRADE_RPT=true`

## Overview

This tutorial demonstrate the process of user self-registration on Keycloak. Then, the users can log in to Keycloak to obtain a token. When the agent uses this token for the wallet creation, the agent recognizes it belongs to a tenant and automatically associates the tenant's permission with the created wallet.

## Endpoints

### Agent endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| `GET /wallets` | List the wallets on the Cloud agent | Tenant |
| `POST /wallets` | Create a new wallet on the Cloud agent | Tenant |
| `GET /did-registrar/dids` | List the DIDs inside the wallet | Tenant |

### Keycloak endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| `POST /realms/{realm}/protocol/openid-connect/token` | Issue a new JWT token | Tenant |

## Tenant interactions

### Self-register account on Keycloak

Start by registering a new account on Keycloak, accessible through its login page. Usually, this should be available at `http://localhost:9980/realms/my-realm/account/`.

For detailed instructions on how to register a new user, please refer to [registering a new user](https://www.keycloak.org/docs/latest/server_admin/index.html#proc-registering-new-user_server_administration_guide) section on the official documentation.

### Obtain access token from Keycloak

Once a new account is registered, Keycloak should recognize it, allowing the user to log in and obtain the access token.

Run this command to log in and get the access token.

```shell
curl -X 'POST' \
  'http://localhost:9980/realms/my-realm/protocol/openid-connect/token' \
  -d "grant_type=password" \
  -d "client_id=admin-cli" \
  -d "username=alice" \
  -d "password=1234"
```

Make sure to use the correct username and password. Special attention on the `client_id`; this should be the actual `client_id` of the frontend application that logs the user in. For this tutorial, it is OK to use `admin-cli`.

Example token response (some fields omitted for readability)

```json
{
    "access_token": "eyJhbGciOi...7ocDHofUDQ",
    "refresh_token": "eyJhbGciOi...otsEEi4eQA",
    ...
}
```

### Check the existing wallets

Right after the account is registered, no permission should be associated with it. Listing wallets on it should return empty results.

```shell
curl -X 'GET' \
  'http://localhost:8080/cloud-agent/wallets' \
  -H 'Authorization: Bearer eyJhbGciOi...7ocDHofUDQ' \
  -H 'Accept: application/json'
```

Use the correct `access_token` in the previous command's Authorization header.

Response Example:

```json
{
  "self": "/wallets",
  "kind": "WalletPage",
  "pageOf": "/wallets",
  "contents": []
}
```

### Create a new wallet

Create a wallet using a `POST /wallets` endpoint. This wallet will be a container for the tenant's assets (DIDs, VCs, Connections, etc.). The Agent can provide or randomly generate the wallet seed during wallet creation.

If the user already has the wallet associated, the wallet creation will fail as multiple wallets per tenant are not yet allowed.

```shell
curl -X 'POST' \
  'http://localhost:8080/cloud-agent/wallets' \
  -H 'Authorization: Bearer eyJhbGciOi...7ocDHofUDQ' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "seed": "c9994785ce6d548134020f610b76102ca1075d3bb672a75ec8c9a27a7b8607e3b9b384e43b77bb08f8d5159651ae38b98573f7ecc79f2d7e1f1cc371ce60cf8a",
    "name": "my-wallet"
  }'
```

Response Example:

```json
{
  "id": "99734c87-5c9d-4697-b5fd-dea4e9590ba7",
  "name": "my-wallet",
  "createdAt": "2023-01-01T00:00:00Z",
  "updatedAt": "2023-01-01T00:00:00Z"
}
```

In this step, the agent creates both wallet resource and UMA permission on Keycloak, assigning the new wallet to the user who created it.

### Perform a simple action to verify access to the Cloud agent

Without further operation, the wallet should be available for the tenant. To prove that the tenant can access the wallet, list the DIDs using RPT in the `Authorization` header.

```shell
curl --location --request GET 'http://localhost:8080/cloud-agent/did-registrar/dids' \
  -H 'Authorization: Bearer eyJhbGciOi...7ocDHofUDQ' \
  -H 'Accept: application/json'
```

The wallet was successfully created, but it currently does not contain any DIDs \- indicated by an empty list and a 200 status. The tenant should only perform interactions within the scope of this wallet.

---

## Access control

**Status**: draft

**Date**: 2021-06-02

**NOTE**: this document is a draft and is not implemented yet. Statement in this document might be changed in the future.

## Introduction

Current document describes the levels of access control in the Identus Platform configured in the Vault service The Vault service uses policies to control the access to the secrets, configuration, and other resources. The policies are applied to the entities and groups of entities.

According to the Principle of Least Privilege (PoLP), the access to the resources must be granted to the entities with the minimal set of permissions required to perform the operation.

## Accounts

### SUDO Account

The account with `sudo` privileges that allows to configure the Vault service. The root token is used for this purpose. The root token must be kept in the most safe place and not used for regular cases.

SUDO Account is used for the following purposes:

- configure the Vault service  
- configure authentication methods  
- configure Management Accounts

Managed by DevOps and SRE teams.

### Management account

Management Account is special account that allows to manage the Cloud agent.

The account with the limited access to configure the Vault service with the following permissions:

- manage the Wallet accounts  
- manage the Agent accounts  
- enforces the policies to the Wallet account

Management Account can be used in the configuration scripts or by the SRE team.

### Agent account

Agent Account is created for the Cloud agent to authenticate itself to the Vault service. AppRole authentication method is used for this account.

The account with the limited access to configure the Vault service with the following permissions:

- create the Wallet accounts  
- issue the token to the Wallet account  
- do other operations required to configure the Wallet account

### Wallet account

The Wallet Account is created for and used by the Wallet. The Wallet Account is has access to the secrets of the Wallet and the Cloud agent must guarantee the data isolation at the Wallet level. This account has the following permissions:

- list, read, write, delete the secrets associated with the Wallet  
- use the REST API associated with the Wallet  
- manage the data associated with the Wallet

## Technical overview

### Principle of Least Privilege

The Principle of Least Privilege (PoLP) is a security concept that requires that a user is granted no more privilege than necessary to perform a task. The following practices are applied to implement the PoLP:

- audit: all the credentials are audited and must be under control of the SRE team  
- administrator and business accounts are separated  
- activity monitoring: activity of the administrator accounts is monitored  
- just-in-time access: the access to the resources is granted only for the time required to perform the operation

**NOTE**: there are other PoLP practices that are not covered in this document. These practices will be ignored for local development and testing purposes.

In order to implement the PoLP, the following access control rules are defined:

- the Cloud agent account has access to the Wallet account that belong to the Agent only  
- the Cloud agent account transparently issues the token to the Wallet account based on configured authentication method

### Token issuing, renewal, expiration and revocation

These policies are applied to all tokens except the SUDO account (root token).

All tokens issued by the Identus Platform have the following properties:

- expiration time  
- maximum time to live (TTL)  
- renewable flag  
- orphan flag  
- policies  
- operation limitations (number of the requests that can be performed by the token)

Management account token policies:

- authentication methods: GitHub, GoogleAuth, user/password  
- expiration time: 1 hour

Agent Account token policies:

- authentication method: AppRole  
- token expiration time: 24 hour

Wallet Account token policies:

- authentication methods: JWT, token issued by the Agent account, user/password  
- token expiration time: 1 hour

**NOTE**: user/password method is used for the development and testing purposes only.

## Links

- [Vault Policies](https://www.vaultproject.io/docs/concepts/policies)  
- [Vault Tokens](https://www.vaultproject.io/docs/concepts/tokens)  
- [Vault Authentication Methods](https://www.vaultproject.io/docs/auth)  
- [Vault AppRole Authentication Method](https://www.vaultproject.io/docs/auth/approle)  
- [Vault JWT Authentication Method](https://www.vaultproject.io/docs/auth/jwt)  
- [Vault GitHub Authentication Method](https://www.vaultproject.io/docs/auth/github)  
- [Vault GoogleAuth Authentication Method](https://www.vaultproject.io/docs/auth/google)  
- [Vault Userpass Authentication Method](https://www.vaultproject.io/docs/auth/userpass)  
- [Vault Tokens](https://www.vaultproject.io/docs/concepts/tokens)

---

## Account management

**Status**: draft

**Date**: 2021-06-02

**NOTE**: this document is a draft and is not implemented yet. Statement in this document might be changed in the future.

## Introduction

This document describes the account management in the Identus Platform, types of accounts, and their usage, account authentication, and logical isolation of accounts.

## Technical overview

Account management is a set of operations that allow users to manage their accounts. Account is required for both single and multi-tenant configurations. In the Identus Platform, the account owns the corresponding Wallet managed by the Cloud agent. The account is identified by the tenant ID and represented by the Entity in the Vault service.

### Account types

The Identus Platform supports the following types of accounts:

- Cloud agent Account \- application account used by the Cloud agent to authenticate itself to the Vault service  
- Wallet Account \- application or user account used to access the Wallet assets over the REST API or WEB UI

### Account creation

The Cloud agent Account is created by before the start of the Cloud agent using the Vault cli, REST API or WEB UI. The Wallet account can be created on the go by the Agent or using the Vault cli, REST API or WEB UI.

### Cloud agent Account

The Cloud agent account required to authenticate the instance of the Cloud agent. The account is responsible for creating the Wallet accounts and issuing the tokens to the Wallet instance. The Cloud agent account doesn't have the access to the Wallet secrets. The Cloud agent uses [AppRole](https://www.vaultproject.io/docs/auth/approle) authentication method to authenticate itself to the Vault service.

### Wallet account

The Wallet account is required to authenticate the entity to the Identus Platform and give it the access to Wallet. The Wallet account can be authenticated by the following methods:

- JWT/OIDC token  
- user/password  
- AppRole method (for the applications)  
- other methods supported by the Vault service

The Wallet is initialized by the Agent on demand and internally communicates with the Agent to reissue the access token. The Wallet account must guarantee the data isolation between tenants in the multi-tenant configuration by leveraging the policies of the Vault service and Row Based Policies in the PostgreSQL database.

**NOTE**: It's still discussed which component will be responsible for issuing the JWT token. The Vault service looks promising for configuring OIDC authentication method and use 3rd party system for authentication.

### Account deactivation

Any account can be deactivated by deactivating the entity in the Vault service using the REST API or cli. The deactivated account can be reactivated by the SRE team. In the case when the account must be deleted, the entity must be deleted from the Vault service. Retention policy is a matter of the configuration and must be discussed separately.

## Links

- [Vault AppRole](https://www.vaultproject.io/docs/auth/approle)  
- [Vault Entities and Groups](https://developer.hashicorp.com/vault/tutorials/auth-methods/identity)  
- [Vault JWT/OIDC](https://developer.hashicorp.com/vault/api-docs/auth/jwt)

---

## Administrator authorization with external IAM

In the past tutorials, the administrator utilized a static API key for authentication and authorization. The Agent also features a more sophisticated role-based authorization mechanism using external IAM, allowing users to be authorized under different roles and granting access to parts of the Agent.

## Roles

For the Agent admin authorization, we need to distinguish the administrator of each component:

1. [Agent Administrator](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#administrator)  
2. Keycloak Administrator

The same person may also represent these roles.

## Prerequisites

1. Keycloak is up and running  
2. Keycloak is configured as follows  
   1. A realm called `my-realm` is created  
   2. A client called `cloud-agent` under `my-realm` with **authorization** feature is created. (See [create client instruction](https://www.keycloak.org/docs/latest/authorization_services/index.html#_resource_server_create_client))  
   3. Make sure the `cloud-agent` client has **direct access grants** enabled to simplify the login  
3. The Cloud agent is up and running  
4. The Cloud agent is configured with the following environment variables:  
   1. `KEYCLOAK_ENABLED=true`  
   2. `KEYCLOAK_URL=http://localhost:9980` (replace with appropriate value)  
   3. `KEYCLOAK_REALM=my-realm`  
   4. `KEYCLOAK_CLIENT_ID=cloud-agent`  
   5. `KEYCLOAK_CLIENT_SECRET=<KEYCLOAK_CLIENT_SECRET>` (replace with appropriate value)  
   6. `KEYCLOAL_ROLES_CLAIM_PATH=resource_access.cloud-agent.roles`

## Overview

This tutorial will guide you through the steps to configure roles in Keycloak and demonstrate the authorization process as an administrator within the agent, enabling you to perform various administrative tasks.

Once authorized as an admin, the user gains the ability to manage the wallet; however, the actual utilization of the wallet remains restricted. Despite UMA permissions configured for the user, the agent strictly maintains a clear segregation of roles between administrator and tenant.

## Endpoints

### Agent endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| `GET /wallets` | List the wallets on the Cloud agent | Administrator |

### Keycloak endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| `POST /realms/{realm}/protocol/openid-connect/token` | Issue a new JWT token | Administrator |

## Keycloak Administrator interactions

### Configure Keycloak role on the client

Open the Keycloak admin UI in the browser and navigate to `my-realm` in the admin UI. Choose the corresponding client to create a `ClientRole`. Choose **Role** tab in the client menu and create a new role called `admin`

### Assign the `admin` role to the user

Once the role gets created, the admin user should be assigned this role. For simplicity, we will assign the role to the user without managing any group or other metadata.

Navigate to **Users** menu on the sidebar in the admin UI. Click on the user that should have the `admin` role. Choose **Role Mapping** tab and click **Assign Role**. Choose **Filter by clients** in the dropdown menu. Then choose the `admin` role.

Fore more info on role mapping see the [Keycloak official documentation](https://www.keycloak.org/docs/latest/server_admin/#proc-assigning-role-mappings_server_administration_guide). If a user does not exist, a new user can be created by following this [Keycloak official guide](https://www.keycloak.org/docs/latest/server_admin/#proc-creating-user_server_administration_guide).

After this step, the user should have the `admin` role assigned, and after they log in, the `roles` claim should appear in the JWT payload.

## Agent Administrator interactions

### Obtain an access token from Keycloak

The admin user can log in using any configured authorization flow in Keycloak. In this example, the **direct access grant** is used to login.

Run the command to log in as admin.

```shell
curl -X 'POST' \
  'http://localhost:9980/realms/my-realm/protocol/openid-connect/token' \
  -d "grant_type=password" \
  -d "client_id=admin-cli" \
  -d "username=alice" \
  -d "password=1234"
```

Example token response (some fields omitted for readability)

```json
{
    "access_token": "eyJhbGciOi...7ocDHofUDQ",
    "refresh_token": "eyJhbGciOi...otsEEi4eQA",
    ...
}
```

Inspecting the `access_token` payload, it should have the following content

```json
{
  "resource_access": {
    "cloud-agent": {
      "roles": [
        "admin"
      ]
    },
    "account": {
      "roles": [
        "manage-account",
        "manage-account-links",
        "view-profile"
      ]
    }
  },
  ...
}
```

### Perform a simple action to verify admin rights on the Agent

To prove that the admin can perform admin tasks, try listing all the tenants' wallets using the JWT in the `Authorization` header.

```shell
curl --location --request GET 'http://localhost:8080/cloud-agent/wallets' \
  -H 'Authorization: Bearer eyJhbGciOi...e7H6W8RUvA' \
  -H 'Accept: application/json'
```

The response should return a list of all wallets on the Agent with HTTP status 200\. Trying to perform prohibited tenant interactions, such as listing the DIDs in the wallet, will result in an authorization error.

---

## Tenant migration

The Cloud agent authentication supports multiple authentication methods simultaneously, which means the user can seamlessly use any available credentials including `apikey` or `JWT` to access the wallet. The agent's [UMA](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#uma) permission resource also exposes the self-service permission endpoint, allowing users to manage the permissions for their wallets. It allows users to transition from `apikey` to `JWT` authentication without admin intervention.

## Roles

In the migration process from `apikey` to `JWT`, there is only one role:

2. [Tenant](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#tenant)

## Prerequisites

1. Keycloak up and running  
2. Keycloak is configured the same as in [Tenant Onboarding Self-Service](https://hyperledger-identus.github.io/docs/tutorials/tenant-onboarding-self-service.md)  
3. The Cloud agent is up and running  
4. The Cloud agent is configured the same as in [Tenant Onboarding Self-Service](https://hyperledger-identus.github.io/docs/tutorials/tenant-onboarding-self-service.md)  
5. The user has access to the wallet using `apikey`. (See [Tenant Onboarding](https://hyperledger-identus.github.io/docs/tutorials/tenant-onboarding.md))  
6. The user has an account registered on Keycloak

## Overview

This tutorial outlines the steps to transition from `apikey` to `JWT` authentication. Initially, users have wallet access through the `apikey` method. To migrate to `JWT` authentication, users can create a new UMA permission for their wallet and grant permission to their Keycloak account.

## Endpoints

### Agent endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| `GET /wallets` | List the wallets on the Cloud agent | Tenant |
| `POST /wallets` | Create a new wallet on the Cloud agent | Tenant |
| `POST /wallets/{walletId}/uma-permissions` | Create a uma-permission for a wallet | Tenant |
| `GET /did-registrar/dids` | List the DIDs inside the wallet | Tenant |

### Keycloak endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| `POST /realms/{realm}/protocol/openid-connect/token` | Issue a new JWT token | Tenant |

## Tenant interactions

### Check the existing wallets using `apikey`

This tutorial assumes the tenant can access the wallet using `apikey`. Before granting more permission to the wallet, the `walletId` must be identified. To find the wallet, list them using `apikey`.

```shell
curl -X 'GET' \
  'http://localhost:8080/cloud-agent/wallets' \
  -H 'accept: application/json' \
  -H "apikey: my-tenant-token"
```

Make sure to use the correct `apikey` from the pre-requisite.

Response Example:

```json
{
  "self": "/wallets",
  "kind": "WalletPage",
  "pageOf": "/wallets",
  "contents": [
    {
      "id": "99734c87-5c9d-4697-b5fd-dea4e9590ba7",
      "name": "my-wallet",
      "createdAt": "2023-01-01T00:00:00Z",
      "updatedAt": "2023-01-01T00:00:00Z"
    }
  ]
}
```

### Get the access token on keycloak

Run this command to log in and get the access token

```shell
curl -X 'POST' \
  'http://localhost:9980/realms/my-realm/protocol/openid-connect/token' \
  -d "grant_type=password" \
  -d "client_id=admin-cli" \
  -d "username=alice" \
  -d "password=1234"
```

Make sure to use the correct username and password. Special attention on the `client_id`; this should be the actual `client_id` of the frontend application that logs the user in. For this tutorial, it is OK to use `admin-cli`

Example token response (some fields omitted for readability)

```json
{
    "access_token": "eyJhbGciOi...7ocDHofUDQ",
    "refresh_token": "eyJhbGciOi...otsEEi4eQA",
    ...
}
```

### Extract the subject ID from JWT

When creating a UMA permission, it is important to provide the subject ID to grant permission. To get the subject ID of the tenant, one can inspect the JWT payload `sub` claim.

Run this command to extract the `sub` claim of the token from previous step.

```shell
echo 'eyJhbGciOi...7ocDHofUDQ' | cut --delimiter='.' --fields=2 | base64 --decode | jq -r '.sub'
```

Example result

```
4a5c6ac9-12f5-4d1e-b8f2-667525c083fd
```

### Grant the user permission to the wallet

UMA permission can be added to the current wallet, giving Keycloak users access. To do this, invoke the `POST /wallets/{walletId}/uma-permissions` endpoint on the agent.

```shell
curl -X 'POST' \
  'http://localhost:8080/cloud-agent/wallets/99734c87-5c9d-4697-b5fd-dea4e9590ba7/uma-permissions' \
  -v \
  -H 'accept: */*' \
  -H "apikey: my-tenant-token" \
  -H 'Content-Type: application/json' \
  -d '{
    "subject": "205e04b7-0158-41b0-89c3-f91c3a09f89b"
  }'
```

Make sure to use the correct `subject` for the user and the correct `walletId` from the step earlier.

The response should return the status `200 OK` in case of successful permission creation.

### Perform a simple action to verify access to the Cloud agent

After successful UMA permission creation, the user should be able to use the `JWT` token to authenticate the wallet. List the wallet using a new `Authorization` header. The listed wallets should contain the wallet with the same ID in step 1\.

```shell
curl -X 'GET' \
  'http://localhost:8080/cloud-agent/wallets' \
  -H 'accept: application/json' \
  -H 'Authorization: Bearer eyJhbGciOi...7ocDHofUDQ'
```

Make sure to use the correct `JWT` from step 2\.

Response Example:

```json
{
  "self": "/wallets",
  "kind": "WalletPage",
  "pageOf": "/wallets",
  "contents": [
    {
      "id": "99734c87-5c9d-4697-b5fd-dea4e9590ba7",
      "name": "my-wallet",
      "createdAt": "2023-01-01T00:00:00Z",
      "updatedAt": "2023-01-01T00:00:00Z"
    }
  ]
}
```

This response indicates that the user should be able to perform any wallet interaction with the `JWT` and `apikey` interchangeably.

---

# Secret management {#secret-management}

## Overview

Secure storage and management of cryptographic keys and sensitive data is critical for SSI systems. Hyperledger Identus integrates with Hashicorp Vault for enterprise-grade secret management, supporting both single-tenant and multi-tenant deployments with logical isolation.

This section covers secret storage architecture, key derivation, seed generation using BIP32/BIP39, and operational workflows for managing secrets across different deployment scenarios.

---

## Secret storage

**Status**: draft

**Date**: 2021-06-02

**NOTE**: this document is a draft and is not implemented yet. Statement in this document might be changed in the future.

## Introduction

Secrets are sensitive data that should be stored securely. There are following types of the secrets in the Identus Platform:

- seed: a secret used to derive cryptographic keys  
- private key: a secret used to sign data  
- any other entities that contain sensitive data (for instance, `credential-definition` and the `link-secret` used by the AnonCreds)

**NOTE**: public keys are not considered as secrets and can be stored in the same of other storage depending on the needs

The Identus Platform provides a secure storage for secrets. Hashicorp Vault is used as a secret storage service and provides a REST API, Web UI and command client to interact with the service.

**NOTE:** The Identus Platform uses a single Vault instance for all tenants per environment. Logical data separation is achieved by using Vault namespaces and policies applied to the tenant.

## Terminology

### Vault

Vault is a secrets management service developed by HashiCorp. It can be used as the default secret storage for the Identus Platform as well as for authentication and account management.

**NOTE**: The Identus Platform must not be dependent on the Vault service and must be able to use other services for the same purposes

### Agent

The Cloud agent is a service that provides an APIs to interact with the Identus Platform and use the SSI capabilities.

### Wallet

Logical component of the Agent that holds secrets and provides the logical or physical isolation of the data.

## Technical overview

### Cloud agent logical isolation

Each instance of the Cloud agent needs to have access to the secrets but must be isolated from other agents at the same environment. For horizontal scalability the group of agents can be configured to share the same namespace, so they can access the same secrets, but they still need to use different Vault account to authenticate themselves to the Vault service.

### Cloud agent authentication

Each instance of the Cloud agent needs to authenticate itself to the Vault service. The Vault service uses a token-based authentication mechanism. The Cloud agent uses a Vault [AppRole](https://developer.hashicorp.com/vault/docs/auth/approle) authentication method to authenticate itself to the Vault service. The token issued to the agent has the expiration time set in the application configuration. After the token expires, the agent needs to re-authenticate itself to the Vault service.

### Wallet authentication

Each instance of the Wallet needs to authenticate itself to the Vault service. The Cloud agent issues the authentication token to the Wallet based on the tenant ID.

### Secrets engine configuration

The Vault service uses a secrets engine to store secrets. KV2 secrets engine is used to store secrets in the Vault service and provides the following features:

- secrets are encrypted at rest  
- secrets are encrypted in transit  
- secrets are versioned  
- secrets can be deleted, restored and rolled back to a previous version  
- secrets are available via REST API, WEB UI, and command client  
- secrets are logically separated by tenants

### Single and multi-tenant configuration

The Identus Platform supports single and multi-tenant configurations. In the single-tenant configuration, the Cloud agent uses a single Wallet and a single Vault account to authenticate itself to the Vault service. In the multi-tenant configuration, the Cloud agent manages multiple Wallets, each Wallet is associated with a single tenant. Multi-tenant configuration is used to achieve logical data separation between tenants, so each Wallet can access only its own secrets. The Wallet is identified by the tenant ID and represented by the account in the Vault service.

The common sequence diagram for the single and multi-tenant configurations is shown below:

```
sequenceDiagram
    Tenant->>+Agent: REST/DIDComm API
    Agent->>+Wallet: route the request
    Wallet->>+Vault: manage secrets
    Vault->>+Wallet: secrets
    Wallet->>+Wallet: business logic
    Wallet->>+Agent: route the response
    Agent->>+Tenant: REST/DIDComm API
```

### Key derivation

The Identus Platform uses HD key derivation to derive cryptographic keys from the seed. The Wallet is initialized with the seed and uses it to derive cryptographic keys for managed DIDs. Key derivation path is conventional and is defined as follows:

```
m / wallet-purpose / DID-index / key-purpose / key-index
```

### Naming convention

To store the assets in the Vault service each asset is assigned a unique name. The Vault is a key/value store with metadata attached to the key and versioning.

The naming convention for the Vault assets is a matter of the implementation, but for the multi-tenant configuration all the assets of the Wallet must be stored under the path that contains the `tenant-id`.

For example, the `seed` can be stored by the following path:

```
<tenant-id>/seed value=<base64-encoded-value> <metadata>
```

The private keys for the DID can be stored by the following path:

```
<tenant-id>/dids/prism/<did-ref>/keys/<key-purpose>/<key-index>/<operation-hash> value=<base64-encoded-value> <metadata>
```

where:

- `tenant-id` is the tenant ID assigned to the Wallet  
- `did-ref` is the DID ref  
- `key-purpose` is the key purpose according to the PRISM DID Method specification  
- `key-index` is the key index. Starts from 0 and grows incrementally after each key rotation  
- `operation-hash` is the reference to the update DID document operation  
- `base64-encoded-value` is the base64-encoded value of the key  
- `metadata` is the key/value metadata attached the key that can be used to store additional information about the key such as `seed` or `key-derivation-path`

The keys material of the DID peer can be stored by the following path:

```
<tenant-id>/dids/peer/<did-ref>/keys/<key-purpose> value=<base64-encoded-value> <metadata>
```

Links:

- [PRISM DID Method Specification](https://github.com/input-output-hk/prism-did-method-spec)  
- [Vault Secrets Engine API](https://www.vaultproject.io/api/secret/kv/kv-v2)  
- [Vault Namespaces](https://www.vaultproject.io/docs/concepts/namespaces)  
- [HD Key Derivation ADR](https://github.com/input-output-hk/atala-prism-building-blocks/blob/main/docs/decisions/20230516-hierarchical-deterministic-key-generation-algorithm.md)  
- [Vault AppRole Authentication](https://www.vaultproject.io/docs/auth/approle)  
- [Vault Token Authentication](https://www.vaultproject.io/docs/auth/token)  
- [Vault KV Secrets Engine](https://www.vaultproject.io/docs/secrets/kv/kv-v2)  
- [Vault Entities](https://www.vaultproject.io/docs/concepts/entities)

---

## Secret operations

## Introduction

The Cloud agent offers a DID (Decentralized Identifier) management solution which involves creating, storing and using key materials. To generate a DID key material, the software relies on a seed, following the BIP32 / BIP39 standards. The system operators have the option to either provide their own seed or allow the software to generate one automatically. However, in a production environment, it is crucial for the system operators to explicitly supply the seed to the agent. This ensures full control over the DID key material and guarantees secure management of user identities.

The Cloud agent uses the following environment variables for secret management.

| Name | Description | Default |
| :---- | :---- | :---- |
| `SECRET_STORAGE_BACKEND` | The storage backend that will be used for the secret storage | `vault` |
| `VAULT_ADDR` | The address which the Cloud agent can reach the Vault | `http://localhost:8200` |
| `VAULT_TOKEN` | The token for accessing HashiCorp Vault | \- |
| `VAULT_APPROLE_ROLE_ID` | The `role_id` for HashiCorp Vault authentication with AppRole | \- |
| `VAULT_APPROLE_SECRET_ID` | The `secret_id` for HashiCorp Vault authentication with AppRole | \- |
| `VAULT_USE_SEMANTIC_PATH` | Enable full path convention for vault secret path | true |
| `DEFAULT_WALLET_SEED` | The seed used for DID key management for the default wallet | \- |

## Storage backend configuration

Secret storage supports various backends like the Vault service or Postgres database. By default, the backend chosen for secret storage is Vault, which is suitable for production environments. There are multiple supported backend implementations, each catering to specific use cases.

### HashiCorp Vault

When operating in a production environment, the agent has the option to utilize Vault as a secure secret storage backend. This choice is deemed suitable for production because all data is encrypted and it also offers additional security-related capabilities. By default, the agent uses this backend but the option is configurable. To utilize this backend, set the `SECRET_STORAGE_BACKEND` variable to `vault`.

**Authentication and Authorization**

The agent expects to read and write secrets to the path `/secret/*`, so ensure the appropriate permissions are provisioned.

Example Vault policy

```
path "secret/*" {
    capabilities = ["create", "read", "update", "patch", "delete", "list"]
}
```

HashiCorp Vault provides multiple authentication methods. One of the simplest methods is [token authentication](https://developer.hashicorp.com/vault/docs/auth/token). To authenticate using the token, set the environment variable `VAULT_TOKEN`. The agent prefers token authentication if provided with multiple authentication methods.

Another method is [AppRole authentication](https://developer.hashicorp.com/vault/docs/auth/approle) which is suitable for automatic workflows. To use AppRole authentication, set the environment variable `VAULT_APPROLE_ROLE_ID` and `VAULT_APPROLE_SECRET_ID`.

**Storage Backend**

HashiCorp Vault supports multiple backends for storage, such as filesystem, Etcd, PostgreSQL, or Integrated Storage (Raft). Each backend has different properties, which have implications for how secrets can be stored. The agent logically stores secrets in the following hierarchies.

```
# Wallet seed
/secret/<wallet-id>/seed

# Peer DID keys
/secret/<wallet-id>/dids/peer/<peer-did>/keys/<key-id>

# Generic secrets
/secret/<wallet-id>/generic-secrets/<specific-path>
```

Each storage backend has certain limitations, such as size, number of sub-paths, or path length. Some backends can support path lengths of up to 32,768 characters, while others only allow a few hundred characters. In some cases, the storage backends might not support the above logical convention due to excessively long paths.

To address this issue, the agent supports path shortening. This feature can be toggled by setting the environment variable `VAULT_USE_SEMANTIC_PATH=false`. When it is disabled, the unbounded portion of the path will be replaced by a SHA-256 digest of the original relative path. Additionally, the original path will be stored in the secret metadata.

### Postgres

Postgres is an alternative backend option for secret storage. However, this option must be explicitly chosen and will replace Vault. By opting for Postgres, there is no need for an additional service like Vault, which simplifies the process of setting up a local development instance. It utilizes the same database instance as the agent itself. To enable this option, set the `SECRET_STORAGE_BACKEND` to `postgres`, and it will utilize the same database configuration as the agent's database. It is important to note that while this option facilitates an easier development experience, it does not provide a secure method of storing secrets. The data is not encrypted, making it unsuitable for production use.

---

## Seed generation

## Introduction

The Cloud agent utilizes a hierarchical-deterministic key derivation algorithm for managing PRISM DIDs, which follows the BIP32 standard. In order to generate the required keys, BIP32 uses a master binary seed serving as the root of the derivation tree, and all other keys are derived from this seed. Given that the Cloud agent employs BIP32, it expects a 64-byte binary seed as input. Various methods exist for generating a byte sequence, each serving different purposes.

The Cloud agent does not have any opinion on how the seed should be generated as long as a valid hex string is provided. However, it is strongly recommended to use high entropy for generating the master seed. The Cloud agent allows customizing the default wallet seed by using the environment variable `DEFAULT_WALLET_SEED`. Other wallet seeds can also be configured when creating a wallet using REST API. The variable must contain a 64-byte value encoded in hexadecimal format.

### Static seed

The Cloud agent expects any valid 64-byte input for a wallet seed. Any static 128-character hexadecimal string can be used to simplify the testing.

For example

```shell
# Any of these are valid
DEFAULT_WALLET_SEED=00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000
DEFAULT_WALLET_SEED=11111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111111
DEFAULT_WALLET_SEED=0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a0a
```

**Note: Do not use method this in production.**

### Simple dynamic seed

Users have the option to create a random hexadecimal string of a desired length using scripting languages like Bash or Python. An example of a Bash script is shown below.

```shell
DEFAULT_WALLET_SEED=$(tr -dc a-f0-9 </dev/urandom | head -c 128 ; echo '')
```

This approach is suitable for basic testing scenarios requiring dynamically generated seeds. However, for production use, it is advisable to employ a reputable implementation of BIP39 with a high level of entropy. (Refer to the details below for further information.)

### Use BIP39 implementation to generate a seed (recommended)

The [BIP39](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki#user-content-From_mnemonic_to_seed) protocol can be utilized to generate a BIP32 master seed, offering a human-friendly approach to seed management. Instead of noting down a seemingly random hexadecimal string, users can write down their mnemonic phrase, making it more convenient to keep track of them.

By using BIP39, users have options to choose a mnemonic phrase length as well as a passphrase. There are many tools for generating a BIP39 seed including but not limited to:

- [BIP39](https://iancoleman.io/bip39/) (use the BIP39 seed field which provides a 128-chars hex string)  
- [BIP39 \- implementations section](https://github.com/bitcoin/bips/blob/master/bip-0039.mediawiki#other-implementations) also provides a list of implementations

---

## Secret storage flows

**Status**: draft

**Date**: 2021-06-02

**NOTE**: this document is a draft and is not implemented yet. Statement in this document might be changed in the future.

## Introduction

The current document describes the sequence diagrams of the Identus Platform components: APISIX, Cloud agent, Wallet, Vault, Tenant. The diagrams are stated from the simplest scenarios to the more complex ones to enforce the security and privacy of the data.

## Sequence diagrams

### Phase \#1: Basic Flow for Secret Management

This diagram describes the basic flow for the secret management.

Goal : the Agent stores the secrets using the `root` token to access the Vault service

Context:

- Tenant uses the access token to access the REST API via APISIX.  
- The Agent uses root Vault token to communicate with the Vault.  
- Tenant represented by any REST API client, Web or Mobile application.

```
sequenceDiagram
    actor User
    User ->> Application: run
    Application->>+APISIX: Agent REST API request + API token
    loop
        APISIX->>APISIX: validate API token
    end
    APISIX->>+Agent: Agent REST API request
    activate Agent
    loop Manage Secrets
        Agent->>+Vault: Vault REST API request
        Vault->>+Agent: Vault REST API response
    end
    Agent->>+Agent: Execute Business Logic
    Agent->>+APISIX: Agent REST API response
    deactivate Agent
    APISIX->>+Application: Agent REST API response
    Application->>+ User: react
```

### Phase \#2: Single Tenant Flow for Secret Management

This diagram describes the flow for the secret management for the single tenant.

Goal: AppRole authentication method is used to authenticate the Agent to the Vault service.

Context:

- The Agent is authenticated to the Vault using the AppRole authentication method.  
- Tenant uses the access token to access the REST API via APISIX.  
- Tenant represented by any REST API client, Web or Mobile application.

```
sequenceDiagram
    actor User
    User ->> Application: run
    Application->>+APISIX: Agent REST API request + API token
    loop
        APISIX->>APISIX: validate API token
    end
    APISIX->>+Agent: Agent REST API request
    activate Agent
    loop Authentication
        Agent->>+Vault: Get auth token
        Vault->>+Agent: Return auth token
    end
    loop Manage Secrets
        Agent->>+Vault: Vault REST API request
        Vault->>+Agent: Vault REST API response
    end
    Agent->>+Agent: Execute Business Logic
    Agent->>+APISIX: Agent REST API response
    deactivate Agent
    APISIX->>+Application: Agent REST API response
    Application->>+ User: react
```

### Phase \#3: Single Tenant Flow for Secret Management with Wallet

This diagram describes the flow for the secret management for the single tenant with the Wallet.

Goal: Tenant uses JWT token to authenticate to the Identus Platform.

Context:

- The Agent is authenticated to the Vault using the AppRole authentication method.  
- Tenant uses the access token to access the REST API via APISIX (probably this might be removed, we need to decide what to do with the `api-token`)  
- Tenant represented by any REST API client, Web or Mobile application authenticated to the Identus Platform using JWT token.  
- Tenant uses the Wallet to communicate with the Vault

```
sequenceDiagram
    participant User
    participant Application
    participant APISIX
    participant Agent
    participant Wallet
    participant Agent
    participant Wallet
    participant Vault
    actor User

    User->>+Application: run
    loop Authentication
        Application ->> APISIX: get jwt-token
        APISIX ->> Vault: get jwt-token
        Vault ->> APISIX: jwt-token
        APISIX ->> Application: jwt-token
    end
    Application->>+APISIX: REST API + api-token + jwt-token
    loop
        APISIX->>APISIX: validate api-token
    end
    APISIX->>+Agent: Agent REST API request + jwt-token
    Agent ->>+ Wallet: handle request
    loop Manage Secrets
        Wallet->>+Vault: Vault REST API request
        Vault->>+Wallet: Vault REST API response
        Wallet->>+Wallet: Execute Business Logic
    end
    Wallet ->>+ Agent: response
    Agent ->>+ APISIX: Agent REST API response
    APISIX ->>+ Application: Agent REST API response
    Application ->>+ User: react
```

### Phase \#4: Multi Tenant Flow for Secret Management

This diagram describes the flow for the secret management for the multi tenant.

## //TBD

# VDR management {#vdr-management}

## Overview

The Verifiable Data Registry (VDR) provides a storage-agnostic interface for managing data referenced in SSI protocols. VDR entries can store arbitrary data (such as credential status lists) and be resolved using VDR URIs. This abstraction allows switching storage implementations without changing the integration interface.

This tutorial demonstrates creating, resolving, and deactivating VDR entries using the Cloud agent's HTTP binding to the VDR specification.

---

The [VDR specification](https://github.com/hyperledger-identus/vdr) defines a mechanism for creating, updating, deactivating, and retrieving data from various data sources. These sources are abstracted, enabling a unified integration interface for other systems. A reference implementation of the VDR is available as a library [here](https://github.com/hyperledger-identus/vdr), and an HTTP binding is also provided within the Cloud agent.

The purpose of the VDR is to store data used across various SSI protocols. In many use cases, these protocols need to reference external resources using URIs. For example: credential status lists. Since the interface is storage-agnostic, switching the underlying storage implementation is trivial.

VDR is capable of storing arbitrary bytes. In this tutorial, we will create a sample binary data and resolve the VDR to compare those bytes. Then we will try to deactivate the data and observe the resolution failure.

## Roles

1. The **data owner** \- is responsible for managing the VDR entries and their lifecycle.

**Example**  
In this example, we will store data using a database driver to enable easy setup and testing.

## Endpoints

The example uses the following endpoints

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| `GET /vdr/entires` | Resolve the data using VDR URI | Anyone |
| `POST /vdr/entries` | Create a new VDR entry | Data owner |
| `DELETE /vdr/entries` | Delete the VDR entry | Data owner |

### Create a sample binary data to store

```shell
echo -ne '\x01\x02\x03\x04' > sample_in.bin
```

We should have a file named `sample_in.bin` containing 4 bytes

### Create a new VDR entry with sample data

```shell
curl -X POST "http://localhost:8080/cloud-agent/vdr/entries?drid=database" \
  -H "Content-Type: application/octet-stream" \
  --data-binary @sample_in.bin
```

The response should look something like

```json
{
  "url":"vdr://?drf=memory&drid=memory&drv=0.1.0&m=0#d63bdd21-0347-4caf-a255-0cca7c2851fe"
}
```

The URL is the locator for the VDR entry. If referenced in the SSI protocol, anyone should be able to resolve this data. In this example, we are using only a database driver, so the data is resolvable only within the same agent instance.

### Resolve the data using VDR URL

Take the URL from previous step and run this command to resolve the URL.

```shell
curl -X GET "http://localhost:8080/cloud-agent/vdr/entries?url=<ENCODED_VDR_URL>" > sample_out.bin
```

Remember to encode the URL using percent encoding to escape any reserved characters in the URL syntax. If executed successfully, the response status should be 200, with the response body saved to a file named `sample_out.bin`.

### Compare the VDR entry and the input

```shell
diff sample_in.bin sample_out.bin
```

The output of the `diff` command should be empty, as the VDR entry is exactly the same as the input data.

### Deactivate the VDR entry

```shell
curl -X DELETE "http://localhost:8080/cloud-agent/vdr/entries?url=<ENCODED_VDR_URL>"
```

Similar to resolving the data, we now change the HTTP method to `DELETE` to deactivate the VDR entry. The response status should be 200, indicating a successful operation.

After trying to resolve the VDR entry in step 3, the response should be

```json
{
  "status":404,
  "type":"error:VdrServiceError:VdrEntryNotFound",
  "title":"Vdr Entry Not Found",
  "detail":"The data could not be found from a provided URL",
  "instance":"error:instance:b70984a1-71f5-409f-9afc-3b8bada54445"
}
```

indicating the resource is no longer available for resolution.

---

# Webhooks {#webhooks}

## Overview

Webhooks enable real-time event notifications from the Cloud agent to your application. Instead of polling for status changes, your application receives HTTP callbacks whenever connections are established, credentials are issued, or proofs are verified. This section explains webhook configuration, event types, and implementation patterns for processing notifications.

---

## Introduction

Welcome to the tutorial on webhook notifications in the Cloud agent. In this tutorial, we will explore how webhook notifications can enhance your experience with the Cloud agent by providing real-time updates on events. By leveraging webhook notifications, you can stay informed about important changes happening within the agent.

## Understanding webhook notifications

### What are webhooks?

Webhooks enable real-time communication between applications by sending HTTP requests containing event data to specified endpoints (webhook URLs) when events occur. They establish a direct communication channel, allowing applications to receive instant updates and respond in a timely manner, promoting efficient integration between event-driven systems.

### Purpose of webhook notifications in the Cloud agent

Webhook notifications in the Cloud agent serve as a vital feature, enabling you to receive timely updates on various events occurring within the agent. Webhooks allow you to receive HTTP requests containing event details at a specified endpoint (webhook URL). These events are specifically related to the execution of the [Connect](https://hyperledger-identus.github.io/docs/tutorials/connections/connection), [Issue](https://hyperledger-identus.github.io/docs/tutorials/credentials/didcomm/issue), and [Presentation](https://hyperledger-identus.github.io/docs/tutorials/credentials/didcomm/present-proof) flows. Webhook notifications will be sent each time there is a state change during the execution of these protocols.

By leveraging webhooks, you can integrate the Cloud agent seamlessly into your applications and systems. You can track and monitor the progress of the main flows, receiving timely updates about changes and events.

## Configuring the webhook feature

### Enabling the webhook feature

There are two kinds of webhook notifications: global webhooks and wallet webhooks. Global webhooks capture all events that happen on the Cloud agent across all wallets, whereas wallet webhooks only capture events that are specific to assets within a particular wallet.

**Enable global webhook using environment variables**

The Cloud agent uses the following environment variables to manage global webhook notifications:

| Name | Description | Default |
| :---- | :---- | :---- |
| `GLOBAL_WEBHOOK_URL` | The webhook endpoint URL where the notifications will be sent | null |
| `GLOBAL_WEBHOOK_API_KEY` | The optional API key (bearer token) to use as the `Authorization` header | null |

**Enable wallet webhook for default wallet using environment variables**

In a multi-tenant scenario, the Cloud agent can optionally create a default wallet to simplify the development and deployment process. The webhook configuration for this default wallet can be defined using environment variables. After the default wallet is created, its webhook settings are stored in the system and are no longer influenced by these environment variables.

| Name | Description | Default |
| :---- | :---- | :---- |
| `DEFAULT_WALLET_ENABLED` | Automatically create default on the Cloud agent startup | true |
| `DEFAULT_WALLET_WEBHOOK_URL` | The webhook endpoint URL where the notifications will be sent | null |
| `DEFAULT_WALLET_WEBHOOK_API_KEY` | The optional API key (bearer token) to use as the `Authorization` header | null |

**Enable wallet hook using REST API**

In a multi-tenant scenario, there is an option to configure wallet webhook parameters using a REST API, which offers more flexibility. For each individual wallet, users can create a new webhook by making a POST request to `/events/webhooks`, which in turn creates a new webhook resource specific to their wallet.

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

### Securing the webhook endpoint

It is essential to secure the webhook endpoint to protect the integrity and confidentiality of the event data. Consider the following best practices when securing your webhook endpoint:

- Use HTTPS to encrypt communication between the Cloud agent and the webhook endpoint.  
- Implement authentication mechanisms (e.g., API keys, tokens) to verify the authenticity of incoming requests.  
- Validate and sanitize incoming webhook requests to mitigate potential security risks.

One of the authorization mechanism for the Cloud agent's webhook notifications is the bearer token. If configured, the token will be included in the `Authorization` header of the HTTP request sent by the agent to the webhook endpoint. You can configure this bearer token by setting the value of the `GLOBAL_WEBHOOK_API_KEY` or `DEFAULT_WALLET_WEBHOOK_API_KEY` environment variable.

An alternative approach is to make use of the `customHeaders` property within the REST API for configuring webhooks. This option offers increased flexibility when custom or multiple headers are needed.

## Event format and types

### Event format

Webhook notifications from the Cloud agent are sent as JSON payloads in the HTTP requests.

The event format is consistent across all events. Each event follows a common structure, while the 'data' field within the event payload contains information specific to the type of event. Here is an example of the JSON payload format:

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

This event format ensures consistency and allows you to handle webhook notifications uniformly while easily extracting the relevant data specific to each event type from the `data` field.

Here is a complete example of a webhook notification event related to a connection flow state change (invitation generated):

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

The Cloud agent sends webhook notifications for events related to protocol state changes in the [Connect](https://hyperledger-identus.github.io/docs/tutorials/connections/connection), [Issue](https://hyperledger-identus.github.io/docs/tutorials/credentials/didcomm/issue), [Presentation](https://hyperledger-identus.github.io/docs/tutorials/credentials/didcomm/present-proof) flows, and also [DID publication](https://hyperledger-identus.github.io/docs/tutorials/dids/publish) state changes. These events allow you to track the progress and updates within these flows in real-time.

The `id` field of the common event structure is the unique identifier (UUID) of the event and is randomly generated at event creation time.

The `ts` field contains the timestamp (date \+ time) at which the event was created.

The `walletId` field contains information about the wallet from which the event originates.

The `type` field indicates to which flow/process the received event is related, and hence the type of JSON payload that can be expected in the inner `data` field. Possible values are:

| Value | Description |
| :---- | :---- |
| `ConnectionUpdated` | An update in the connection flow state |
| `IssueCredentialRecordUpdated` | An update in the VC issuance flow state |
| `PresentationUpdated` | An update in the VC presentation flow state |
| `DIDStatusUpdated` | An update in the DID publication state |

State change notifications that you can expect to receive through webhook notifications include:

- Connection State Change: Notifies about state changes in the connection flow, such as `InvitationGenerated`, `ConnectionRequestSent`, `ConnectionResponseReceived`, etc. Please refer to the `state` field of the [connection resource](https://hyperledger-identus.github.io/docs/agent-api/#tag/Connections-Management/operation/getConnection) for an exhaustive list of states.  
- Credential State Change: Indicates changes in the credential issuance flow, such as `OfferSent`, `RequestReceived`, `CredentialSent`, etc. Please refer to the `protocolState` field of the [credential resource](https://hyperledger-identus.github.io/docs/agent-api/#tag/Issue-Credentials-Protocol/operation/getCredentialRecord) for an exhaustive list of states.  
- Presentation State Change: Notifies about changes in the presentation flow, such as `RequestReceived`, `PresentationGenerated`, `PresentationVerified`, etc. Please refer to the `status` field of the [presentation resource](https://hyperledger-identus.github.io/docs/agent-api/#tag/Present-Proof/operation/getPresentation) for an exhaustive list of states.  
- DID State Change: Notifies about DID-related state changes. Currently, only the `Published` DID publication state event will be notified.

## Processing webhook notifications

### Handling incoming webhook requests

To handle incoming webhook notifications from the Cloud agent in your application, follow these general steps:

1. Receive the HTTP request at your specified webhook endpoint.  
2. Parse the JSON payload of the request to extract the event details.  
3. Process the event data according to your application's requirements.  
4. Send a response back to acknowledge the successful receipt of the webhook notification. For a successful reception, the response status code should be `>= 200` and `< 300`. Any other response status code will lead to a new attempt from the Cloud agent.

### Error handling and retry mechanisms

When working with webhook notifications in the Cloud agent, it is important to consider error handling and retry mechanisms. In case of failed webhook notifications or errors, the Cloud agent employs an automatic retry mechanism to ensure delivery. The agent will attempt to send the webhook notification up to three times, with a five-second interval between each attempt. Please note that the number of retries and the interval duration are currently not configurable in the Cloud agent.

By default, this retry mechanism provides a reasonable level of reliability for delivering webhook notifications, allowing for temporary network issues or intermittent failures.

### A basic Webhook implementation for logging requests

In the following example, we will demonstrate a simple Python code snippet that sets up a webhook endpoint and logs incoming HTTP requests to the console. This basic implementation can serve as a starting point for building more advanced webhook systems.

In the provided Python code snippet, the port on which the webhook listener will listen for incoming requests should be passed as a command-line parameter. This allows flexibility in starting multiple webhooks in parallel, which is useful when testing multiple locally running agents, e.g. for a holder, an issuer, and/or a verifier.

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

Congratulations\! You've learned about webhook notifications in the Cloud agent. By leveraging this feature, you can receive real-time updates on events happening within the agent, enabling you to integrate the Cloud agent seamlessly into your applications. Remember to secure your webhook endpoint and handle webhook notifications effectively to maximize the benefits of this feature.

Start integrating webhook notifications into your Cloud agent workflow and unlock the power of real-time event updates\!

## If you have any further questions or need assistance, don't hesitate to reach out to the Identus support team or refer to the official documentation for more details.

# Troubleshooting and considerations {#troubleshooting-and-considerations}

## Overview

This section provides solutions to common issues and operational best practices for running Hyperledger Identus in development and production environments.

---

## Docker logging management considerations

When setting up a long-running environment using Docker Compose, it’s important to consider several factors to avoid issues such as excessive log file sizes leading to out-of-memory errors.

### Configuring Docker Compose for effective log management

To ensure your Docker containers run smoothly and avoid problems related to excessive log file growth, configure log rotation in your docker-compose.yml file. This will help manage log file sizes and prevent out-of-memory errors caused by uncontrolled log growth.

### Log rotation example

Here’s a example in the mediator how to set up log rotation in your Docker Compose configuration:

1. Open your docker-compose.yml file.  
2. Add or update the logging configuration under your service definition. For example:

```
version: '3.8'

services:
  identus-mediator:
    image: ghcr.io/input-output-hk/atala-prism-mediator:0.14.2
    logging:
      driver: json-file
      options:
        max-size: "10m"
        max-file: "3"
```

- `driver`: Specifies the logging driver to use. The json-file driver is the default and supports log rotation options.  
- `max-size`: Sets the maximum size of the log file before it gets rotated. In the example above, the log file will be rotated when it reaches 10 MB.  
- `max-file`: Determines the number of rotated log files to keep. In this example, up to 3 log files will be kept before old files are deleted.

```shell
docker-compose up -d
```

3. Save the changes to your `docker-compose.yml` file and **Restart** your Docker containers to apply the new logging configuration.

### Configuring Docker Daemon for effective log management

We should consider configuring the logging Options in the **Docker Daemon**. For a global logging configuration applicable to all Docker containers. We can modify the Docker daemon settings. This approach ensures consistent log management across all containers.

1. Edit the Docker daemon configuration file (usually located at /etc/docker/daemon.json). If the file doesn’t exist, you can create it.  
2. Add or update the logging options in the `daemon.json` file:

```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

3. Restart the Docker daemon to apply the new settings:

```shell
sudo systemctl restart docker
```

**Docker logging drivers**

For more information see [https://docs.docker.com/engine/logging/configure/\#supported-logging-drivers](https://docs.docker.com/engine/logging/configure/#supported-logging-drivers)  
