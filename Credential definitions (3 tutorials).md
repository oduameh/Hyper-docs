# Credential definitions (3 tutorials) {#credential-definitions}

Credential definitions extend AnonCreds schemas with the cryptographic material required for zero-knowledge proofs and selective disclosure. Each definition references a schema and publishes the issuer’s public keys so verifiers can validate credentials securely. This guide explains the structure of a definition and how to create one with the Cloud agent APIs.

The Identus platform implements the [Hyperledger AnonCreds specification](https://hyperledger.github.io/anoncreds-spec/#term:schemas), so every definition that you publish remains compatible with other AnonCreds agents.

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

## Conclusion and references

AnonCred credential definitions provide a consistent format for verifiable credentials across ecosystems. Replace the placeholders (for example, `{{CREDENTIAL_NAME}}` and `{{VERSION_NUMBER}}`) with production values before you publish a definition.

- [Hyperledger AnonCreds specification](https://hyperledger.github.io/anoncreds-spec/#term:schemas)

---

## Creating a credential definition

The Cloud agent exposes REST API for creation, fetching, and searching the [credential definition](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#credential-definition) records.

The OpenAPI specification and ReDoc documentation describe the endpoint.

In this document, you can find step-by-step instructions for creating the credential definition.

## Prerequisites

Before you create a credential definition, complete the following tasks:

1. Create and publish a [PRISM DID](http://../dids/create.md).
2. [Create a credential schema](http://../schemas/create.md) with the type `AnoncredSchemaV1`, as shown in the [example](http://../schemas/credential-schema#schema-anoncred-schema).

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
