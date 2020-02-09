---
title: Bing Web Search API에 대한 분석 추가
titleSuffix: Azure Cognitive Services
description: Bing Statistics는 Bing Image Search API에 대한 분석을 제공합니다. 분석에는 호출량, 주요 쿼리 문자열, 지리적 분포 등이 포함됩니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 5d1b0b19523eb37aa83aa59b24114be9f76ffa55
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882761"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Bing Search API에 분석 추가

Bing Statistics는 Bing Search API에 대 한 분석을 제공 합니다. 이러한 분석에는 통화 볼륨, 상위 쿼리 문자열, 지리적 분포 등이 포함 됩니다. Azure 리소스로 이동 하 고 **Bing Statistics 사용**을 클릭 하 여 [Azure Portal](https://ms.portal.azure.com) 에서 bing 통계를 사용 하도록 설정할 수 있습니다.

> [!IMPORTANT]
> * Bing Statistics는 무료 평가판 구독 또는 무료 `F0` 가격 책정 계층의 리소스에서 사용할 수 없습니다.
> * 제3자에게 배포할 애플리케이션을 만들 때는 Bing Statistics 대시보드를 통해 사용 가능한 어떤 데이터도 사용할 수 없습니다.
> * Bing 통계를 사용 하도록 설정 하면 구독 요금이 약간 늘어납니다. 자세한 내용은 [가격 책정](https://aka.ms/bingstatisticspricing) 을 참조 하세요.


다음 이미지는 각 Bing Search API 엔드포인트에 대해 사용 가능한 분석을 보여 줍니다.

![엔드포인트 지원 매트릭스별 배포](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>분석 액세스

Bing 업데이트는 24 시간 마다 분석 데이터를 분석 하 고 [분석 대시보드에서](https://bingapistatistics.com)액세스할 수 있는 최대 13 개월 분량의 기록을 유지 관리 합니다. Bing 통계를 등록 하는 데 사용한 것과 동일한 Microsoft 계정 (MSA)를 사용 하 여 로그인 했는지 확인 합니다.

> [!NOTE]  
> * 대시보드에 메트릭이 표시되는 데 최대 24시간이 걸릴 수 있습니다. 대시보드에는 데이터가 마지막으로 업데이트된 날짜 및 시간이 표시됩니다.  
> * 메트릭은 Bing Statistics 추가 기능을 사용하도록 설정할 때부터 사용할 수 있습니다.

## <a name="filter-the-data"></a>데이터 필터링

기본적으로 차트와 그래프는 사용자가 액세스할 수 있는 모든 메트릭과 데이터를 표시 합니다. 관심 있는 리소스, 지역/국가, 엔드포인트 및 보고 기간을 선택하여 차트 및 그래프에 표시되는 데이터를 필터링할 수 있습니다. 다음 필터를 변경할 수 있습니다.

- **리소스 ID**: Azure 구독을 식별하는 고유한 리소스 ID입니다. 둘 이상의 Bing Search API 계층을 구독하는 경우 이 목록에는 여러 ID가 포함됩니다. 기본적으로 모든 리소스가 선택됩니다.  
  
- **지역/국가**: 결과가 나오는 지역/국가입니다. 예를 들면 en-us(영어, 미국)와 같습니다. 기본값으로 모든 국가가 선택되어 있습니다. `en-WW` 시장 출시는 콜에서 시장을 지정 하지 않고 bing에서 사용자의 시장을 확인할 수 없는 경우 bing에서 사용 하는 시장입니다.  
  
- **엔드포인트**: Bing Search API 엔드포인트입니다. 목록에는 유료 구독이 있는 모든 엔드포인트가 포함됩니다. 기본적으로 모든 엔드포인트가 선택됩니다.  

- **시간 프레임**: 보고 기간입니다. 다음을 지정할 수 있습니다.
  - **모두**: 최대 13 개월 분량의 데이터를 포함 합니다.  
  - **지난 24 시간**: 최근 24 시간 동안의 분석 포함  
  - **지난 주**: 지난 7 일간의 분석 포함  
  - **지난 달**: 이전 30 일간의 분석 포함  
  - **사용자 지정 날짜 범위**: 지정 된 날짜 범위에서 분석을 포함 합니다 (사용 가능한 경우).  

## <a name="charts-and-graphs"></a>차트 및 그래프

대시보드에는 선택한 엔드포인트에 사용할 수 있는 메트릭의 차트 및 그래프가 표시됩니다. 모든 엔드포인트에 모든 메트릭을 사용할 수 있는 것은 아닙니다. 각 엔드포인트의 차트와 그래프는 정적입니다(표시할 차트 및 그래프를 선택할 수 없음). 대시보드에는 데이터가 있는 차트 및 그래프만 표시됩니다.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

가능한 메트릭 및 엔드포인트 제한 사항은 다음과 같습니다.

- **호출량**: 보고 기간 동안 수행된 호출 수를 표시합니다. 보고 기간이 하루 동안인 경우 차트에는 시간당 호출 수가 표시됩니다. 그렇지 않은 경우, 차트에는 보고 기간 중 하루당 호출 수가 표시됩니다.  
  
  > [!NOTE]
  > 호출량은 청구 보고서마다 다를 수 있으며 일반적으로는 성공한 호출만 포함됩니다.

- **상위 쿼리**: 보고 기간 동안의 상위 쿼리 및 각 쿼리의 발생 수가 표시됩니다. 표시된 쿼리의 수를 구성할 수 있습니다. 예를 들어, 상위 25, 50 또는 75개 쿼리를 표시할 수 있습니다. 다음 엔드포인트에 대해서는 상위 쿼리를 사용할 수 없습니다.  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > 일부 쿼리 용어는 전자 메일, 전화 번호, SSN 등의 기밀 정보를 제거하도록 표시되지 않을 수 있습니다.

- **지리적 배포**: 검색 결과가 발생 하는 시장입니다. 예를 `en-us` 들면 (영어, 미국)입니다. Bing에서는 `mkt` 쿼리 매개 변수를 사용하여 지역/국가(지정된 경우)를 결정합니다. 그렇지 않은 경우, Bing은 호출자의 IP 주소와 같은 신호를 사용하여 지역/국가를 결정합니다.

- **응답 코드 배포**: 보고 기간 동안 수행된 모든 호출에 대한 HTTP 상태 코드입니다.

- **호출 원본 배포**: 사용자가 사용하는 브라우저 유형입니다. Microsoft Edge, Chrome, Safari, FireFox 등을 예로 들 수 있습니다. 브라우저 외부에서 수행 된 호출 (예: 봇, Postman 또는 콘솔 앱에서 말아 넘기기 사용)은 라이브러리 아래에 그룹화 됩니다. 원본은 요청의 사용자 에이전트 헤더 값을 사용하여 결정됩니다. 요청에 사용자 에이전트 헤더가 포함되지 않을 경우 Bing은 다른 신호에서 원본을 파생하려고 시도합니다.  

- **안전 검색 배포**: 안전 검색 값의 배포입니다. 예를 들면 off, moderate 또는 strict입니다. `safeSearch` 쿼리 매개 변수는 값(지정된 경우)을 포함합니다. 이렇게 하지 않으면 Bing은 기본적으로 moderate 값을 사용합니다.  

- **요청된 답변 배포**: `responseFilter` 쿼리 매개 변수에 요청한 Web Search API 응답입니다.  

- **반환된 답변 배포**: Web Search API가 응답에 반환한 답변입니다.

- **응답 서버 배포**: API 요청을 처리하는 애플리케이션 서버입니다. 가능한 값은 Bing.com(데스크톱 및 랩톱 디바이스에서 제공된 트래픽) 및 Bing.com-mobile(모바일 디바이스에서 제공된 트래픽)입니다. 서버는 요청의 사용자 에이전트 헤더 값을 사용하여 결정됩니다. 요청에 사용자 에이전트 헤더가 포함되지 않을 경우 Bing은 다른 신호에서 서버를 파생하려고 시도합니다.

## <a name="next-steps"></a>다음 단계

* [Bing Search API은 무엇 인가요?](bing-api-comparison.md)
* [Bing Search API 사용 및 표시 요구 사항](use-display-requirements.md)
