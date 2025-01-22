---
title: pipes
---

# pipes

The `pipes` connection can be used to access Turbot Pipes resources.

```hcl
connection "pipes" "pipes_connection" {
  token      = "spt_cbu2_FAKETOKEN_5ruln"
  org_handle = "my-pipes-org"  

}
```

## Arguments

| Name            | Type   | Required? | Description                                                                                                     |
| --------------- | ------ | --------- | --------------------------------------------------------------------------------------------------------------- |
| `token`         | String | Optional  | A Pipes [access token](https://turbot.com/pipes/docs/accounts/developer/advanced#tokens).
| `org_handle`    | String | Optional  | A Pipes organization handle.


>[!NOTE]
> Copied from azure, do these sections this apply? We have PIPES_TOKEN but I think not PIPE_ORG_HANDLE?

## Attributes (Read-Only)

| Attribute | Type | Description                                                                                                                                       |
| --------- | ---- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| `env`     | Map  | A map of the resolved connection-related environment variables (`AZURE_TENANT_ID`, `AZURE_CLIENT_SECRET`, `AZURE_CLIENT_ID`, `AZURE_ENVIRONMENT`) |

## Default Connection

The `azure` connection type includes an implicit, default connection (`connection.azure.default`) that will be configured using the Azure environment variables:

```hcl
connection "azure" "default" {
  tenant_id       = env("AZURE_TENANT_ID")
  client_secret   = env("AZURE_CLIENT_SECRET")
  client_id       = env("AZURE_CLIENT_ID")
  environment     = env("AZURE_ENVIRONMENT")
}
```
