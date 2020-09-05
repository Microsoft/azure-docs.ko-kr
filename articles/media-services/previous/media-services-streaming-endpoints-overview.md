---
title: Azure Media Services 스트리밍 엔드포인트 개요 | Microsoft Docs
description: 이 문서에서는 스트리밍 끝점 Azure Media Services의 개요를 제공 합니다.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: a541e1b068ec3667120bbb31e65ca7bc35febadb
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265918"
---
# <a name="streaming-endpoints-overview"></a>스트리밍 엔드포인트 개요  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2에는 새로운 특징 또는 기능이 추가되지 않습니다. <br/>[Media Services v3](../latest/index.yml)의 최신 버전을 확인하세요. 또한 [v2에서 v3로의 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)을 참조하세요.

Microsoft AMS(Azure Media Services)에서 **스트리밍 엔드포인트**는 추가 배포를 위해 CDN(Content Delivery Network) 또는 클라이언트 플레이어 애플리케이션에 직접 콘텐츠를 배달할 수 있는 스트리밍 서비스를 나타냅니다. Media Services는 매끄러운 Azure CDN 통합도 제공합니다. StreamingEndpoint 서비스의 아웃바운드 스트림은 Media Services 계정에서 자산의 라이브 스트림, 주문형 비디오 또는 점진적 다운로드일 수 있습니다. 각 Azure Media Services 계정에는 기본 StreamingEndpoint 포함됩니다. 계정에서 추가 StreamingEndpoints를 만들 수 있습니다. StreamingEndpoints 1.0 및 2.0이라는 두 가지 버전이 있습니다. 2017년 1월 10일부터 새로 만든 AMS 계정은 **기본** StreamingEndpoint 버전 2.0을 포함합니다. 이 계정에 추가하는 추가 스트리밍 엔드포인트도 버전 2.0입니다. 이 변경 사항은 기존 계정에 영향을 주지 않습니다. 기존 StreamingEndpoints는 1.0 버전이며 2.0 버전으로 업그레이드할 수 있습니다. 이 변경 사항으로 인해 동작, 청구 및 기능이 변경됩니다(자세한 내용은 아래에서 설명한 **스트리밍 형식 및 버전** 섹션 참조).

Azure Media Services 스트리밍 끝점 엔터티에 **Cdnprovider**, **cdnprovider**, **streamingendpointversion**속성이 추가 되었습니다. 이러한 속성의 자세한 개요는 [여기](/rest/api/media/operations/streamingendpoint)를 참조하세요. 

Azure Media Services 계정을 만들 경우 기본 표준 스트리밍 엔드포인트가 **중지됨** 상태로 만들어집니다. 기본 스트리밍 엔드포인트를 삭제할 수 없습니다. 기본적으로 대상 지역에서 Azure CDN 사용 가능성에 따라 새로 만든 기본 스트리밍 엔드포인트도 "StandardVerizon" CDN 공급자 통합을 포함합니다. 
                
> [!NOTE]
> 스트리밍 엔드포인트를 시작하기 전에 Azure CDN 통합을 비활성화할 수 있습니다. `hostname` 및 스트리밍 URL은 CDN을 사용하도록 설정하는지 여부에 관계없이 동일하게 유지됩니다.

이 항목에서는 스트리밍 엔드포인트가 제공하는 기본 기능을 간략하게 설명합니다.

## <a name="naming-conventions"></a>명명 규칙

기본 엔드포인트: `{AccountName}.streaming.mediaservices.windows.net`

추가 엔드포인트: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>스트리밍 형식 및 버전

### <a name="standardpremium-types-version-20"></a>표준/프리미엄 유형(버전 2.0)

Media Services 1 월 2017 릴리스부터 **표준** (미리 보기) 및 **프리미엄**의 두 가지 스트리밍 유형이 있습니다. 이러한 유형은 스트리밍 엔드포인트 버전 "2.0"의 일부입니다.


|유형|설명|
|--------|--------|  
|**Standard**|기본 스트리밍 끝점은 **표준** 형식이 며, 스트리밍 단위를 조정 하 여 프리미엄 형식으로 변경할 수 있습니다.|
|**Premium** |이 옵션은 큰 규모나 세밀한 컨트롤을 필요로 하는 전문 시나리오에 적합합니다. 스트리밍 단위를 조정 하 여 **프리미엄** 유형으로 이동 합니다.<br/>전용 스트리밍 끝점은 격리 된 환경에서 라이브 되며 리소스를 경합 하지 않습니다.|

대량 인터넷 사용자에 게 콘텐츠를 배달 하려는 고객의 경우 스트리밍 끝점에서 CDN을 사용 하도록 설정 하는 것이 좋습니다.

