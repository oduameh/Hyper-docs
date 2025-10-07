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

The Identus Platform supports the following credential schema specifications:

- [Verifiable Credentials JSON Schema 2022](https://www.w3.org/TR/vc-json-schema/).
- [AnonCreds Schema](https://hyperledger.github.io/anoncreds-spec/#term:schemas).

The signed credential schema allows the following verifications:

- Semantic verification of the verifiable credentials.
- Authorship verification.

The author can use credential schemas to issue the following types of verifiable credentials:

- JSON verifiable credential.
- JSON-LD verifiable credential.
- AnonCreds verifiable credential.
- All types above encoded as JWT.

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
