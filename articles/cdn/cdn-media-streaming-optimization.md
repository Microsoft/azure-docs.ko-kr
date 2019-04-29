---
title: Azure CDN을 통해 미디어 스트리밍 최적화
description: 부드러운 배달을 위한 스트리밍 미디어 파일 최적화
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: magattus
ms.openlocfilehash: 9802296170f07bb8599058e230798f647e900d4d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60636246"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Azure CDN을 통해 미디어 스트리밍 최적화 
 
인터넷에서 고화질 비디오의 사용이 증가하고 있으며 대용량 파일을 효율적으로 배달하기가 어렵습니다. 고객은 전 세계의 다양한 네트워크 및 클라이언트에서 주문형 비디오 또는 라이브 비디오 자산이 부드럽게 재생되기를 바랍니다. 원활하고 즐거운 소비자 환경을 보장하려면 미디어 스트리밍 파일에 대한 빠르고 효율적인 배달 메커니즘이 중요합니다.  

라이브 스트리밍 미디어는 크기가 크고 동시 시청자가 많기 때문에 배달하기가 특히 어렵습니다. 긴 지연으로 인해 사용자가 떠날 수도 있습니다. 라이브 스트림은 미리 캐시할 수 없고 긴 대기 시간은 시청자에게 허용되지 않으므로 비디오 조각을 시기 적절하게 배달해야 합니다. 

스트리밍의 요청 패턴 또한 몇 가지 새로운 과제를 제공합니다. 인기 있는 라이브 스트림의 경우나 새로운 시리즈가 주문형 비디오로 릴리스되는 경우 수천 수백만 명의 시청자가 동시에 스트림을 요청할 수 있습니다. 이 경우 자산이 아직 캐시되지 않았을 때 원본 서버가 과부하에 걸리지 않도록 하는 데 있어 스마트 요청 통합이 필수적입니다.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Microsoft의 Azure CDN에 대한 미디어 스트리밍 최적화

**Microsoft의 Azure CDN 표준** 엔드포인트는 일반 웹 배달 최적화 형식을 사용하여 스트리밍 미디어 자산을 직접 배달할 수 있습니다. 

**Microsoft의 Azure CDN 표준**에 대한 미디어 스트리밍 최적화는 배달에 개별 미디어 조각을 사용하는 라이브 또는 주문형 비디오 스트리밍 미디어에 효과적입니다. 이 프로세스는 점진적 다운로드 또는 바이트 범위 요청을 통해 전송되는 단일 대규모 자산과 다릅니다. 해당 스타일의 미디어 배달에 대한 자세한 내용은 [Azure CDN을 통해 대용량 파일 다운로드 최적화](cdn-large-file-optimization.md)를 참조하세요.

일반 미디어 배달 또는 주문형 비디오 미디어 배달 최적화 형식은 백 엔드 최적화가 포함된 Azure CDN(Content Delivery Network)을 사용하여 미디어 자산을 더 빠르게 배달합니다. 또한 시간이 지남에 따라 학습된 모범 사례에 따라 미디어 자산에 대한 구성을 사용합니다.

### <a name="partial-cache-sharing"></a>부분 캐시 공유
부분적인 캐시 공유는 CDN에서 부분적으로 캐시된 콘텐츠를 새 요청에 제공할 수 있도록 합니다. 예를 들어 CDN에 대한 첫 번째 요청에서 캐시가 누락되면 해당 요청이 원본 서버로 보내집니다. 이 불완전한 콘텐츠가 CDN 캐시에 로드되지만 CDN에 대한 다른 요청에서 이 데이터 가져오기를 시작할 수 있습니다. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Verizon의 Azure CDN에 대한 미디어 스트리밍 최적화

**Verizon의 Azure CDN 표준** 및 **Verizon의 Azure CDN 프리미엄** 엔드포인트는 일반 웹 배달 최적화 형식을 사용하여 스트리밍 미디어 자산을 직접 배달할 수 있습니다. 기본적으로 CDN에는 미디어 자산 배달에 직접 도움이 되는 몇 가지 기능이 있습니다.

### <a name="partial-cache-sharing"></a>부분 캐시 공유

부분적인 캐시 공유는 CDN에서 부분적으로 캐시된 콘텐츠를 새 요청에 제공할 수 있도록 합니다. 예를 들어 CDN에 대한 첫 번째 요청에서 캐시가 누락되면 해당 요청이 원본 서버로 보내집니다. 이 불완전한 콘텐츠가 CDN 캐시에 로드되지만 CDN에 대한 다른 요청에서 이 데이터 가져오기를 시작할 수 있습니다. 

### <a name="cache-fill-wait-time"></a>캐시 채우기 대기 시간

 부분 캐시 공유와 함께 유용한 캐시 채우기 대기 시간 기능은 HTTP 응답 헤더가 원본 서버에서 도착할 때까지 에지 서버에서 동일한 리소스에 대한 향후 요청을 보류하도록 합니다. 타이머가 만료되기 전에 원본 서버의 HTTP 응답 헤더가 도착하면 보류된 모든 요청이 증가하는 캐시에서 처리됩니다. 동시에 캐시는 원본 서버의 데이터로 채워집니다. 기본적으로 캐시 채우기 대기 시간은 3,000밀리초로 설정됩니다. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Akamai의 Azure CDN에 대한 미디어 스트리밍 최적화
 
