# Credentials (5 tutorials)

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
