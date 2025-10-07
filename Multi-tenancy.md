# Multi-tenancy {#multi-tenancy}

## Overview

Multi-tenancy enables a single Cloud agent instance to serve multiple organizations or user groups with complete data isolation. Each tenant has their own wallet containing DIDs, credentials, and connections, logically separated from other tenants. This section covers tenant onboarding, authentication methods (built-in and external IAM with Keycloak), access control, and tenant migration.

Understanding multi-tenancy is essential for enterprise deployments, SaaS platforms, or any scenario where you need to serve multiple user groups from shared infrastructure.

---

## Tenant onboarding

Two roles participate in tenant management. Administrators manage wallets and tenants, and tenants use those wallets to perform standard SSI interactions with the Cloud agent.

## Roles

1. [Administrator](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#administrator). Manages wallets, entities, and authentication methods.
2. [Tenant](https://hyperledger-identus.github.io/docs/home/concepts/glossary/#tenant). Uses the allocated wallet to issue, hold, and present credentials.

## Prerequisites

1. Cloud agent running.
2. The Cloud agent configured with the following environment variables:
   - `ADMIN_TOKEN=my-admin-token`
   - `API_KEY_ENABLED=true`
   - `API_KEY_AUTO_PROVISIONING=false`
   - `DEFAULT_WALLET_ENABLED=false`

## Onboarding flow

This walkthrough covers how to onboard a new tenant from scratch. You create an entity to represent the tenant, provision a wallet, and enable an authentication method. Afterward, the tenant can interact with the Cloud agent in an isolated wallet environment.

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

When you start the Cloud agent with the configuration above, it should have no wallets. Listing wallets returns an empty result set.

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

Create a wallet with the `POST /wallets` endpoint. The wallet stores the tenant's assets (DIDs, verifiable credentials, connections, and so on). Provide a seed during creation or omit it to let the agent generate a random value.

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

Create an entity to represent the tenant by sending a `POST` request to the `/iam/entities` endpoint with the following payload:

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

After the tenant has a wallet and entity, configure the entity's authentication method. Share the resulting `apikey` with the tenant so they can access the agent.

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

The endpoint returns HTTP status code 201 when the request succeeds.

## Tenant interactions

With the `apikey` provisioned by the administrator, the tenant can authenticate and use the Cloud agent.

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
