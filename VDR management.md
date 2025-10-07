# VDR management {#vdr-management}

## Overview

The Verifiable Data Registry (VDR) gives the Cloud agent a storage-agnostic interface for data referenced in self-sovereign identity (SSI) protocols. Each entry can store arbitrary bytes—such as credential status lists—and be resolved through a VDR URI. Because the interface abstracts the storage layer, you can swap drivers without changing the integration logic.

This tutorial shows how to create, resolve, and deactivate VDR entries through the Cloud agent HTTP binding that implements the [VDR specification](https://github.com/hyperledger-identus/vdr). A reference library and the agent binding share the same behavior, so the steps apply across deployments.

## Roles

1. The **data owner** is responsible for creating entries and managing their lifecycle.

This tutorial stores data with the database driver so that you can complete the walkthrough on a single Cloud agent instance.

## Endpoints

The example uses the following endpoints:

| Endpoint | Description | Role |
| :---- | :---- | :---- |
| `GET /vdr/entries` | Resolve the data by VDR URI | Anyone |
| `POST /vdr/entries` | Create a new VDR entry | Data owner |
| `DELETE /vdr/entries` | Deactivate the VDR entry | Data owner |

### Create sample binary input

```shell
echo -ne '\x01\x02\x03\x04' > sample_in.bin
```

The command creates a file named `sample_in.bin` that contains four bytes.

### Create a new VDR entry with sample data

```shell
curl -X POST "http://localhost:8080/cloud-agent/vdr/entries?drid=database" \
  -H "Content-Type: application/octet-stream" \
  --data-binary @sample_in.bin
```

```json
{
  "url":"vdr://?drf=memory&drid=memory&drv=0.1.0&m=0#d63bdd21-0347-4caf-a255-0cca7c2851fe"
}
```

The response returns the locator for the VDR entry. When you reference the URL within an SSI protocol, any party with access to the same driver can resolve the stored data. Because this example uses the database driver, only the current Cloud agent instance can resolve the resource.

### Resolve the data by VDR URL

Use the URL from the previous step and resolve the entry.

```shell
curl -X GET "http://localhost:8080/cloud-agent/vdr/entries?url=<ENCODED_VDR_URL>" > sample_out.bin
```

Remember to percent-encode the URL to escape reserved characters. A successful request returns status 200 and writes the response body to `sample_out.bin`.

### Compare the VDR entry and the input

```shell
diff sample_in.bin sample_out.bin
```

The output should be empty, confirming that the VDR entry matches the input data.

### Deactivate the VDR entry

```shell
curl -X DELETE "http://localhost:8080/cloud-agent/vdr/entries?url=<ENCODED_VDR_URL>"
```

Changing the method to `DELETE` deactivates the entry and returns HTTP 200 on success.

Resolving the entry after deletion produces a not-found error:

```json
{
  "status":404,
  "type":"error:VdrServiceError:VdrEntryNotFound",
  "title":"Vdr Entry Not Found",
  "detail":"The data could not be found from a provided URL",
  "instance":"error:instance:b70984a1-71f5-409f-9afc-3b8bada54445"
}
```

The error confirms that the resource is no longer available for resolution.

---
