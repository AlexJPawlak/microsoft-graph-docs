---
title: "skypeForBusinessPeerToPeerActivityUserCounts resource type"
description: "The following is a JSON representation of the resource."
ms.localizationpriority: medium
doc_type: resourcePageType
ms.prod: ""
author: "kszb"
---

# skypeForBusinessPeerToPeerActivityUserCounts resource type

Namespace: microsoft.graph

## Properties

| Property          | Type   |
| :---------------- | :----- |
| im                | Int64  |
| audio             | Int64  |
| video             | Int64  |
| appSharing        | Int64  |
| fileTransfer      | Int64  |
| reportRefreshDate | Date   |
| reportDate        | Date   |
| reportPeriod      | String |

## JSON representation

The following is a JSON representation of the resource.

<!-- {
  "blockType": "resource",
  "@odata.type": "microsoft.graph.skypeForBusinessPeerToPeerActivityUserCounts"
} -->

```json
{
  "im": 1024,
  "audio": 1024,
  "video": 1024,
  "appSharing": 1024,
  "fileTransfer": 1024,
  "reportRefreshDate": "Date",
  "reportDate": "Date",
  "reportPeriod": "String"
}
```


