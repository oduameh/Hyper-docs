# Quick start guide {#quick-start-guide}

## Introduction to self-sovereign identity (SSI)

[Self-sovereign identity (SSI)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#self-sovereign-identity) is complex. This guide highlights the essential Hyperledger Identus concepts so you can create [decentralized identifiers (DIDs)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#decentralized-identifier), issue credentials, make connections, and verify credentials with [verifiable presentations](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifiable-presentation). For deeper explanations, review the Concepts and Components sections.

The trust triangle is the core process for conveying digital trust. Each SSI ecosystem includes three roles: [Holders](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#holder), [Issuers](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#issuer), and [Verifiers](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifier).

Holders can be any entity, such as individuals, organizations, or digital and physical things. They store [verifiable credentials (VCs)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifiable-credential) and share them by creating verifiable presentations.

Issuers can also be any entity that makes [claims](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#claim) about an [entity](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#entity). These claims are attestations, or evidence of something, about the holder. For example, an insurance company can provide proof of valid insurance.

Verifiers are the [relying party](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#relying-party) in the triangle. They request information from the holder, such as proof of insurance, and the holder responds with a verifiable presentation that contains the relevant VCs. The verifier checks the holder's digital signature, verifies the issuer DID, and reviews the credential contents to ensure nothing has been tampered with.

## Hyperledger Identus flow

The diagram details how the concepts fit alongside the Identus components in a typical SSI interaction.

## Hyperledger Identus components

Identus consists of core libraries that facilitate typical SSI interactions between [Issuers](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#issuer), [Holders](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#holder), and [Verifiers](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifier).

### Cloud agent

A Cloud agent can issue, hold, and verify [verifiable credentials (VCs)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifiable-credential) for any entity and manage [decentralized identifiers (DIDs)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#decentralized-identifier) and DID-based connections. The Cloud agent provides a REST API for straightforward integration into any solution and uses [DIDComm V2](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#didcomm) for Cloud agent-to-Cloud agent communication.

It is maintained as an open-source project through [Hyperledger Identus](https://www.hyperledger.org/projects/identus).

For additional context, review the [Cloud agent overview](https://hyperledger-identus.github.io/docs/home/identus/cloud-agent/overview).

### Wallet SDKs

[Wallet SDKs](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#wallet-sdk) for web and mobile (iOS, Android, TypeScript) enable identity holders to store credentials and respond to proof requests. They are typically used in applications that allow identity holders to interact with issuers and verifiers.

Explore the detailed documentation for each SDK: [TypeScript](https://hyperledger-identus.github.io/docs/sdk-ts/sdk/), [Swift](https://hyperledger-identus.github.io/sdk-swift/documentation/edgeagentsdk/), and [KMP](https://hyperledger-identus.github.io/sdk-kmp/).

### Mediator

[Mediators](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#mediator) store and relay messages between Cloud agents and Wallet SDKs. They act as a proxy that remains connected to the network and receives any message, credential, or proof request on behalf of the Wallet SDKs, which can occasionally be offline.

Learn more in the [Mediator documentation](https://hyperledger-identus.github.io/docs/home/identus/mediator).

**Node for a Verifiable Data Registry (VDR)**
To issue and verify VCs with DIDs, we need a [Verifiable Data Registry (VDR)](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#verifiable-data-registry) that is globally resolvable and always on. In Identus's case, it is `prism-node`, [anchoring](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#anchoring) key information required for issuance and verification on the distributed ledger.

## Prerequisites

### Agent deployment

This guide demonstrates a single-tenant deployment with API Key authentication disabled and an in-memory ledger for published DID storage, which is the simplest configuration for new developers. For advanced configurations, review the [Multi-Tenancy Management](https://hyperledger-identus.github.io/docs/tutorials/multitenancy/tenant-onboarding) tutorial and the associated [environment variables](https://hyperledger-identus.github.io/docs/home/identus/cloud-agent/environment-variables).

Develop on modern machines equipped with either Intel-based x64 processors or Apple ARM processors with a minimum of four cores, 16 GB of memory, and at least 128 GB of SSD storage.

1. Prepare to spin up a Cloud agent. Ensure that you:
   * Have Git installed
   * Have Docker installed
   * Clone the [Identus Cloud agent repository](https://github.com/hyperledger/identus-cloud-agent)

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

4. Setting the `API_KEY_ENABLED` parameter to `false` disables the requirement of using API keys.

:::caution

API\_KEY\_ENABLED disables API Key authentication. This should **not** be used beyond Development purposes.

:::

5. Start the `issuer` and `verifier` Cloud agents by running the following commands.
* Issuer Cloud agent:

macOS terminal shell

```shell
 ./infrastructure/local/run.sh -n issuer -b -e ./infrastructure/local/.env-issuer -p 8000 -d "$(ipconfig getifaddr $(route get default | grep interface | awk '{print $2}'))"
```

Linux terminal shell

```shell
 ./infrastructure/local/run.sh -n issuer -b -e ./infrastructure/local/.env-issuer -p 8000 -d "$(ip addr show $(ip route show default | awk '/default/ {print $5}') | grep 'inet ' | awk '{print $2}' | cut -d/ -f1)"
```

* The Issuer [API endpoint](http://localhost:8000/cloud-agent/) is accessible on port 8000 at `http://localhost:8000/cloud-agent/` with a [Swagger interface](http://localhost:8000/cloud-agent/redoc) available at `http://localhost:8000/cloud-agent/redoc`.
    
* Verifier Cloud agent:

For macOS terminal shell

```shell
 ./infrastructure/local/run.sh -n verifier -b -e ./infrastructure/local/.env-verifier -p 9000 -d "$(ipconfig getifaddr $(route get default | grep interface | awk '{print $2}'))"
```

For the Linux terminal shell

```shell
 ./infrastructure/local/run.sh -n verifier -b -e ./infrastructure/local/.env-verifier -p 9000 -d "$(ip addr show $(ip route show default | awk '/default/ {print $5}') | grep 'inet ' | awk '{print $2}' | cut -d/ -f1)"
```

* The Verifier [API endpoint](http://localhost:9000/cloud-agent/) is accessible on port 9000 at `http://localhost:9000/cloud-agent/` with a [Swagger interface](http://localhost:9000/cloud-agent/redoc) available at `http://localhost:9000/cloud-agent/redoc`.

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

3. The short version of the DID is the `publishedPrismDID`.

:::info

📖Learn more about PRISM DIDs and why it is necessary to publish specific DIDs [here](https://staging-docs.atalaprism.io/tutorials/dids/publish).

:::

**Create a credential schema (JWT W3C Credential)**

1. To create a [credential schema](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#credential-schema) on the Issuer API instance, run the following request:

:::info

Replace the `[[publishedPrismDID]]` in the example request with the `did` value from the previous step.

:::

2. Capture the schema GUID because it is used in later steps.

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

All wallet SDKs come bundled with a sample application that covers the Identus flows, including establishing connections, issuance, and verification.

1. Clone the [TypeScript SDK](https://github.com/hyperledger-identus/sdk-ts) repository.

```shell
git clone -b v6.6.0 https://github.com/hyperledger-identus/sdk-ts
```

2. Ensure you install all applications required to build the SDK and its dependencies.

[rust](https://www.rust-lang.org/tools/install) and [wasm-pack](https://rustwasm.github.io/wasm-pack/installer/) are leveraged to build and use the AnonCreds and DIDComm Rust libraries within TypeScript. To build the SDK locally or run demonstration applications, you must have these applications installed.

The following commands work on Linux and macOS. If you experience any issues, refer to the latest installation instructions for your platform.

```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
curl https://rustwasm.github.io/wasm-pack/installer/init.sh -sSf | sh
```

3. Run the following commands:
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

* This command starts the React Wallet SDK TypeScript demonstration at [http://localhost:3000](http://localhost:3000).
1. Clone the [Swift SDK](https://github.com/hyperledger/identus-edge-agent-sdk-swift) repository.

```shell
git clone https://github.com/hyperledger/identus-edge-agent-sdk-swift
```

2. Open the Xcode project at **./Sample/AtalaPrismWalletDemo/AtalaPrismWalletDemo.xcodeproj**.
3. In the top-left corner of the Xcode window, click the Play/Run button.
4. The app will start.
5. Click Wallet Demo 2.0
* You will be able to run the rest of the guide here.
1. Clone the [KMM SDK](https://github.com/hyperledger/identus-edge-agent-sdk-kmm) repository.

```shell
git clone https://github.com/hyperledger/identus-edge-agent-sdk-kmm
```

2. Open the Wallet SDK project in IntelliJ IDEA or Android Studio.
3. In the `Run configuration` dropdown, select SampleApp.  
4. Select the device or emulator you want to use.  
5. Click "Run".  
* The SampleApp launches on the applicable device or emulator.

### Deploy and establish mediation

Mediation ensures messages are routed and stored correctly between Issuers, Verifiers, and Holders, even if they are offline. The mediator offers an always-on service that securely stores messages and delivers them to the associated DIDs by using DIDComm. This capability enables use cases where connectivity to a mobile wallet cannot be guaranteed.

**Preparation**

1. To get the mediator deployed locally for the demo, clone the [Mediator repository](https://github.com/hyperledger/identus-mediator).

```shell
git clone https://github.com/hyperledger/identus-mediator
```

2. With a Docker service running, open a new terminal and run:

:::info

The latest mediator version can be found at [Mediator releases](https://github.com/hyperledger/identus-mediator/releases). Change the version in the example if you want to use the latest version.

:::

macOS terminal shell

```shell
MEDIATOR_VERSION=1.1.0 SERVICE_ENDPOINTS="http://$(ipconfig getifaddr $(route get default | grep interface | awk '{print $2}')):8080;ws://$(ipconfig getifaddr $(route get default | grep interface | awk '{print $2}')):8080/ws" docker compose up
```

Linux terminal shell

```shell
MEDIATOR_VERSION=1.1.0 SERVICE_ENDPOINTS="http://$(ip addr show $(ip route show default | awk '/default/ {print $5}') | grep 'inet ' | awk '{print $2}' | cut -d/ -f1):8080;ws://$(ip addr show $(ip route show default | awk '/default/ {print $5}') | grep 'inet ' | awk '{print $2}' | cut -d/ -f1):8080/ws" docker compose up
```

`MEDIATOR_ENDPOINTS` is then set to your local IP address:8080.

3. For advanced documentation and configuration options, see the [Mediator repository](https://github.com/hyperledger/identus-mediator).

4. Capture the mediator's [Peer DID](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#peer-did) to start the DIDComm V2 mediation protocol. Open your browser and navigate to the mediator [endpoint](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#endpoints) to obtain the DID.

**Demo application**

:::info

In order to complete this step you'll need to connect to the mediators Peer DID which you can fetch by making the following API request.

```shell
curl --location \
--request GET 'localhost:8080/invitation' \
--header 'Content-Type: application/json'
```

:::

Follow the steps on your chosen platform:

* **TypeScript SDK demo**
  1. Open [http://localhost:3000/debug](http://localhost:3000/debug) in your browser.
  2. Paste the mediator peer DID obtained from the `from` attribute after fetching the mediator's invitation endpoint.
  3. Click the **Edge Agent** tab in the bottom left.
  4. Click **Connect**.
  5. Click **Start**.
* **Swift demo app**
  1. In the app, go to Wallet Demo and, on the Mediator tab, insert the mediator DID.
* **KMM demo app**
  1. Return to the Sample app. On the main screen, provide the mediator DID of your choice or use the default value. Proceed and click **Start**.
  2. If you are running the SampleApp, click the **Start Agent** button.

The following code examples show how to establish mediation when you build your own application.

These examples demonstrate how to establish mediation and instantiate the Cloud agent.

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

To connect the holder to both Cloud agent instances, run the commands in both Issuer and Verifier endpoints.

### Establish a connection on the agent side

A connection must be established between the holder and Cloud agents to deliver issuance and verification messages to the holder.

**Establish connection on the Issuer Cloud agent**

```shell
curl --location \
--request POST 'http://localhost:8000/cloud-agent/connections' \
--header 'Content-Type: application/json' \
--data-raw '{
    "label": "Cloud agent demo connection with holder"
}'
```

1. This request returns a JSON response with an invitation and its URL. The Issuer Cloud agent shares this URL as a QR code, and the holder scans it with the wallet app.
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

2. This request returns a JSON response with an invitation and its URL. The Verifier Cloud agent shares this URL as a QR code, and the holder scans it with the wallet app.
* Copy the `invitationUrl` and the `connectionId`.

### Establish a connection on the holder side

3. Now that you have the invitation, the holder can accept it.

**Demo application**

Follow the steps on your chosen platform:

* **TypeScript SDK demo**
  1. Open [http://localhost:3000](http://localhost:3000) in your browser.
  2. Start the Edge Agent by clicking the button.
  3. Paste the invitation URL generated in the previous step into the `CloudAgent` connection section and click **Create Connection**.
  * The application displays a new connection when the process completes successfully.
* **Swift demo app**
  1. On the Out of Bounds (OOB) dialog, paste the invitation URL you generated into the `CloudAgent` connection section and click **Validate**.
  * The application shows a message in the Messages section after the connection is established correctly.
* **KMM demo app**
  1. Return to the application and click the floating button in the bottom-right corner of the Contacts tab.
  2. In the dialog, paste the invitation URL you generated into the `CloudAgent` connection section and click **Validate**.
  * The application shows a message in the Messages section after the connection is established correctly.

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

## Issue a credential from the issuer to the holder

The credential issuance flow consists of multiple steps, detailed in this section. It starts with the issuer sending a [credential offer](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#credential-offer) to the holder, which accepts or rejects the invitation and creates a `credentialRequest` from it. The [credential request](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#credential-request) is sent through DIDComm to the issuer, which issues the credential and returns it to the holder.

The issuer can create a credential offer in two ways:

1. As a direct credential offer DIDComm message for a holder with an existing connection
2. As a credential offer attachment in an out-of-band (OOB) invitation message for connectionless issuance

### Create a credential offer with an existing connection (Issuer agent)

To trigger the creation of a credential offer, call the credential-offers endpoint as follows:

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

### Create a credential offer as an invitation for connectionless issuance (Issuer agent)

To trigger the creation of a credential offer, call the credential-offers invitation endpoint as follows:

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

### Accept a credential offer invitation for connectionless issuance (Holder)

For connectionless issuance, the holder must accept the invitation that contains the credential offer before creating the credential request.

**Demo application**

Follow the steps on your chosen platform:

* **TypeScript SDK demo**
  1. In the browser at [http://localhost:3000](http://localhost:3000), navigate to the **Credential Offer** section.
  2. Paste the invitation URL received from the issuer into the input field.
  3. Click **Accept Invitation** to process the credential offer.
* **Swift mobile app**
  1. Go to the **Credential Offer** section.
  2. Enter the invitation URL received from the issuer.
  3. Tap **Accept Invitation** to process the credential offer.
* **Android mobile app**
  1. Navigate to the **Credential Offer** section.
  2. Input the invitation URL provided by the issuer.
  3. Tap **Accept Invitation** to process the credential offer.

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

### Create a credential request from a credential offer (Holder)

Because this credential offer was created with `automaticIssuance` set to `true`, as soon as the Cloud agent receives the `credentialRequest` it responds with the `IssuedCredential` message and sends it back to the holder.

:::info

automaticIssuance is optional. It can also be manually triggered and confirmed by the Holder.\`\`\`

:::

**Demo application**

The holder receives a `CredentialOffer`, accepts it, and then sends a `CredentialRequest` back to the issuer through DIDComm V2 protocols.

* **TypeScript SDK demo**
  * The `CredentialOffer` message is accepted automatically in the browser, which triggers a `CredentialRequest` message that is sent back to the Cloud agent.
* **Swift mobile app**
  * When the `CredentialOffer` message arrives, the user chooses to accept or reject it. After acceptance, a `CredentialRequest` message is sent back to the Cloud agent.
* **Android mobile app**
  * The `CredentialOffer` message is accepted automatically, which triggers a `CredentialRequest` message that is sent back to the Cloud agent.

Code examples

The exchange between `CredentialOffer` and `CredentialRequest` is demonstrated through the code samples below, showcasing how different platforms handle it.

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

### Store the issued credential (Holder)

:::caution

The sample application are using an insecure storage solution which should only be used for testing purposes and not production environments\!

:::

Code examples

Once the holder receives a credential from the Cloud agent, it needs to store the credential:

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

## Request a verification from the verifier Cloud agent to the holder (JWT W3C Credential)

Now that the holder has received a credential, it can be used in a verification workflow between a holder and a verifier. Complete the following steps:

1. The verifier creates a proof request
2. The holder receives the proof request
3. The holder creates a proof presentation and shares it with the verifier
4. The verifier verifies the proof presentation

:::info

In the example, we demonstrate two verification flows:

1. Verification with an established connection between the Holder and the Verifier.  
2. Connectionless verification in which the Holder and Verifier do not have a pre-established connection. :::

### Verifier agent

Use [the connection](https://hyperledger-identus.github.io/docs/home/quick-start#establish-connection-on-the-verifier-cloud-agent) you created between the holder and the verifier to request a presentation:

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

* This API request returns a `presentationRequestId`, which the verifier can use later to check the current status of the request.
To send a connectionless request, use the presentation invitation endpoint to create a request presentation invitation that the holder can scan or the verifier can share directly:

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

* This API request returns an `invitationId` along with an out-of-band (OOB) message. The OOB message includes a request presentation in JSON format as an attachment and is encoded as a base64 URL-encoded string, which can be shared with the holder.

### Accept a request presentation invitation for connectionless verification (Holder)

For connectionless verification, the holder needs to accept the invitation containing the request presentation.

**Demo application**

Follow the steps on your chosen platform:

* **TypeScript SDK demo**
  1. In the browser at [http://localhost:3000](http://localhost:3000), navigate to the "Request Presentation" section.
  2. Paste the invitation URL received from the issuer into the provided input field.
  3. Click **Accept Invitation** to process the request presentation.
* **Swift mobile app**
  1. Go to the "Request Presentation" section.
  2. Enter the invitation URL received from the issuer.
  3. Tap **Accept Invitation** to process the request presentation.
* **Android mobile app**
  1. Navigate to the "Request Presentation" section.
  2. Input the invitation URL provided by the issuer.
  3. Tap **Accept Invitation** to process the request presentation.

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

### Holder: Receive the presentation proof request

Ensure the holder runs an Edge Agent with the message listener active. It receives the presentation proof request from the Verifier Cloud agent for the correct message types, as detailed below:

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

### Verifier: Check whether the presentation request has been completed

```shell
curl --location \
--request GET 'http://localhost:9000/cloud-agent/present-proof/presentations/[[presentationRequestId]]' \
--header 'Accept: application/json'
```

The response body confirms whether the request has been completed and can be verified for correctness.
