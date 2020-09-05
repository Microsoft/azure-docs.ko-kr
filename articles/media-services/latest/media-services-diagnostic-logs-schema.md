---
title: Azure Media Services 진단 로그 스키마-Azure
description: 이 문서에서는 Azure Media Services 진단 로그 스키마를 보여 줍니다.
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
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 6f09a0ab88273aa10bb5f3cb55f89d014897df12
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268454"
---
# <a name="diagnostic-logs-schemas"></a>진단 로그 스키마

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure Monitor](../../azure-monitor/overview.md) 를 사용 하면 응용 프로그램의 작동 방식을 이해 하는 데 도움이 되는 메트릭 및 진단 로그를 모니터링할 수 있습니다. Media Services 진단 로그를 모니터링 하 고 수집 된 메트릭 및 로그에 대 한 경고 및 알림을 만들 수 있습니다. [Azure Storage](https://azure.microsoft.com/services/storage/)로 로그를 전송 하 고, [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)로 스트리밍하 고, [Log Analytics](https://azure.microsoft.com/services/log-analytics/)로 내보내거나 타사 서비스를 사용할 수 있습니다.

자세한 내용은 [Azure Monitor 메트릭](../../azure-monitor/platform/data-platform.md) 및 [Azure Monitor 진단 로그](../../azure-monitor/platform/platform-logs-overview.md)를 참조 하세요.

이 문서에서는 Media Services 진단 로그 스키마에 대해 설명 합니다.

## <a name="top-level-diagnostic-logs-schema"></a>최상위 수준 진단 로그 스키마

최상위 진단 로그 스키마에 대 한 자세한 설명은 [Azure 진단 로그에 대해 지원 되는 서비스, 스키마 및 범주](../../azure-monitor/platform/resource-logs-schema.md)를 참조 하세요.

## <a name="key-delivery-log-schema"></a>키 배달 로그 스키마

### <a name="properties"></a>속성

이러한 속성은 키 배달 로그 스키마에만 적용 됩니다.

|Name|설명|
|---|---|
|keyId|요청 된 키의 ID입니다.|
|keyType|"Clear" (암호화 없음), "FairPlay", "PlayReady" 또는 "Widevine" 값 중 하나일 수 있습니다.|
|policyName|정책의 Azure Resource Manager 이름입니다.|
|tokenType|토큰 유형입니다.|
|statusMessage|상태 메시지입니다.|

### <a name="examples"></a>예

키 배달 요청 스키마의 속성입니다.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="additional-notes"></a>추가적인 참고 사항

* Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="next-steps"></a>다음 단계

[Media Services 메트릭 및 진단 로그 모니터링](media-services-metrics-diagnostic-logs.md)
