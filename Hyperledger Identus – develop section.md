# Hyperledger Identus – develop section {#hyperledger-identus-–-develop-section}

## Understanding the develop section

The Develop section provides comprehensive tutorials for building self-sovereign identity solutions with Hyperledger Identus. Each tutorial includes step-by-step instructions, code examples, and explanations of the underlying concepts. Whether you're new to Identus or implementing specific features, this section guides you through the complete development process.

## Navigating the develop section

**New to Identus?** Start with the [Quick start guide](#quick-start-guide) for a complete end-to-end workflow.

**Implementing specific features?** Jump to the relevant tutorial (Connections, DIDs, Schemas, Credentials, etc.). Each tutorial includes roles, prerequisites, and step-by-step instructions.

**Troubleshooting?** See [Troubleshooting and considerations](#troubleshooting-and-considerations).

## Understanding developer roles

**Backend developers** – focus on DIDs, schemas, credential definitions, and credentials tutorials to build issuer and verifier services.

**Frontend developers** – start with the Quick start guide to understand the flow, then reference wallet SDK examples in the Quick Start.

**DevOps engineers** – review prerequisites in the Quick start guide, focus on multi-tenancy and secret management tutorials for secure infrastructure deployment.

## Following tutorial conventions

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
