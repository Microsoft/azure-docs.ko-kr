---
title: Azure Media Services REST 호출에 대한 Postman 환경 가져오기
description: 이 항목에서는 Azure Media Services REST 호출에 대한 Postman 환경 정의를 제공합니다.
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 02/08/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: 6813dfca4a89263a10887ef4fa58c7403a9b1294
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60637559"
---
# <a name="import-the-postman-environment"></a>Postman 환경 가져오기  

이 문서에는 Media Services REST API를 호출하는 그룹화된 HTTP 요청이 포함된 [Postman 컬렉션](postman-collection.md)을 사용하는 **Postman** 환경 변수의 정의가 포함되어 있습니다. 환경 및 컬렉션 파일은 [Media Services REST API 호출에 대한 Postman 구성](media-rest-apis-with-postman.md) 자습서에서 사용됩니다.

```
{
  "id": "2dbce3ce-74c2-2ceb-0461-c4c2323f5b09",
  "name": "AzureMedia",
  "values": [
    {
      "enabled": true,
      "key": "TenantID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AzureADSTSEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "RESTAPIEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientSecret",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AccessToken",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAssetId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAccessPolicyId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "UploadURL",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "MediaFileName",
      "value": "BigBuckBunny.mp4",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastChannelId",
      "value": "",
      "type": "text"
    }
  ],
  "_postman_variable_scope": "environment",
  "_postman_exported_using": "Postman/5.5.0"
}
```
