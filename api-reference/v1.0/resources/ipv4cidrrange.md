---
title: "iPv4CidrRange resource type"
description: "Represents an IPv4 range using the CIDR notation."
ms.localizationpriority: medium
author: "videor"
ms.prod: "directory-management"
doc_type: resourcePageType
---

# iPv4CidrRange resource type

Namespace: microsoft.graph

Represents an IPv4 range using the CIDR notation.

Inherits from [ipRange](../resources/iprange.md)

## Properties

| Property     | Type        | Description |
|:-------------|:------------|:------------|
|cidrAddress|String|IPv4 address in CIDR notation|

## JSON representation

The following is a JSON representation of the resource.

<!-- {
  "blockType": "resource",
  "optionalProperties": [

  ],
  "@odata.type": "microsoft.graph.iPv4CidrRange",
  "baseType": "microsoft.graph.ipRange"
}-->

```json
{
  "cidrAddress": "String"
}
```

<!-- uuid: 16cd6b66-4b1a-43a1-adaf-3a886856ed98
2019-02-04 14:57:30 UTC -->
<!-- {
  "type": "#page.annotation",
  "description": "iPv4CidrRange resource",
  "keywords": "",
  "section": "documentation",
  "tocPath": ""
}-->
