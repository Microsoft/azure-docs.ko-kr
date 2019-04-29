---
title: Azure Media Services의 스트리밍 엔드포인트 | Microsoft Docs
description: 이 문서에서는 스트리밍 엔드포인트란 무엇이고 Azure Media Services가 어떤 스트리밍 엔드포인트를 사용하는지 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 8b6deadca610916a10f719d715fe6a17e29148bb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125426"
---
# <a name="streaming-endpoints"></a>스트리밍 엔드포인트

Microsoft AMS(Azure Media Services)에서 [스트리밍 엔드포인트](https://docs.microsoft.com/rest/api/media/streamingendpoints) 엔터티는 추가 배포를 위해 CDN(Content Delivery Network) 또는 클라이언트 플레이어 애플리케이션에 직접 콘텐츠를 배달할 수 있는 스트리밍 서비스를 나타냅니다. **스트리밍 엔드포인트** 서비스의 아웃바운드 스트림은 라이브 스트림 또는 Media Services 계정에 주문형 비디오 자산이 될 수 있습니다. Media Services 계정을 만들 경우 **기본** 스트리밍 엔드포인트가 중지됨 상태로 만들어집니다. **기본** 스트리밍 엔드포인트는 삭제할 수 없습니다. 계정에서 추가 스트리밍 엔드포인트를 만들 수 있습니다. 

> [!NOTE]
> 비디오 스트리밍을 시작하려면 비디오를 스트리밍하려는 **스트리밍 엔드포인트**를 시작해야 합니다. 
>  
> 스트리밍 엔드포인트가 실행 중인 상태일 때만 요금이 청구됩니다.

## <a name="naming-convention"></a>명명 규칙

기본 엔드포인트: `{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

추가 엔드포인트: `{EndpointName}-{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

## <a name="types"></a>형식  

**스트리밍 엔드포인트**의 유형으로는 **표준** 및 **프리미엄** 두 가지가 있습니다. 이러한 유형은 스트리밍 엔드포인트에 할당하는 배율 단위(`scaleUnits`) 수로 정의됩니다. 

다음 표에 해당 유형이 설명되어 있습니다.  

|type|배율 단위|설명|
|--------|--------|--------|  
|**표준 스트리밍 엔드포인트**(권장)|0|기본 스트리밍 끝점은는 **표준** 하지만 프리미엄 유형으로 변경할 수 있습니다.<br/> 표준 형식에는 거의 모든 스트리밍 시나리오 및 대상 크기에 대 한 권장 되는 옵션입니다. **표준** 유형은 아웃바운드 대역폭을 자동으로 확장합니다. 이 유형의 스트리밍 끝점에서 처리량이 최대 600mbps입니다. CDN에 캐시할 비디오 조각을 스트리밍 끝점 대역폭을 사용 하지 마십시오.<br/>요구 사항이 매우 까다로운 고객을 위해, Media Services는 가장 규모가 큰 인터넷 대상 사용자를 위해 용량을 스케일 아웃할 수 있는 **프리미엄** 스트리밍 엔드포인트를 제공합니다. 큰 대상 그룹 및 동시 시청자를 예상 하는 경우에 문의 amsstreaming\@이동 해야 하는지 여부에 대 한 지침은 microsoft.com 합니다 **Premium** 형식입니다. |
|**프리미엄 스트리밍 엔드포인트**|>0|**프리미엄** 스트리밍 엔드포인트는 고급 워크로드에 적합하며, 확장성 있는 전용 대역폭 용량을 제공합니다. `scaleUnits`를 조정하여 **프리미엄** 유형으로 이동합니다. `scaleUnits`는 200Mbps 단위로 구입할 수 있는 전용 송신 용량을 제공합니다. **프리미엄** 유형을 사용할 경우 설정된 각 단위가 애플리케이션에 대한 추가 대역폭 용량을 제공합니다. |
 
## <a name="comparing-streaming-types"></a>스트리밍 유형 비교

### <a name="features"></a>기능

기능|Standard|Premium
---|---|---
처음 15일 무료| 예 |아닙니다.
처리량 |Azure CDN을 사용하지 않는 경우 최대 600Mbps입니다. CDN을 사용하여 크기를 조정합니다.|SU(스트리밍 단위)당 200Mbps CDN을 사용하여 크기를 조정합니다.
CDN|Azure CDN, 타사 CDN 또는 CDN 없음.|Azure CDN, 타사 CDN 또는 CDN 없음.
청구를 계산합니다.| 매일|매일
동적 암호화|예|예
동적 패키징|예|예
확장|대상 처리량까지 자동으로 확장합니다.|추가 스트리밍 단위
IP 필터링 G20/사용자 지정 호스트 <sup>1</sup>|예|예
점진적 다운로드|예|예
권장 사용량 |대부분의 스트리밍 시나리오에 권장됩니다.|전문 사용량입니다.<br/>필요한 경우 표준 이상이 필요할 수 있습니다. 동시 뷰어의 규모가 50,000명을 초과할 것으로 예상되는 경우 문의하세요(amsstreaming@microsoft.com).

<sup>1</sup> 만 CDN 끝점에서 사용 하지 않는 경우 스트리밍 끝점에서 직접 사용 합니다.

## <a name="properties"></a>properties 

이 섹션에서는 스트리밍 끝점의 속성 중 일부에 대 한 세부 정보를 제공 합니다. 새 스트리밍 엔드포인트를 만드는 방법의 예제와 모든 속성에 대한 설명을 보려면 [스트리밍 엔드포인트](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)를 참조하세요. 

- `accessControl` -이 스트리밍 끝점에 대해 다음 보안 설정을 구성 하는 데 사용 합니다. 이 엔드포인트에 연결할 수 있는 Akamai 서명 헤더 인증 키 및 IP 주소.<br />이 속성 에서만 설정할 수 있습니다 때 `cdnEnabled` 가 false로 설정 합니다.
- `cdnEnabled` -이 스트리밍 끝점에 대 한 Azure CDN 통합을 사용 하도록 설정된 (기본적으로 해제 됨) 인지 여부를 나타냅니다. `cdnEnabled`를 true로 설정하면 `customHostNames` 및 `accessControl` 구성이 사용되지 않도록 설정됩니다.
  
    모든 데이터 센터에서 Azure CDN 통합을 지원하는 것은 아닙니다. 데이터 센터에 Azure CDN 통합을 사용할 수 있는지 여부를 확인 하려면 다음을 수행 합니다.
 
  - 설정 하 여 `cdnEnabled` true로 합니다.
  - 반환된 된 결과 확인 한 `HTTP Error Code 412` (PreconditionFailed) "스트리밍 끝점 CdnEnabled 속성 설정할 수 없습니다 true로 현재 지역에서 CDN 기능을 사용할 수 없는." 메시지와 함께 

    이 오류가 발생하면 데이터 센터가 통합을 지원하지 않는 것입니다. 다른 데이터 센터를 시도해보세요.
- `cdnProfile` - `cdnEnabled` 로 설정 된 true 전달할 수도 있습니다 `cdnProfile` 값입니다. `cdnProfile`은 CDN 엔드포인트가 생성되는 CDN 프로필의 이름입니다. 기존 cdnProfile을 제공하거나 새로 만들 수 있습니다. 값이 NULL이고 `cdnEnabled`가 true이면 기본값 “AzureMediaStreamingPlatformCdnProfile”이 사용됩니다. 제공된 `cdnProfile`이 이미 있으면 그 아래에 엔드포인트가 생성됩니다. 프로필이 없으면 새 프로필을 자동으로 생성 됩니다.
- `cdnProvider` -전달할 수도 있습니다 CDN을 사용 하는 경우 `cdnProvider` 값입니다. `cdnProvider`는 사용할 공급자를 제어합니다. 현재 "StandardVerizon", "PremiumVerizon" 및 "StandardAkamai"의 세 값이 지원됩니다. 값이 제공 되지 경우 및 `cdnEnabled` 가 true 이면 "StandardVerizon" (즉, 기본값) 사용 됩니다.
- `crossSiteAccessPolicies` -다양 한 클라이언트에 대 한 사이트 간 액세스 정책을 지정 하는 데 사용 합니다. 자세한 내용은 [도메인 간 정책 파일 사양](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) 및 [여러 도메인 간에 서비스를 사용하도록 지정](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx)을 참조하세요.<br/>만을 부드러운 스트리밍으로 적용 됩니다.
- `customHostNames` -사용자 지정 호스트 이름에 전달 하는 트래픽을 허용 하도록 스트리밍 끝점을 구성 하는 데 사용 합니다.  이 속성이 표준 및 프리미엄 스트리밍 끝점에 대 한 유효 하 고 설정할 수 있습니다 `cdnEnabled`: false.
    
    Media Services에서 도메인 이름의 소유권을 확인 해야 합니다. Media Services는 도메인 이름 소유권을 요구 하 여 확인을 `CName` Media Services 계정 ID를 사용 하 여 도메인에 추가할 구성 요소를 포함 하는 레코드입니다. 예를 들어 “sports.contoso.com”을 스트리밍 엔드포인트에 대한 사용자 지정 호스트 이름으로 사용하려면 Media Services 확인 호스트 이름 중 하나를 가리키도록 `<accountId>.contoso.com`에 대한 레코드를 구성해야 합니다. 확인 호스트 이름은 verifydns.\<mediaservices-dns-zone>으로 구성됩니다. 

    다음은 서로 다른 Azure 지역에 대 한 확인 레코드에서 사용할 예상된 DNS 영역입니다.
  
  - North America, Europe, 싱가포르, 홍콩 특별 행정구, 일본:
      
    - `media.azure.net`
    - `verifydns.media.azure.net`
      
  - 중국:
        
    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`
        
    예를 들어, 한 `CName` "945a4c4e-28ea-45 cd-8ccb-a519f6b700ad.contoso.com" "verifydns.media.azure.net"에 매핑하는 레코드를 Media Services ID 945a4c4e-28ea-45cd-8ccb-a519f6b700ad에 contoso.com 도메인의 소유권이 있으므로 증명 해당 계정에서 스트리밍 끝점에 대 한 사용자 지정 호스트 이름으로 사용할 contoso.com 아래의 모든 이름을 사용 하도록 설정 합니다. 미디어 서비스 ID 값을 찾으려면 [Azure Portal](https://portal.azure.com/)로 이동한 후 미디어 서비스 계정을 선택합니다. 합니다 **계정 ID** 위에 표시 되는 페이지의 오른쪽입니다.
        
    `CName` 레코드를 적절히 확인하지 않고 사용자 지정 호스트 이름을 설정하려고 하면 DNS 응답이 실패하고 일정 시간 동안 캐시됩니다. 적절한 레코드가 준비되고 나면, 캐시된 응답의 유효성이 다시 확인될 때까지 잠시 시간이 소요될 수 있습니다. 사용자 지정 도메인에 대한 DNS 공급자에 따라, 레코드의 유효성이 다시 확인되기까지 몇 분에서 1시간 정도 걸릴 수 있습니다.
        
    외에 `CName` 매핑되 `<accountId>.<parent domain>` 를 `verifydns.<mediaservices-dns-zone>`, 또 다른을 만들어야 `CName` 사용자 지정 호스트 이름에 매핑되는 (예를 들어, `sports.contoso.com`)에 Media Services 스트리밍 끝점의 호스트 이름에 (예를 들어 `amstest-usea.streaming.media.azure.net`).
 
    > [!NOTE]
    > 동일한 데이터 센터에 있는 스트리밍 끝점에서 동일한 사용자 지정 호스트 이름을 공유할 수 없습니다.

    현재 Media Services는 사용자 지정 도메인을 사용 하 여 SSL을 지원 하지 않습니다. 
    
- `maxCacheAge` -재정의 기본 최대 처리 기간 HTTP 캐시 헤더 스트리밍 끝점이 미디어 조각 및 주문형 매니페스트에서 설정한를 제어 합니다. 이 값은 초 단위로 설정됩니다.
- `resourceState` -

    - 중지 된-스트리밍 끝점을 만든 후의 초기 상태
    - 실행 중인 상태로 전환 되어-시작
    - 실행-은 클라이언트에 콘텐츠를 스트리밍할 수 있습니다.
    - 크기 조정-확장 단위는 증가 또는 감소
    - 중지-중지 됨된 상태로 전환 되
    - 삭제-삭제 하는 중
    
- `scaleUnits` -200mbps 단위로 구입할 수 있는 전용된 송신 용량을 사용 하 여 제공 합니다. **프리미엄** 유형으로 전환해야 할 경우 `scaleUnits`를 조정합니다.

## <a name="working-with-cdn"></a>CDN 사용

대부분의 경우 CDN이 사용되도록 설정되어 있을 것입니다. 그러나 최대 동시성이 500명보다 적은 수의 독자로 예상될 경우 CDN이 동시성에 따라 가장 잘 확장되므로 CDN을 사용하지 않도록 설정하는 것이 좋습니다.

### <a name="considerations"></a>고려 사항

* Streaming Endpoint `hostname` 및 스트리밍 URL은 CDN을 사용하도록 설정하는지 여부에 관계없이 동일하게 유지됩니다.
* CDN 없이 콘텐츠를 테스트 하는 기능을 해야 하는 경우 CDN이 사용 하도록 설정 되지 않은 또 다른 스트리밍 끝점을 만들 수 있습니다.

### <a name="detailed-explanation-of-how-caching-works"></a>캐싱 작동 방식에 대한 자세한 설명

CDN 지원 스트리밍 엔드포인트에 필요한 대역폭 양이 다르므로 CDN을 추가할 때 구체적인 대역폭 값은 없습니다. 콘텐츠 형식, 인기 정도, 비트 전송률 및 프로토콜 유형에 따라 많은 영향을 받습니다. CDN은 요청되는 항목만 캐시합니다. 즉, 비디오 조각이 캐시되면, 인기 있는 콘텐츠가 CDN에서 직접 제공되는 것입니다. 일반적으로 많은 사람들이 정확히 동일한 것을 보게 하므로 라이브 콘텐츠는 캐시될 확률이 높습니다. 인기 있는 콘텐츠도 있고 그렇지 않은 콘텐츠도 있으므로 주문형 콘텐츠를 좀 더 까다로울 수 있습니다. 수백만 개의 비디오 자산이 있고 모두 인기가 없지만(1주일에 1명 또는 2명의 독자) 수천 명의 사람들이 모두 다른 비디오를 보는 경우 CDN이 훨씬 덜 효과적입니다. 이 캐시가 손실되면 스트리밍 엔드포인트에 대한 부하를 늘립니다.
 
또한 적응 스트리밍의 작동 방식도 고려해야 합니다. 각 개별 비디오 조각은 자체 엔터티로 캐시됩니다. 예를 들어 특정 비디오가 처음 시청될 때 사용자가 여기저기를 이동하면서 몇 초 동안만 시청하게 되고, CDN에서 시청한 내용과 연결된 비디오 조각만 CDN에 캐시됩니다. 적응 스트리밍을 사용하면 비디오의 비트 전송률이 일반적으로 5~ 7 사이의 차이를 보입니다. 한 사람이 한 비트 전송률로 시청 중이고, 다른 사람이 다른 비트 전송률로 시청 중이면 CDN에서 각각 따로 캐시됩니다. 두 사람이 동일한 비트 전송률로 시청 중이면 다른 프로토콜을 통해 스트리밍할 수 있습니다. 각 프로토콜(HLS, MPEG DASH, 부드러운 스트리밍)은 별도로 캐시됩니다. 따라서 각 비트 전송률 및 프로토콜은 개별적으로 캐시되고, 요청된 비디오 조각만 캐시됩니다.

### <a name="enable-azure-cdn-integration"></a>Azure CDN 통합을 사용 하도록 설정

스트리밍 끝점을 사용 하 여 프로 비전 되 면 CDN 사용 있는 경우 Media Services에서 정의 된 대기 시간을 스트리밍 끝점 CDN 끝점에 매핑할 DNS 업데이트는 완료 되기 전에

나중에 CDN을 사용/사용 안 함으로 설정하려면 스트리밍 엔드포인트가 **중지됨** 상태여야 합니다. Azure CDN 통합이 사용하도록 설정되고 변경 내용이 모든 CDN POP에서 활성화되려면 최대 2시간이 걸릴 수 있습니다. 그러나 스트리밍 엔드포인트를 시작하고 스트리밍 엔드포인트에서 중단 없이 스트리밍할 수 있으며, 통합이 완료되면 CDN에서 스트림이 전송됩니다. 프로비저닝 기간 동안에는 스트리밍 엔드포인트가 **시작** 상태가 되고 성능 저하를 보일 수 있습니다.

표준 스트리밍 끝점을 만들 때 표준 verizon 기본적으로 구성 됩니다. REST Api를 사용 하 여 프리미엄 Verizon 또는 akamai 표준의 공급자를 구성할 수 있습니다. 

CDN 통합은 중국 및 연방 정부 지역을 제외한 모든 Azure 데이터 센터에서 사용됩니다.

> [!IMPORTANT]
> Azure Media Services와 Azure CDN의 통합은 **Verizon의 Azure CDN**에서 표준 스트리밍 엔드포인트에 구현됩니다. 프리미엄 스트리밍 엔드포인트는 모든 **Azure CDN 가격 책정 및 공급자**를 사용하여 구성할 수 있습니다. Azure CDN 기능에 대한 자세한 내용은 [CDN 개요](../../cdn/cdn-overview.md)를 참조하세요.

### <a name="determine-if-dns-change-has-been-made"></a>DNS 변경 된 내용이 있는지 확인 합니다.

사용 하 여 스트리밍 끝점 (Azure CDN에 트래픽을 보내지는)에 DNS 변경이 수행 되었을 경우를 확인할 수 있습니다 https://www.digwebinterface.com합니다. 결과 결과에 azureedge.net 도메인 이름이 있으면 트래픽은 이제를 가리키는지 CDN 합니다.

## <a name="provide-feedback"></a>피드백 제공

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

[이 리포지토리](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)의 샘플은 .NET을 사용하여 기본 스트리밍 엔드포인트를 시작하는 방법을 보여 줍니다.

