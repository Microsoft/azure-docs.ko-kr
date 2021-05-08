---
title: Azure Media Services REST 호출에 대한 Postman 환경 가져오기
description: 이 항목에서는 Azure Media Services REST 호출에 대한 Postman 환경 정의를 제공합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 5b1705fb2b3b1b69f79158747827d764f1bef4f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103007847"
---
# <a name="import-the-postman-environment"></a>Postman 환경 가져오기

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

이 문서에는 Media Services REST API를 호출하는 그룹화된 HTTP 요청이 포함된 [Postman 컬렉션](postman-collection.md)을 사용하는 **Postman** 환경 변수의 정의가 포함되어 있습니다. 환경 및 컬렉션 파일은 [Media Services REST API 호출에 대한 Postman 구성](media-rest-apis-with-postman.md) 자습서에서 사용됩니다.

> [!NOTE]
> `AzureADSTSEndpoint ` = `https://login.microsoftonline.com/{{TenantId}}/oauth2/token`의 값입니다. 테넌트 ID를 가져오려는 경우 포털에서 사용자 이름(오른쪽 상단) 위로 마우스를 가져가면 “Directory: Microsoft ( {{TENANTID}} )에 배치됩니다.

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
