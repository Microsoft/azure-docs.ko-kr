---
title: 스트리밍 끝점 (원본)
titleSuffix: Azure Media Services
description: 클라이언트 플레이어 앱 또는 CDN (Content Delivery Network)에 직접 콘텐츠를 전달 하는 동적 패키징 및 스트리밍 서비스인 스트리밍 끝점 (원본)에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: c1e9be605a6f01695f2472ae76a9e5a786388aa0
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77206109"
---
# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Azure Media Services의 스트리밍 끝점 (원본)

Microsoft Azure Media Services에서 [스트리밍 끝점](https://docs.microsoft.com/rest/api/media/streamingendpoints) 은 일반 스트리밍 미디어 프로토콜 (HLS 또는 대시) 중 하나를 사용 하 여 라이브 및 주문형 콘텐츠를 클라이언트 플레이어 앱에 직접 배달할 수 있는 동적 (just-in-time) 패키징 및 원본 서비스를 나타냅니다. 또한 **스트리밍 끝점** 은 업계 최고의 drms에 동적 (just-in-time) 암호화를 제공 합니다.

Media Services 계정을 만들 경우 **기본** 스트리밍 엔드포인트가 중지됨 상태로 만들어집니다. **기본** 스트리밍 끝점은 삭제할 수 없습니다. 계정으로 더 많은 스트리밍 끝점을 만들 수 있습니다 ( [할당량 및 제한 사항](limits-quotas-constraints.md)참조).

> [!NOTE]
> 비디오 스트리밍을 시작하려면 비디오를 스트리밍하려는 **스트리밍 엔드포인트**를 시작해야 합니다.
>
> 스트리밍 끝점이 실행 중 상태인 경우에만 요금이 청구 됩니다.

## <a name="naming-convention"></a>명명 규칙

스트리밍 URL의 호스트 이름 형식은 `{servicename}-{accountname}-{regionname}.streaming.media.azure.net`입니다. 여기서 `servicename`은 스트리밍 끝점 이름 또는 라이브 이벤트 이름입니다.

기본 스트리밍 끝점을 사용 하는 경우 `servicename` 생략 되므로 URL은 `{accountname}-{regionname}.streaming.azure.net`입니다.

### <a name="limitations"></a>제한 사항

* 스트리밍 끝점 이름의 최대 값은 24 자입니다.
* 이름은 `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`[regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference) 패턴을 따라야 합니다.

## <a name="types"></a>유형

**표준** (미리 보기) 및 **프리미엄**의 두 가지 **스트리밍 끝점** 유형이 있습니다. 이러한 유형은 스트리밍 엔드포인트에 할당하는 배율 단위(`scaleUnits`) 수로 정의됩니다.

다음 표에 해당 유형이 설명되어 있습니다.

|Type|배율 단위|Description|
|--------|--------|--------|  
|**Standard**|0|기본 스트리밍 끝점은 **표준** 유형이 며 `scaleUnits`를 조정 하 여 프리미엄 유형으로 변경할 수 있습니다.|
|**Premium**|>0|**프리미엄** 스트리밍 끝점은 고급 워크 로드에 적합 하며, 확장 가능한 전용 대역폭 용량을 제공 합니다. `scaleUnits` (스트리밍 단위)를 조정 하 여 **프리미엄** 유형으로 이동 합니다. `scaleUnits`는 200Mbps 단위로 구입할 수 있는 전용 송신 용량을 제공합니다. **프리미엄** 유형을 사용 하는 경우 사용 하도록 설정 된 각 단위는 앱에 추가 대역폭 용량을 제공 합니다. |

> [!NOTE]
> 대량 인터넷 사용자에 게 콘텐츠를 배달 하려는 고객의 경우 스트리밍 끝점에서 CDN을 사용 하도록 설정 하는 것이 좋습니다.

SLA에 대 한 자세한 내용은 [가격 책정 및 SLA](https://azure.microsoft.com/pricing/details/media-services/)를 참조 하세요.

## <a name="comparing-streaming-types"></a>스트리밍 유형 비교

기능|Standard|Premium
---|---|---
처리량 |CDN을 사용 하는 경우 최대 600 Mbps 및 보다 효과적인 처리량을 제공할 수 있습니다.|SU(스트리밍 단위)당 200Mbps 는 CDN을 사용 하는 경우 훨씬 더 효율적인 처리량을 제공할 수 있습니다.
CDN|Azure CDN, 타사 CDN 또는 CDN이 없습니다.|Azure CDN, 타사 CDN 또는 CDN이 없습니다.
청구를 계산합니다.| 매일|매일
동적 암호화|yes|yes
동적 패키징|yes|yes
확장|대상 처리량까지 자동으로 확장합니다.|추가 SUs
IP 필터링/G20/사용자 지정 호스트 <sup>1</sup>|yes|yes
점진적 다운로드|yes|yes
권장 사용량 |대부분의 스트리밍 시나리오에 권장됩니다.|전문 사용량입니다.

<sup>1</sup> 끝점에서 CDN을 사용 하도록 설정 하지 않은 경우에만 스트리밍 끝점에서 직접 사용 됩니다.<br/>

## <a name="properties"></a>속성

이 섹션에서는 일부 스트리밍 끝점 속성에 대해 자세히 설명 합니다. 새 스트리밍 엔드포인트를 만드는 방법의 예제와 모든 속성에 대한 설명을 보려면 [스트리밍 엔드포인트](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)를 참조하세요.

- `accessControl`:이 스트리밍 끝점에 대해이 끝점에 연결할 수 있는 Akamai 서명 헤더 인증 키와 IP 주소에 대 한 다음 보안 설정을 구성 하는 데 사용 됩니다. 이 속성은 `cdnEnabled`가 false로 설정 된 경우에만 설정할 수 있습니다.

- `cdnEnabled`:이 스트리밍 끝점에 대 한 Azure CDN 통합을 사용할 수 있는지 여부를 나타냅니다 (기본적으로 사용 안 함). `cdnEnabled`를 true로 설정하면 `customHostNames` 및 `accessControl` 구성이 사용되지 않도록 설정됩니다.

    모든 데이터 센터에서 Azure CDN 통합을 지원하는 것은 아닙니다. 데이터 센터에서 Azure CDN 통합을 사용할 수 있는지 확인 하려면 다음 단계를 수행 합니다.

  - `cdnEnabled`를 true로 설정합니다.
  - 현재 지역에서 CDN 기능을 사용할 수 없으므로 "스트리밍 끝점 CdnEnabled 속성을 true로 설정할 수 없습니다." 라는 메시지와 함께 `HTTP Error Code 412` (PreconditionFailed)에 대해 반환 된 결과를 확인 합니다.

    이 오류가 발생 하면 데이터 센터에서이를 지원 하지 않습니다. 다른 데이터 센터를 사용해 보세요.

- `cdnProfile`: `cdnEnabled`을 true로 설정 하면 `cdnProfile` 값을 전달할 수도 있습니다. `cdnProfile`은 CDN 엔드포인트이 생성되는 CDN 프로필의 이름입니다. 기존 cdnProfile을 제공하거나 새로 만들 수 있습니다. 값이 NULL이고 `cdnEnabled`가 true이면 기본값 “AzureMediaStreamingPlatformCdnProfile”이 사용됩니다. 제공된 `cdnProfile`이 이미 있으면 그 아래에 엔드포인트가 생성됩니다. 프로필이 없으면 새 프로필이 자동으로 만들어집니다.
- `cdnProvider`: CDN을 사용 하는 경우 `cdnProvider` 값을 전달할 수도 있습니다. `cdnProvider`는 사용할 공급자를 제어합니다. 현재, "StandardVerizon", "PremiumVerizon" 및 "StandardAkamai"의 세 가지 값이 지원됩니다. 값을 제공 하지 않고 `cdnEnabled` true 이면 "StandardVerizon"이 사용 됩니다 (기본값).
- `crossSiteAccessPolicies`: 다양 한 클라이언트에 대해 사이트 간 액세스 정책을 지정 하는 데 사용 됩니다. 자세한 내용은 [도메인 간 정책 파일 사양](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) 및 [여러 도메인 간에 서비스를 사용하도록 지정](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx)을 참조하세요. 설정은 부드러운 스트리밍에만 적용 됩니다.
- `customHostNames`: 사용자 지정 호스트 이름으로 전송 된 트래픽을 허용 하도록 스트리밍 끝점을 구성 하는 데 사용 됩니다. 이 속성은 표준 및 프리미엄 스트리밍 끝점에 대해 유효 하며 `cdnEnabled`경우 false로 설정할 수 있습니다.

    Media Services에서 도메인 이름의 소유권을 확인 해야 합니다. Media Services는 사용 중인 도메인에 추가할 구성 요소로 Media Services 계정 ID를 포함 하는 `CName` 레코드를 요구 하 여 도메인 이름 소유권을 확인 합니다. 예를 들어 “sports.contoso.com”을 스트리밍 엔드포인트에 대한 사용자 지정 호스트 이름으로 사용하려면 Media Services 확인 호스트 이름 중 하나를 가리키도록 `<accountId>.contoso.com`에 대한 레코드를 구성해야 합니다. 확인 호스트 이름은 verifydns.\<mediaservices-dns-zone>으로 구성됩니다.

    다음은 다양 한 Azure 지역에 대 한 확인 레코드에서 사용 되는 예상 DNS 영역입니다.
  
  - 북아메리카, 유럽, 싱가포르, 홍콩 특별 행정구, 일본:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - 중국:

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    예를 들어 "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com"를 "verifydns.media.azure.net"로 매핑하는 `CName` 레코드는 Media Services ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad에 contoso.com 도메인의 소유권이 있음을 증명 하므로 contoso.com의 모든 이름을 해당 계정 아래 스트리밍 끝점의 사용자 지정 호스트 이름으로 사용할 수 있습니다. 미디어 서비스 ID 값을 찾으려면 [Azure Portal](https://portal.azure.com/)로 이동한 후 미디어 서비스 계정을 선택합니다. **계정 ID** 는 페이지의 오른쪽 위에 표시 됩니다.

    `CName` 레코드를 적절 하 게 확인 하지 않고 사용자 지정 호스트 이름을 설정 하려고 하면 DNS 응답이 실패 하 여 일정 시간 동안 캐시 됩니다. 적절한 레코드가 준비되고 나면, 캐시된 응답의 유효성이 다시 확인될 때까지 잠시 시간이 소요될 수 있습니다. 사용자 지정 도메인에 대 한 DNS 공급자에 따라 레코드의 유효성을 다시 검사 하는 데 몇 분에서 1 시간까지 소요 됩니다.

    `<accountId>.<parent domain>`를 `verifydns.<mediaservices-dns-zone>`에 매핑하는 `CName` 외에도 `sports.contoso.com`스트리밍 끝점의 호스트 이름 (예: Media Services)에 사용자 지정 호스트 이름 (예: `amstest-usea.streaming.media.azure.net`)을 매핑하는 다른 `CName`를 만들어야 합니다.

    > [!NOTE]
    > 동일한 데이터 센터에 있는 스트리밍 끝점은 동일한 사용자 지정 호스트 이름을 공유할 수 없습니다.

    현재 Media Services에서는 사용자 지정 도메인에 대 한 SSL을 지원 하지 않습니다.

- `maxCacheAge`-미디어 조각과 주문형 매니페스트의 스트리밍 끝점에 의해 설정 된 기본 최대 기간 HTTP 캐시 제어 헤더를 재정의 합니다. 이 값은 초 단위로 설정됩니다.
- `resourceState` -

    - 중지 됨: 스트리밍 끝점을 만든 후의 초기 상태입니다.
    - 시작 중: 실행 중 상태로 전환 하 고 있습니다.
    - 실행 중: 클라이언트에 콘텐츠를 스트리밍할 수 있습니다.
    - 크기 조정: 배율 단위가 늘어나거나 감소 하 고 있습니다.
    - 중지 중: 중지 됨 상태로 전환 되 고 있습니다.
    - 삭제 중: 삭제 중

- `scaleUnits`: 200 Mbps 단위로 구입할 수 있는 전용 송신 용량을 제공 합니다. **프리미엄** 유형으로 전환해야 할 경우 `scaleUnits`를 조정합니다.

## <a name="working-with-cdn"></a>CDN 사용

대부분의 경우 CDN이 사용되도록 설정되어 있을 것입니다. 그러나 최대 동시성이 500 뷰어 보다 낮은 것으로 예상 되는 경우 CDN이 동시성을 통해 최적으로 확장 되므로 CDN을 사용 하지 않도록 설정 하는 것이 좋습니다.

### <a name="considerations"></a>고려 사항

* CDN을 사용 하도록 설정할지 여부에 관계 없이 스트리밍 끝점 `hostname` 및 스트리밍 URL은 동일 하 게 유지 됩니다.
* CDN을 사용 하거나 사용 하지 않고 콘텐츠를 테스트 하는 기능이 필요한 경우 CDN이 사용 하도록 설정 되지 않은 또 다른 스트리밍 끝점을 만듭니다.

### <a name="detailed-explanation-of-how-caching-works"></a>캐싱 작동 방식에 대한 자세한 설명

Cdn을 사용 하는 스트리밍 끝점에 필요한 대역폭 양은 다양 하므로 CDN을 추가할 때 특정 대역폭 값은 없습니다. 많은 내용이 콘텐츠 형식, 인기 자료, 비트 전송률 및 프로토콜에 따라 달라 집니다. CDN은 요청 되는 항목만 캐시 합니다. 즉, 비디오 조각이 캐시 된 경우에는 널리 사용 되는 콘텐츠가 CDN에서 직접 제공 됩니다. 일반적으로 많은 사람들이 정확히 동일한 것을 보게 하므로 라이브 콘텐츠는 캐시될 확률이 높습니다. 주문형 콘텐츠는 널리 사용 되는 콘텐츠 중 일부를 포함할 수 있기 때문에 다소 까다롭습니다. 수많은 비디오 자산이 널리 사용 되 고 있지 않은 경우 (한 주에 한 번 또는 두 번만 표시), 수천 명의 사용자가 다른 비디오를 모두 감시 하는 경우 CDN이 훨씬 효율적입니다. 이 캐시가 손실되면 스트리밍 엔드포인트에 대한 부하를 늘립니다.

또한 적응 스트리밍의 작동 방식도 고려해야 합니다. 각 개별 비디오 조각은 자체 엔터티로 캐시됩니다. 예를 들어 특정 비디오가 처음으로 시청 될 때를 가정 합니다. 뷰어에서 몇 초 동안만 감시 하는 것을 건너뛴 경우, 사용자가 감시에서 감시 하는 항목과 연결 된 비디오 조각만 표시 됩니다. 적응 스트리밍을 사용하면 비디오의 비트 전송률이 일반적으로 5~ 7 사이의 차이를 보입니다. 한 사용자가 하나의 비트 전송률을 감시 하 고 다른 사용자가 다른 비트 전송률을 감시 하는 경우 각 사용자는 CDN에서 별도로 캐시 됩니다. 두 명의 사용자가 동일한 비트 전송률을 시청 하는 경우에도 서로 다른 프로토콜을 통해 스트리밍할 수 있습니다. 각 프로토콜(HLS, MPEG DASH, 부드러운 스트리밍)은 별도로 캐시됩니다. 따라서 각 비트 전송률 및 프로토콜은 개별적으로 캐시되고, 요청된 비디오 조각만 캐시됩니다.

### <a name="enable-azure-cdn-integration"></a>Azure CDN 통합 사용

> [!IMPORTANT]
> 평가판 또는 학생용 Azure 계정에 대해 CDN을 사용 하도록 설정할 수 없습니다.
>
> CDN 통합은 연방 정부 및 중국 지역을 제외한 모든 Azure 데이터 센터에서 사용 하도록 설정 됩니다.

CDN을 사용 하 여 스트리밍 끝점을 프로 비전 한 후에는 스트리밍 끝점을 CDN 끝점에 매핑하기 위해 DNS 업데이트를 수행 하기 전에 Media Services에 정의 된 대기 시간이 있습니다.

나중에 CDN을 사용/사용 안 함으로 설정하려면 스트리밍 엔드포인트가 **중지됨** 상태여야 합니다. Azure CDN 통합이 사용하도록 설정되고 변경 내용이 모든 CDN POP에서 활성화되려면 최대 2시간이 걸릴 수 있습니다. 그러나 스트리밍 끝점을 시작 하 고 스트리밍 끝점에서 중단 없이 스트리밍할 수 있으며, 통합이 완료 되 면 CDN에서 스트림이 배달 됩니다. 프로 비전 기간 동안에는 스트리밍 끝점이 **시작** 상태가 되 고 성능이 저하 될 수 있습니다.

표준 스트리밍 끝점을 만들 때 기본적으로 표준 Verizon을 사용 하 여 구성 됩니다. REST Api를 사용 하 여 Premium Verizon 또는 표준 Akamai 공급자를 구성할 수 있습니다.

Azure Media Services와 Azure CDN의 통합은 **Verizon의 Azure CDN**에서 표준 스트리밍 엔드포인트에 구현됩니다. 프리미엄 스트리밍 엔드포인트는 모든 **Azure CDN 가격 책정 및 공급자**를 사용하여 구성할 수 있습니다. 

> [!NOTE]
> Azure CDN에 대 한 자세한 내용은 [CDN 개요](../../cdn/cdn-overview.md)를 참조 하세요.

### <a name="determine-if-dns-change-was-made"></a>DNS 변경을 수행 했는지 확인 합니다.

https://www.digwebinterface.com를 사용 하 여 스트리밍 끝점에 대 한 DNS 변경 (트래픽이 Azure CDN로 전달 됨)을 확인할 수 있습니다. 결과에 azureedge.net 도메인 이름이 있으면 이제 해당 트래픽이 CDN을 가리킵니다.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="see-also"></a>참고 항목

[CDN 개요](../../cdn/cdn-overview.md)

## <a name="next-steps"></a>다음 단계

[이 리포지토리](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)의 샘플은 .NET을 사용하여 기본 스트리밍 엔드포인트를 시작하는 방법을 보여 줍니다.
