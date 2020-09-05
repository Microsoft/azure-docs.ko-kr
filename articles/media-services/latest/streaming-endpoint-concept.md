---
title: 스트리밍 끝점 (원본)
titleSuffix: Azure Media Services
description: 클라이언트 플레이어 앱 또는 CDN (Content Delivery Network)에 직접 콘텐츠를 전달 하는 동적 패키징 및 스트리밍 서비스인 스트리밍 끝점 (원본)에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: inhenkel
ms.openlocfilehash: aa54bc6b8b0912158a5dcd369b12801d51ca7141
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89256500"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Azure Media Services의 스트리밍 끝점 (원본)

Microsoft Azure Media Services에서 [스트리밍 끝점](/rest/api/media/streamingendpoints) 은 일반 스트리밍 미디어 프로토콜 (HLS 또는 대시) 중 하나를 사용 하 여 라이브 및 주문형 콘텐츠를 클라이언트 플레이어 앱에 직접 배달할 수 있는 동적 (just-in-time) 패키징 및 원본 서비스를 나타냅니다. 또한 **스트리밍 끝점** 은 업계 최고의 drms에 동적 (just-in-time) 암호화를 제공 합니다. 

Media Services 계정을 만들 경우 **기본** 스트리밍 엔드포인트가 중지됨 상태로 만들어집니다. **기본** 스트리밍 끝점은 삭제할 수 없습니다. 계정으로 더 많은 스트리밍 끝점을 만들 수 있습니다 ( [할당량 및 제한](limits-quotas-constraints.md)참조).

> [!NOTE]
> 비디오 스트리밍을 시작하려면 비디오를 스트리밍하려는 **스트리밍 엔드포인트**를 시작해야 합니다.
>
> 스트리밍 끝점이 실행 중 상태인 경우에만 요금이 청구 됩니다.

또한 [동적 패키징](dynamic-packaging-overview.md) 항목을 검토 해야 합니다. 

## <a name="naming-convention"></a>명명 규칙

스트리밍 URL의 호스트 이름 형식은입니다. 여기서는 `{servicename}-{accountname}-{regionname}.streaming.media.azure.net` `servicename` 스트리밍 끝점 이름 또는 라이브 이벤트 이름입니다.

기본 스트리밍 끝점을 사용 하는 경우 `servicename` 이 생략 되므로 URL은 `{accountname}-{regionname}.streaming.azure.net` 입니다.

### <a name="limitations"></a>제한 사항

* 스트리밍 끝점 이름의 최대 값은 24 자입니다.
* 이름은이 [regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) 패턴을 따라야 합니다. `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`

## <a name="types"></a>유형

**표준** (미리 보기) 및 **프리미엄**의 두 가지 **스트리밍 끝점** 유형이 있습니다. 이러한 유형은 스트리밍 엔드포인트에 할당하는 배율 단위(`scaleUnits`) 수로 정의됩니다.

