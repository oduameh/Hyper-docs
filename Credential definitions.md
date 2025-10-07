# Credential definitions {#credential-definitions}

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
