---
title: "Add identityProvider"
description: "Add identityProviders by posting to the identityProviders collection."
author: "**TODO: Provide Github Name. See [topic-level metadata reference](https://msgo.azurewebsites.net/add/document/guidelines/metadata.html#topic-level-metadata)**"
localization_priority: Normal
ms.prod: "**TODO: Add MS prod. See [topic-level metadata reference](https://msgo.azurewebsites.net/add/document/guidelines/metadata.html#topic-level-metadata)**"
doc_type: apiPageType
---

# Add identityProvider
Namespace: microsoft.graph

[!INCLUDE [beta-disclaimer](../../includes/beta-disclaimer.md)]

Add identityProviders by posting to the identityProviders collection.

## Permissions
One of the following permissions is required to call this API. To learn more, including how to choose permissions, see [Permissions](/graph/permissions-reference).

|Permission type|Permissions (from least to most privileged)|
|:---|:---|
|Delegated (work or school account)|**TODO: Provide applicable permissions.**|
|Delegated (personal Microsoft account)|**TODO: Provide applicable permissions.**|
|Application|**TODO: Provide applicable permissions.**|

## HTTP request

<!-- {
  "blockType": "ignored"
}
-->
``` http
POST /identity/b2cUserFlows/{b2cIdentityUserFlowId}/identityProviders/$ref
```

## Request headers
|Name|Description|
|:---|:---|
|Authorization|Bearer {token}. Required.|
|Content-Type|application/json. Required.|

## Request body
In the request body, supply a JSON representation of the [identityProvider](../resources/identityprovider.md) object.

The following table shows the properties that are required when you create the [identityProvider](../resources/identityprovider.md).

|Property|Type|Description|
|:---|:---|:---|
|id|String|**TODO: Add Description**|
|type|String|**TODO: Add Description**|
|name|String|**TODO: Add Description**|
|clientId|String|**TODO: Add Description**|
|clientSecret|String|**TODO: Add Description**|



## Response

If successful, this method returns a `204 No Content` response code and an [identityProvider](../resources/identityprovider.md) object in the response body.

## Examples

### Request
<!-- {
  "blockType": "request",
  "name": "create_identityprovider_from_identityproviders"
}
-->
``` http
POST https://graph.microsoft.com/beta/identity/b2cUserFlows/{b2cIdentityUserFlowId}/identityProviders/$ref
Content-Type: application/json
Content-length: 168

{
  "@odata.type": "#Microsoft.Cpim.Api.DataModels.identityProvider",
  "type": "String",
  "name": "String",
  "clientId": "String",
  "clientSecret": "String"
}
```


### Response
>**Note:** The response object shown here might be shortened for readability.
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "Microsoft.Cpim.Api.DataModels.identityProvider"
}
-->
``` http
HTTP/1.1 204 No Content
Content-Type: application/json

{
  "@odata.type": "#Microsoft.Cpim.Api.DataModels.identityProvider",
  "id": "378f3d06-3d06-378f-063d-8f37063d8f37",
  "type": "String",
  "name": "String",
  "clientId": "String",
  "clientSecret": "String"
}
```