최대 스트리밍 단위 제한은 일반적으로 10입니다. 계정에 대 한 제한을 높이려면 [여기](https://azure.microsoft.com/support/create-ticket/) 로 문의 하세요.

다음 표에 해당 유형이 설명되어 있습니다.

|유형|배율 단위|설명|
|--------|--------|--------|  
|**Standard**|0|기본 스트리밍 끝점은 **표준** 유형이 며를 조정 하 여 프리미엄 유형으로 변경할 수 있습니다 `scaleUnits` .|
|**Premium**|>0|**프리미엄** 스트리밍 끝점은 고급 워크 로드에 적합 하며, 확장 가능한 전용 대역폭 용량을 제공 합니다. (스트리밍 단위)를 조정 하 여 **프리미엄** 유형으로 이동 `scaleUnits` 합니다. `scaleUnits`는 200Mbps 단위로 구입할 수 있는 전용 송신 용량을 제공합니다. **프리미엄** 유형을 사용 하는 경우 사용 하도록 설정 된 각 단위는 앱에 추가 대역폭 용량을 제공 합니다. |

> [!NOTE]
> 대량 인터넷 사용자에 게 콘텐츠를 배달 하려는 고객의 경우 스트리밍 끝점에서 CDN을 사용 하도록 설정 하는 것이 좋습니다.

SLA에 대 한 자세한 내용은 [가격 책정 및 SLA](https://azure.microsoft.com/pricing/details/media-services/)를 참조 하세요.

## <a name="comparing-streaming-types"></a>스트리밍 유형 비교

기능|Standard|Premium
---|---|---
처리량 |CDN을 사용 하는 경우 최대 600 Mbps 및 보다 효과적인 처리량을 제공할 수 있습니다.|SU(스트리밍 단위)당 200Mbps 는 CDN을 사용 하는 경우 훨씬 더 효율적인 처리량을 제공할 수 있습니다.
CDN|Azure CDN, 타사 CDN 또는 CDN이 없습니다.|Azure CDN, 타사 CDN 또는 CDN이 없습니다.
청구를 계산합니다.| 매일|매일
동적 암호화|예|예
동적 패키징|예|예
확장|대상 처리량까지 자동으로 확장합니다.|추가 SUs
IP 필터링/G20/사용자 지정 호스트 <sup>1</sup>|예|예
점진적 다운로드|예|예
권장 사용량 |대부분의 스트리밍 시나리오에 권장됩니다.|전문 사용량입니다.

<sup>1</sup> 끝점에서 CDN을 사용 하도록 설정 하지 않은 경우에만 스트리밍 끝점에서 직접 사용 됩니다.<br/>

## <a name="streaming-endpoint-properties"></a>스트리밍 끝점 속성

이 섹션에서는 일부 스트리밍 끝점 속성에 대해 자세히 설명 합니다. 새 스트리밍 엔드포인트를 만드는 방법의 예제와 모든 속성에 대한 설명을 보려면 [스트리밍 엔드포인트](/rest/api/media/streamingendpoints/create)를 참조하세요.

- `accessControl`:이 스트리밍 끝점에 대 한 다음 보안 설정을 구성 하는 데 사용 됩니다. Akamai 서명 헤더 인증 키와이 끝점에 연결할 수 있는 IP 주소입니다. 이 속성 `cdnEnabled` 은가 false로 설정 된 경우에만 설정할 수 있습니다.

- `cdnEnabled`:이 스트리밍 끝점에 대 한 Azure CDN 통합을 사용할 수 있는지 여부를 나타냅니다 (기본적으로 사용 안 함). `cdnEnabled`를 true로 설정하면 `customHostNames` 및 `accessControl` 구성이 사용되지 않도록 설정됩니다.

    모든 데이터 센터에서 Azure CDN 통합을 지원하는 것은 아닙니다. 데이터 센터에서 Azure CDN 통합을 사용할 수 있는지 확인 하려면 다음 단계를 수행 합니다.

  - `cdnEnabled`를 true로 설정합니다.
  - `HTTP Error Code 412`현재 지역에서 CDN 기능을 사용할 수 없으므로 "스트리밍 끝점 CdnEnabled 속성을 true로 설정할 수 없습니다." 라는 메시지와 함께 (preconditionfailed)에 대해 반환 된 결과를 확인 합니다.

    이 오류가 발생 하면 데이터 센터에서이를 지원 하지 않습니다. 다른 데이터 센터를 사용해 보세요.

- `cdnProfile`: `cdnEnabled` 이 true로 설정 된 경우 값을 전달할 수도 있습니다 `cdnProfile` . `cdnProfile`은 CDN 엔드포인트이 생성되는 CDN 프로필의 이름입니다. 기존 cdnProfile을 제공하거나 새로 만들 수 있습니다. 값이 NULL이고 `cdnEnabled`가 true이면 기본값 “AzureMediaStreamingPlatformCdnProfile”이 사용됩니다. 제공된 `cdnProfile`이 이미 있으면 그 아래에 엔드포인트가 생성됩니다. 프로필이 없으면 새 프로필이 자동으로 만들어집니다.
- `cdnProvider`: CDN을 사용 하는 경우 값을 전달할 수도 있습니다 `cdnProvider` . `cdnProvider`는 사용할 공급자를 제어합니다. 현재, "StandardVerizon", "PremiumVerizon" 및 "StandardAkamai"의 세 가지 값이 지원됩니다. 값을 제공 하지 않고 `cdnEnabled` true 이면 "StandardVerizon"이 사용 됩니다 (기본값).
- `crossSiteAccessPolicies`: 다양 한 클라이언트에 대해 사이트 간 액세스 정책을 지정 하는 데 사용 됩니다. 자세한 내용은 [도메인 간 정책 파일 사양](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) 및 [여러 도메인 간에 서비스를 사용하도록 지정](/previous-versions/azure/azure-services/gg185950(v=azure.100))을 참조하세요. 설정은 부드러운 스트리밍에만 적용 됩니다.
- `customHostNames`: 사용자 지정 호스트 이름으로 전송 된 트래픽을 허용 하도록 스트리밍 끝점을 구성 하는 데 사용 됩니다. 이 속성은 표준 및 프리미엄 스트리밍 끝점에 유효 하며 `cdnEnabled` : false 인 경우 설정할 수 있습니다.

    Media Services에서 도메인 이름의 소유권을 확인 해야 합니다. Media Services `CName` 는 사용 중인 도메인에 추가할 구성 요소로 Media Services 계정 ID를 포함 하는 레코드를 요구 하 여 도메인 이름 소유권을 확인 합니다. 예를 들어 “sports.contoso.com”을 스트리밍 엔드포인트에 대한 사용자 지정 호스트 이름으로 사용하려면 Media Services 확인 호스트 이름 중 하나를 가리키도록 `<accountId>.contoso.com`에 대한 레코드를 구성해야 합니다. 확인 호스트 이름은 verifydns .로 구성 되어 \<mediaservices-dns-zone> 있습니다.

    다음은 다양 한 Azure 지역에 대 한 확인 레코드에서 사용 되는 예상 DNS 영역입니다.
  
  - 북아메리카, 유럽, 싱가포르, 홍콩 특별 행정구, 일본:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - 중국:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    예를 들어 `CName` "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com"를 "verifydns.media.azure.net"로 매핑하는 레코드는 MEDIA SERVICES ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad에 contoso.com 도메인의 소유권이 있음을 증명 하므로 contoso.com의 모든 이름을 해당 계정 아래에 있는 스트리밍 끝점의 사용자 지정 호스트 이름으로 사용할 수 있습니다. 미디어 서비스 ID 값을 찾으려면 [Azure Portal](https://portal.azure.com/)로 이동한 후 미디어 서비스 계정을 선택합니다. **계정 ID** 는 페이지의 오른쪽 위에 표시 됩니다.

    레코드를 적절히 확인 하지 않고 사용자 지정 호스트 이름을 설정 하려고 하면 `CName` DNS 응답이 실패 하 여 일정 시간 동안 캐시 됩니다. 적절한 레코드가 준비되고 나면, 캐시된 응답의 유효성이 다시 확인될 때까지 잠시 시간이 소요될 수 있습니다. 사용자 지정 도메인에 대 한 DNS 공급자에 따라 레코드의 유효성을 다시 검사 하는 데 몇 분에서 1 시간까지 소요 됩니다.

    에 매핑되는 외에도 `CName` `<accountId>.<parent domain>` `verifydns.<mediaservices-dns-zone>` `CName` 사용자 지정 호스트 이름 (예: `sports.contoso.com` )을 Media Services 스트리밍 끝점의 호스트 이름 (예:)에 매핑하는 다른를 만들어야 합니다 `amstest-usea.streaming.media.azure.net` .

    > [!NOTE]
    > 동일한 데이터 센터에 있는 스트리밍 끝점은 동일한 사용자 지정 호스트 이름을 공유할 수 없습니다.

    현재 Media Services는 사용자 지정 도메인을 사용하는 TLS를 지원하지 않습니다.

- `maxCacheAge` -미디어 조각과 주문형 매니페스트의 스트리밍 끝점에서 설정한 기본 최대 기간 HTTP 캐시 제어 헤더를 재정의 합니다. 이 값은 초 단위로 설정됩니다.
- `resourceState` -

    - 중지 됨: 스트리밍 끝점을 만든 후의 초기 상태입니다.
    - 시작 중: 실행 중 상태로 전환 하 고 있습니다.
    - 실행 중: 클라이언트에 콘텐츠를 스트리밍할 수 있습니다.
    - 크기 조정: 배율 단위가 늘어나거나 감소 하 고 있습니다.
    - 중지 중: 중지 됨 상태로 전환 되 고 있습니다.
    - 삭제 중: 삭제 중

- `scaleUnits`: 200 Mbps 단위로 구입할 수 있는 전용 송신 용량을 제공 합니다. **프리미엄** 유형으로 전환해야 할 경우 `scaleUnits`를 조정합니다.

## <a name="why-use-multiple-streaming-endpoints"></a>여러 스트리밍 끝점을 사용 하는 이유

단일 스트리밍 끝점은 라이브 및 주문형 비디오를 모두 스트리밍할 수 있으며, 대부분의 고객은 하나의 스트리밍 끝점만 사용 합니다. 이 섹션에서는 여러 스트리밍 끝점을 사용 해야 하는 이유에 대 한 몇 가지 예를 제공 합니다.

* 각 예약 단위는 200 Mbps의 대역폭을 허용 합니다. 대역폭이 2000 Mbps (2gbps) 이상이 필요한 경우 두 번째 스트리밍 끝점과 부하 분산을 사용 하 여 추가 대역폭을 제공할 수 있습니다.

    그러나 CDN은 스트리밍 콘텐츠의 규모를 확장 하는 가장 좋은 방법 이지만 CDN이 2gbps 이상 끌어오는 많은 콘텐츠를 제공 하는 경우에는 추가 스트리밍 끝점 (원본)을 추가할 수 있습니다. 이 경우 두 스트리밍 끝점에서 균형을 유지 하는 콘텐츠 Url을 전달 해야 합니다. 이 방법은 트래픽 관리자를 통해 임의로 각 원본에 요청을 전송 하는 것 보다 더 나은 캐싱을 제공 합니다. 
    
    > [!TIP]
    > 일반적으로 CDN이 2 g b를 초과 하는 경우 잘못 구성 된 것일 수 있습니다 (예: 원본 차폐 없음).
    
* 다른 CDN 공급자의 부하를 분산 합니다. 예를 들어 Verizon CDN을 사용 하도록 기본 스트리밍 끝점을 설정 하 고 Akamai를 사용 하는 두 번째 스트리밍 끝점을 만들 수 있습니다. 그런 다음 다중 CDN 분산을 얻기 위해 둘 사이에 부하 분산을 추가 합니다. 

    그러나 고객은 단일 원본을 사용 하 여 여러 CDN 공급자에서 부하 분산을 수행 하는 경우가 많습니다.
* 혼합 콘텐츠 스트리밍: 주문형 라이브 및 비디오입니다. 

    라이브 및 주문형 콘텐츠에 대 한 액세스 패턴은 매우 다릅니다. 라이브 콘텐츠는 동일한 콘텐츠에 대해 한 번에 많은 수요를 가져오는 경향이 있습니다. 주문형 비디오 콘텐츠 (인스턴스에 대 한 긴 비상 보관 콘텐츠)는 동일한 콘텐츠에 대 한 사용량이 적습니다. 따라서 캐싱은 라이브 콘텐츠에서 매우 잘 작동 하지만 긴 비상 콘텐츠에서는 잘 작동 하지 않습니다.

    고객이 라이브 콘텐츠를 주로 감시 하지만 가끔 주문형 콘텐츠를 감시 하 고 동일한 스트리밍 끝점에서 제공 되는 시나리오를 고려해 보세요. 주문형 콘텐츠를 적게 사용 하면 라이브 콘텐츠에 더 잘 저장 되는 캐시 공간을 차지 하 게 됩니다. 이 시나리오에서는 하나의 스트리밍 끝점에서 라이브 콘텐츠를 제공 하 고 다른 스트리밍 끝점에서 긴 비상 콘텐츠를 제공 하는 것이 좋습니다. 이렇게 하면 라이브 이벤트 콘텐츠의 성능이 향상 됩니다.
    
## <a name="scaling-streaming-with-cdn"></a>CDN을 사용하여 스트리밍 크기 조정

다음 문서를 참조하세요.

- [CDN 개요](../../cdn/cdn-overview.md)
- [CDN을 사용하여 스트리밍 크기 조정](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>질문 하 고 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="see-also"></a>참고 항목

[동적 패키징](dynamic-packaging-overview.md)

## <a name="next-steps"></a>다음 단계

[스트리밍 엔드포인트 관리](manage-streaming-endpoints-howto.md)