자세한 내용은 다음 [스트리밍 유형 비교](#comparing-streaming-types) 섹션을 참조하세요.

### <a name="classic-type-version-10"></a>클래식 유형(버전 1.0)

2017년 1월 10일 릴리스 이전에 AMS 계정을 만든 사용자는 스트리밍 엔드포인트의 **클래식** 유형을 보유합니다. 이 유형은 스트리밍 엔드포인트 버전 "1.0"의 일부입니다.

**버전 "1.0"** 스트리밍 엔드포인트에 하나 이상의 프리미엄 SU(스트리밍 단위)가 있는 경우 해당 항목은 프리미엄 스트리밍 엔드포인트이며 추가 구성 단계 없이 **표준/프리미엄** 유형과 동일하게 모든 AMS 기능을 제공합니다.

>[!NOTE]
>**클래식** 스트리밍 엔드포인트(버전 "1.0" 및 0SU)는 제한된 기능을 제공하고 SLA를 포함하지 않습니다. **표준** 형식을 마이그레이션하여 더 나은 환경을 제공하고 동적 패키징 또는 암호화와 같은 기능 및 **표준** 유형에 포함된 다른 기능을 사용하는 것이 좋습니다. **표준** 유형으로 마이그레이션하려면 [Azure Portal](https://portal.azure.com/)로 이동하여 **표준으로 옵트인**을 선택합니다. 마이그레이션에 대한 자세한 내용은 [마이그레이션](#migration-between-types) 섹션을 참조하세요.
>
>이 작업을 롤백할 수 없고 가격 책정에 미치는 영향이 있음에 주의합니다.
>
 
## <a name="comparing-streaming-types"></a>스트리밍 유형 비교

### <a name="versions"></a>버전

|유형|StreamingEndpointVersion|ScaleUnits|CDN|결제|
|--------------|----------|-----------------|-----------------|-----------------|
|Classic|1.0|0|해당 없음|Free|
|표준 스트리밍 끝점 (미리 보기)|2.0|0|예|유료|
|프리미엄 스트리밍 단위|1.0|>0|예|유료|
|프리미엄 스트리밍 단위|2.0|>0|예|유료|

### <a name="features"></a>기능

기능|Standard|Premium
---|---|---
처리량 |CDN을 사용 하는 경우 최대 600 Mbps 및 보다 효과적인 처리량을 제공할 수 있습니다.|SU(스트리밍 단위)당 200Mbps 는 CDN을 사용 하는 경우 훨씬 더 효율적인 처리량을 제공할 수 있습니다.
CDN|Azure CDN, 타사 CDN 또는 CDN 없음.|Azure CDN, 타사 CDN 또는 CDN 없음.
청구를 계산합니다.| 매일|매일
동적 암호화|예|예
동적 패키징|예|예
확장|대상 처리량까지 자동으로 확장합니다.|추가 스트리밍 단위입니다.
IP 필터링/G20/사용자 지정 호스트 <sup>1</sup>|예|예
점진적 다운로드|예|예
권장 사용량 |대부분의 스트리밍 시나리오에 권장됩니다.|전문 사용량입니다. 

<sup>1</sup> 끝점에서 CDN을 사용 하도록 설정 하지 않은 경우에만 스트리밍 끝점에서 직접 사용 됩니다.<br/>

SLA에 대 한 자세한 내용은 [가격 책정 및 SLA](https://azure.microsoft.com/pricing/details/media-services/)를 참조 하세요.

## <a name="migration-between-types"></a>유형 간의 마이그레이션

From | 받는 사람 | 작업
---|---|---
클래식|Standard|옵트인할 필요
Classic|Premium| 크기 조정(추가 스트리밍 단위)
표준/프리미엄|Classic|사용할 수 없음(스트리밍 엔드포인트 버전이 1.0인 경우 scaleunits을 "0"으로 설정하도록 클래식을 변경할 수 있음)
표준(CDN 포함/없이)|동일한 구성을 포함한 프리미엄|**시작됨** 상태에서 허용됩니다. (Azure Portal을 통해)
프리미엄(CDN 포함/없이)|동일한 구성을 포함한 표준|**시작됨** 상태에서 허용됩니다(Azure Portal을 통해).
표준(CDN 포함/없이)|다른 구성을 포함한 프리미엄|**중지됨** 상태에서 허용됩니다(Azure Portal을 통해). 실행 상태에서 허용되지 않습니다.
프리미엄(CDN 포함/없이)|다른 구성을 포함한 표준|**중지됨** 상태에서 허용됩니다(Azure Portal을 통해). 실행 상태에서 허용되지 않습니다.
CDN 포함 SU >= 1인 버전 1.0|CDN 없는 표준/프리미엄|**중지됨** 상태에서 허용됩니다. **시작됨** 상태에서 허용되지 않습니다.
CDN 포함 SU >= 1인 버전 1.0|CDN 포함된/없는 표준|**중지됨** 상태에서 허용됩니다. **시작됨** 상태에서 허용되지 않습니다. CDN 버전 1.0을 삭제하고 새로 만들어서 시작합니다.
CDN 포함 SU >= 1인 버전 1.0|CDN 포함된/없는 프리미엄|**중지됨** 상태에서 허용됩니다. **시작됨** 상태에서 허용되지 않습니다. 클래식 CDN을 삭제하고 새로 만들어서 시작합니다.

## <a name="next-steps"></a>다음 단계
Media Services 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