**Akamai의 Azure CDN 표준**은 전세계 사용자에게 스트리밍 미디어 자산을 효율적으로 배달하는 기능을 제공합니다. 이 기능은 원본 서버의 부하를 줄이므로 대기 시간을 줄여주며, 표준 Akamai 가격 책정 계층으로 사용할 수 있습니다. 

**Akamai의 Azure CDN 표준**에 대한 미디어 스트리밍 최적화는 배달에 개별 미디어 조각을 사용하는 라이브 또는 주문형 비디오 스트리밍 미디어에 효과적입니다. 이 프로세스는 점진적 다운로드 또는 바이트 범위 요청을 통해 전송되는 단일 대규모 자산과 다릅니다. 해당 스타일의 미디어 배달에 대한 자세한 내용은 [대용량 파일 최적화](cdn-large-file-optimization.md)를 참조하세요.

일반 미디어 배달 또는 주문형 비디오 미디어 배달 최적화 형식은 백 엔드 최적화가 포함된 CDN을 사용하여 미디어 자산을 더 빠르게 배달합니다. 또한 시간이 지남에 따라 학습된 모범 사례에 따라 미디어 자산에 대한 구성을 사용합니다.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>미디어 스트리밍을 최적화하도록 Akamai CDN 엔드포인트 구성
 
Azure Portal을 통해 대용량 파일 배달을 최적화하도록 CDN(Content Delivery Network) 엔드포인트를 구성할 수 있습니다. REST API나 클라이언트 SDK를 사용할 수도 있습니다. 다음 단계에서는 **Akamai의 Azure CDN 표준** 프로필에 대해 Azure Portal을 통한 프로세스를 보여줍니다.

1. 새 엔드포인트를 추가하려면 Akamai **CDN 프로필** 페이지에서 **엔드포인트**를 선택합니다.
  
    ![새 엔드포인트](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. **Optimized for**(최적화 대상) 드롭다운 목록에서 주문형 비디오 자산에 대한 **주문형 비디오 미디어 스트리밍**을 선택합니다. 라이브 및 주문형 비디오 스트리밍을 조합하는 경우 **일반 미디어 스트리밍**을 선택합니다.

    ![스트리밍 선택됨](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
엔드포인트를 만든 후에 특정 기준과 일치하는 모든 파일에 대해 최적화를 적용합니다. 다음 섹션에서는 이 프로세스에 대해 자세히 설명합니다. 

### <a name="caching"></a>구성

**Akamai의 Azure CDN 표준**에서 자산이 스트리밍 매니페스트 또는 조각임이 감지되면 일반 웹 배달과 다른 캐싱 만료 시간을 사용합니다. 다음 표의 전체 목록을 참조하세요. 언제나 그렇듯이, 원본 서버에서 보낸 cache-control 또는 Expires 헤더가 사용됩니다. 자산이 미디어 자산이 아닌 경우 일반 웹 배달에 대한 만료 시간을 사용하여 캐시합니다.

많은 사용자가 아직 존재하지 않는 조각을 요청할 때는 짧은 부정 캐싱 시간이 원본 서버 오프로드에 유용합니다. 예를 들어 원본 서버에서 해당 시간(초) 동안 패킷을 사용할 수 없는 라이브 스트림이 있습니다. 또한 비디오 내용이 일반적으로 수정되지 않기 때문에 캐싱 간격은 길수록 원본 서버의 요청을 오프로드하는 데 도움이 됩니다.
 

|   | 일반 웹 배달 | 일반 미디어 스트리밍 | 주문형 비디오 미디어 스트리밍  
--- | --- | --- | ---
Caching: Positive <br> HTTP 200, 203, 300, <br> 301, 302 및 410 | 7 일 |365일 | 365일   
Caching: Negative <br> HTTP 204, 305, 404 <br> 및 405 | 없음 | 1초 | 1초
 
### <a name="deal-with-origin-failure"></a>원본 오류 처리  

일반 미디어 배달 및 주문형 비디오 미디어 배달에도 일반적인 요청 패턴에 대한 모범 사례에 기반한 원본 시간 제한 및 다시 시도 로그가 있습니다. 예를 들어 일반 미디어 배달은 라이브 및 주문형 비디오 미디어 배달을 위한 것이므로 시간에 민감한 라이브 스트리밍 특성으로 인해 더 짧은 연결 시간 제한을 사용합니다.

연결 시간이 초과되면 CDN에서 "504 - 게이트웨이 시간 초과" 오류를 클라이언트로 보내기 전에 여러 번 다시 시도합니다. 

파일이 파일 형식 및 크기 조건 목록과 일치하면 CDN에서 미디어 스트리밍에 대한 동작을 사용합니다. 그렇지 않으면 일반 웹 배달을 사용합니다.
   
### <a name="conditions-for-media-streaming-optimization"></a>미디어 스트리밍 최적화에 대한 조건 

다음 표에서는 미디어 스트리밍 최적화를 위해 충족되어야 하는 조건 집합을 보여 줍니다. 
 
지원되는 스트리밍 유형 | 파일 확장명  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, key, ts, aac
Adobe HDS | f4m, f4x, drmmeta, bootstrap, f4f,<br>Seg-Frag URL 구조 <br> (일치하는 regex: ^(/.*)Seq(\d+)-Frag(\d+)
DASH | mpd, dash, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
부드러운 스트리밍 | /manifest/, /QualityLevels/Fragments/
  
 
