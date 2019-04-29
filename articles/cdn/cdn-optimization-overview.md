---
title: 콘텐츠 전송 형식에 맞게 Azure CDN 최적화
description: 콘텐츠 전송 형식에 맞게 Azure CDN 최적화
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
ms.date: 03/25/2019
ms.author: magattus
ms.openlocfilehash: 954d19fb557540e4fdc6b17f313127e01eba97a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60636329"
---
# <a name="optimize-azure-cdn-for-the-type-of-content-delivery"></a>콘텐츠 전송 형식에 맞게 Azure CDN 최적화

전 세계의 수많은 대상 고객에게 콘텐츠를 배달하는 경우 콘텐츠의 최적화된 배달을 보장해야 합니다. [Azure CDN(Content Delivery Network)](cdn-overview.md)은 보유한 콘텐츠의 형식에 따라 전송 환경을 최적화할 수 있습니다. 콘텐츠는 다운로드를 위한 웹 사이트, 라이브 스트림, 비디오 또는 대용량 파일일 수 있습니다. CDN 엔드포인트를 만들 때 **최적화됨** 옵션에서 시나리오를 지정합니다. 선택 항목에 따라 CDN 엔드포인트에서 배달되는 콘텐츠에 적용될 최적화가 결정됩니다.

최적화 선택 항목은 모범 사례 동작을 사용하여 콘텐츠 배달 성능을 향상시키고 원본 서버 오프로드를 개선하도록 설계되었습니다. 시나리오를 선택하면 부분 캐싱, 개체 청크, 원본 오류 다시 시도 정책 등에 대한 구성을 수정하여 성능에 영향을 줍니다. 

이 문서에서는 다양한 최적화 기능과 사용 시기에 대해 간략하게 설명합니다. 기능 및 제한 사항에 대한 자세한 내용은 각각의 개별 최적화 형식에 대한 해당 문서를 참조하세요.

> [!NOTE]
> CDN 엔드포인트를 만들 때 **최적화 기준** 옵션은 엔드포인트가 만들어지는 프로필 유형에 따라 달라질 수 있습니다. Azure CDN 공급자는 시나리오에 따라 다양한 방법으로 향상된 기능을 적용합니다. 

## <a name="provider-options"></a>공급자 옵션

**Microsoft의 Azure CDN 표준** 프로필은 다음 최적화를 지원합니다.

