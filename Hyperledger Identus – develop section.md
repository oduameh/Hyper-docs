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

- [Quick start guide](./Quick%20start%20guide.md)
- [Connections](./Connections.md)
- [DIDs](./DIDs.md)
- [Schemas](./Schemas.md)
- [Credential definitions](./Credential-definitions.md)
- [Credentials](./Credentials.md)
- [Multi-tenancy](./Multi-tenancy.md)
- [Secret management](./Secret%20management.md)
- [VDR management](./VDR%20management.md)
- [Webhooks](./Webhooks.md)
- [Troubleshooting and considerations](./Troubleshooting%20and%20considerations.md)

---

{{#include Hyperledger Identus – develop section.md}}

{{#include Quick start guide.md}}

{{#include Connections.md}}

{{#include DIDs.md}}

{{#include Schemas.md}}

{{#include Credential-definitions.md}}

{{#include Credentials.md}}

{{#include Multi-tenancy.md}}

{{#include Secret management.md}}

{{#include VDR management.md}}

{{#include Webhooks.md}}

{{#include Troubleshooting and considerations.md}}
