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
