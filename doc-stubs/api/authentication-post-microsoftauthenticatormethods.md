---
title: "Create microsoftAuthenticatorAuthenticationMethod"
description: "Create a new microsoftAuthenticatorAuthenticationMethod object."
author: "**TODO: Provide Github Name. See [topic-level metadata reference](https://msgo.azurewebsites.net/add/document/guidelines/metadata.html#topic-level-metadata)**"
ms.localizationpriority: medium
ms.prod: "**TODO: Add MS prod. See [topic-level metadata reference](https://msgo.azurewebsites.net/add/document/guidelines/metadata.html#topic-level-metadata)**"
doc_type: apiPageType
---

# Create microsoftAuthenticatorAuthenticationMethod
Namespace: microsoft.graph

[!INCLUDE [beta-disclaimer](../../includes/beta-disclaimer.md)]

Create a new microsoftAuthenticatorAuthenticationMethod object.

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
POST /user/authentication/microsoftAuthenticatorMethods
```

## Request headers
|Name|Description|
|:---|:---|
|Authorization|Bearer {token}. Required.|
|Content-Type|application/json. Required.|

## Request body
In the request body, supply a JSON representation of the [microsoftAuthenticatorAuthenticationMethod](../resources/microsoftauthenticatorauthenticationmethod.md) object.

The following table shows the properties that are required when you create the [microsoftAuthenticatorAuthenticationMethod](../resources/microsoftauthenticatorauthenticationmethod.md).

|Property|Type|Description|
|:---|:---|:---|
|id|String|**TODO: Add Description** Inherited from [authenticationMethod](../resources/authenticationmethod.md)|
|displayName|String|**TODO: Add Description**|
|deviceTag|String|**TODO: Add Description**|
|phoneAppVersion|String|**TODO: Add Description**|
|createdDateTime|DateTimeOffset|**TODO: Add Description**|
|clientAppName|microsoftAuthenticatorAuthenticationMethodClientAppName|**TODO: Add Description**. The possible values are: `microsoftAuthenticator`, `outlookMobile`, `unknownFutureValue`.|



## Response

If successful, this method returns a `201 Created` response code and a [microsoftAuthenticatorAuthenticationMethod](../resources/microsoftauthenticatorauthenticationmethod.md) object in the response body.

## Examples

### Request
<!-- {
  "blockType": "request",
  "name": "create_microsoftauthenticatorauthenticationmethod_from_"
}
-->
``` http
POST https://graph.microsoft.com/beta/user/authentication/microsoftAuthenticatorMethods
Content-Type: application/json
Content-length: 215

{
  "@odata.type": "#microsoft.strongAuthentication.microsoftAuthenticatorAuthenticationMethod",
  "displayName": "String",
  "deviceTag": "String",
  "phoneAppVersion": "String",
  "clientAppName": "String"
}
```


### Response
>**Note:** The response object shown here might be shortened for readability.
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.strongAuthentication.microsoftAuthenticatorAuthenticationMethod"
}
-->
``` http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "@odata.type": "#microsoft.strongAuthentication.microsoftAuthenticatorAuthenticationMethod",
  "id": "8a061acf-1acf-8a06-cf1a-068acf1a068a",
  "displayName": "String",
  "deviceTag": "String",
  "phoneAppVersion": "String",
  "createdDateTime": "String (timestamp)",
  "clientAppName": "String"
}
```