* [일반 웹 배달](#general-web-delivery) 이 최적화는 미디어 스트리밍 및 큰 파일 다운로드에도 사용됩니다.

> [!NOTE]
> Microsoft의 동적 사이트 가속을 통해 제공 됩니다 [Azure 프런트 도어 서비스](https://docs.microsoft.com/azure/frontdoor/front-door-overview)합니다.

**Verizon의 Azure CDN 표준** 및 **Verizon의 Azure CDN 프리미엄** 프로필은 다음 최적화를 지원합니다.

* [일반 웹 배달](#general-web-delivery) 이 최적화는 미디어 스트리밍 및 큰 파일 다운로드에도 사용됩니다.

* [동적 사이트 가속](#dynamic-site-acceleration) 


**Akamai의 Azure CDN 표준** 프로필은 다음 최적화를 지원합니다.

* [일반 웹 배달](#general-web-delivery) 

* [일반 미디어 스트리밍](#general-media-streaming)

* [주문형 비디오 미디어 스트리밍](#video-on-demand-media-streaming)

* [대용량 파일 다운로드](#large-file-download)

* [동적 사이트 가속](#dynamic-site-acceleration) 

다양한 공급자 간의 성능 차이를 테스트하여 배달에 가장 적합한 공급자를 선택하는 것이 좋습니다.

## <a name="select-and-configure-optimization-types"></a>최적화 형식 선택 및 구성

CDN 엔드포인트를 만들 때 엔드포인트를 전송하려는 시나리오 및 콘텐츠 형식과 가장 잘 맞는 최적화 형식을 선택하세요. **일반 웹 배달**이 기본 선택 항목입니다. **Akamai의 기존 Azure CDN 표준** 엔드포인트의 경우 언제든지 최적화 옵션을 업데이트할 수 있습니다. 이러한 변경으로 인해 Azure CDN에서 배달이 중단되지는 않습니다. 

1. **Akamai의 Azure CDN 표준** 프로필에서 엔드포인트를 선택합니다.

    ![엔드포인트 선택](./media/cdn-optimization-overview/01_Akamai.png)

2. 설정 아래에서 **최적화**를 선택합니다. 그런 다음, **다음에 대해 최적화됨** 드롭다운 목록에서 형식을 선택합니다.

    ![최적화 및 형식 선택](./media/cdn-optimization-overview/02_Select.png)

## <a name="optimization-for-specific-scenarios"></a>특정 시나리오에 대한 최적화

다음 시나리오 중 하나에 대해 CDN 엔드포인트를 최적화할 수 있습니다. 

### <a name="general-web-delivery"></a>일반 웹 배달

일반 웹 배달은 가장 일반적인 최적화 옵션입니다. 웹 페이지 및 웹 애플리케이션과 같은 일반 웹 콘텐츠 최적화를 위해 설계되었습니다. 이 최적화는 파일 및 비디오 다운로드에도 사용할 수 있습니다.

일반적인 웹 사이트에는 정적 및 동적 콘텐츠가 포함되어 있습니다. 정적 콘텐츠는 캐시되고 다른 사용자에게 배달될 수 있는 이미지, JavaScript 라이브러리 및 스타일시트를 포함합니다. 동적 콘텐츠는 사용자 프로필에 맞게 조정된 뉴스 항목과 같이 개별 사용자에 맞게 개인 설정됩니다. 장바구니 콘텐츠 같은 동적 콘텐츠는 각 사용자에게 고유하기 때문에 캐시되지 않습니다. 일반 웹 배달은 전체 웹 사이트를 최적화할 수 있습니다. 

> [!NOTE]
> **Akamai의 Azure CDN 표준** 프로필을 사용하는 경우에는 평균 파일 크기가 10MB 미만이면 이 최적화 유형을 선택하세요. 그렇지 않은 경우에는 평균 파일 크기가 10MB를 초과하면 **최적화 대상** 드롭다운 목록에서 **대용량 파일 다운로드**를 선택하세요.

### <a name="general-media-streaming"></a>일반 미디어 스트리밍

라이브 스트리밍과 주문형 비디오 스트리밍에 엔드포인트를 사용해야 하는 경우 일반 미디어 스트리밍 최적화를 선택하세요.

미디어 스트리밍은 시간에 민감합니다. 비디오 콘텐츠의 잦은 버퍼링처럼 클라이언트에 늦게 도착하는 패킷 때문에 시청 환경이 저하될 수 있기 때문입니다. 미디어 스트리밍 최적화는 미디어 콘텐츠 배달의 대기 시간을 줄이고 최종 사용자에게 부드러운 스트리밍 환경을 제공합니다. 

이 시나리오는 Azure Media Service 고객에게 일반적입니다. Azure Media Services를 사용하면 단일 스트리밍 엔드포인트를 라이브 스트리밍과 주문형 스트리밍에 모두 사용할 수 있습니다. 이 시나리오에서는 고객이 라이브 스트리밍에서 주문형 스트리밍으로 변경할 때 다른 엔드포인트로 전환할 필요가 없습니다. 일반 미디어 스트리밍 최적화는 이 유형의 시나리오를 지원합니다.

**Microsoft의 Azure CDN 표준**, **Verizon의 Azure CDN 표준** 및 **Verizon의 Azure CDN 프리미엄**의 경우 일반 웹 배달 최적화 유형을 사용하여 일반 스트리밍 미디어 콘텐츠를 제공합니다.

미디어 스트리밍 최적화에 대한 자세한 내용은 [미디어 스트리밍 최적화](cdn-media-streaming-optimization.md)를 참조하세요.

### <a name="video-on-demand-media-streaming"></a>주문형 비디오 미디어 스트리밍

주문형 비디오 미디어 스트리밍 최적화는 주문형 비디오 스트리밍 콘텐츠를 향상시킵니다. 주문형 비디오 스트리밍에 엔드포인트를 사용하는 경우 이 옵션을 사용하세요.

**Microsoft의 Azure CDN 표준**, **Verizon의 Azure CDN 표준** 및 **Verizon의 Azure CDN 프리미엄** 프로필의 경우 일반 웹 전송 최적화 유형을 사용하여 주문형 스트리밍 미디어 콘텐츠를 전송하세요.

미디어 스트리밍 최적화에 대한 자세한 내용은 [미디어 스트리밍 최적화](cdn-media-streaming-optimization.md)를 참조하세요.

> [!NOTE]
> CDN 엔드포인트에서 주로 주문형 비디오 콘텐츠를 제공하는 경우 이 최적화 형식을 사용합니다. 이 최적화 형식과 일반 미디어 스트리밍 최적화 형식 사이의 주요 차이점은 연결 다시 시도 시간 제한에 있습니다. 라이브 스트리밍 시나리오를 사용하는 경우 시간 제한이 더 짧습니다.
>

### <a name="large-file-download"></a>대용량 파일 다운로드

**Akamai의 Azure CDN 표준** 프로필을 사용할 경우 대용량 파일 다운로드는 10MB를 초과하는 콘텐츠에 최적화됩니다. 평균 파일 크기가 10MB보다 작은 경우 일반 웹 배달을 사용합니다. 평균 파일 크기가 일관되게 10MB보다 큰 경우 대용량 파일에 대해 별도의 엔드포인트를 만드는 것이 더 효율적일 수 있습니다. 예를 들어 펌웨어 또는 소프트웨어 업데이트는 일반적으로 대용량 파일입니다. 1.8GB보다 큰 파일을 전달하려면 큰 파일 다운로드 최적화가 필요합니다.

**Microsoft의 Azure CDN 표준**, **Verizon의 Azure CDN 표준** 및 **Verizon의 Azure CDN 프리미엄** 프로필의 경우 일반 웹 전송 최적화 유형을 사용하여 큰 파일 다운로드 콘텐츠를 전송합니다. 파일 다운로드 크기에는 제한이 없습니다.

대용량 파일 최적화에 대한 자세한 내용은 [대용량 파일 최적화](cdn-large-file-optimization.md)를 참조하세요.

### <a name="dynamic-site-acceleration"></a>동적 사이트 가속

 **Akamai의 Azure CDN 표준**, **Verizon의 Azure CDN 표준**, **Verizon의 Azure CDN 프리미엄** 프로필에 DSA(동적 사이트 가속)를 사용할 수 있습니다. 이 최적화에는 추가 사용 요금이 포함되며 자세한 내용은 [Content Delivery Network 가격](https://azure.microsoft.com/pricing/details/cdn/)을 참조하세요.

> [!NOTE]
> Microsoft의 동적 사이트 가속을 통해 제공 됩니다 [Azure 프런트 도어 서비스](https://docs.microsoft.com/azure/frontdoor/front-door-overview) 는 전역 [애니캐스트](https://en.wikipedia.org/wiki/Anycast) 앱 워크 로드를 전달할 Microsoft의 개인 글로벌 네트워크를 활용 하는 서비스입니다.

DSA에는 동적 콘텐츠의 대기 시간과 성능에 도움이 되는 다양한 기술이 포함되어 있습니다. 경로 및 네트워크 최적화, TCP 최적화 등이 이러한 기술에 포함됩니다. 

이 최적화를 사용하여 캐시할 수 없는 수많은 응답을 포함하는 웹앱을 가속화할 수 있습니다. 예를 들어 검색 결과, 체크 아웃 트랜잭션 또는 실시간 데이터가 있습니다. 정적 데이터에 핵심 Azure CDN 캐싱 기능을 계속 사용할 수 있습니다. 

동적 사이트 가속에 대한 자세한 내용은 [동적 사이트 가속](cdn-dynamic-site-acceleration.md)을 참조하세요.



