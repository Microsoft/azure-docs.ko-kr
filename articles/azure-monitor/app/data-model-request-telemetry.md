---
title: Azure Application Insights 원격 분석 데이터 모델 - 요청 원격 분석 | Microsoft Docs
description: 요청 원격 분석을 위한 Azure Application Insights 데이터 모델
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: fef016d87cc60bc916fdcb08f92171e115221fe5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60900530"
---
# <a name="request-telemetry-application-insights-data-model"></a>요청 원격 분석: Application Insights 데이터 모델

[Application Insights](../../azure-monitor/app/app-insights-overview.md)에서 요청 원격 분석 항목은 애플리케이션에 대한 외부 요청으로 트리거되는 실행의 논리적 순서를 나타냅니다. 모든 요청 실행은 모든 실행 매개 변수를 포함하는 고유한 `ID` 및 `url`로 식별됩니다. 논리적 `name`으로 요청을 그룹화하고 이 요청의 `source`를 정의할 수 있습니다. 코드 실행으로 `success` 또는 `fail`이 발생할 수 있으며 특정 `duration` 동안 지속됩니다. success(성공) 및 failure(실패) 실행은 모두 `resultCode`별로 그룹화할 수 있습니다. 봉투 (envelope) 수준에 정의된 요청 원격 분석의 시작 시간입니다.

요청 원격 분석은 사용자 지정 `properties` 및 `measurements`를 사용하여 표준 확장성 모델을 지원합니다.

## <a name="name"></a>이름

요청의 이름은 요청을 처리하기 위해 진행된 코드 경로를 나타냅니다. 더 나은 요청 그룹화를 허용하는 낮은 카디널리티 값입니다. HTTP 요청의 경우 HTTP 메서드 및 실제 `id` 값이 없는 `GET /values/{id}`와 같은 URL 경로 템플릿을 나타냅니다.

Application Insights 웹 SDK는 요청 이름을 대/소문자를 바꾸지 않고 “있는 그대로” 전송합니다. UI의 그룹화는 대/소문자를 구분하므로 `GET /Home/Index`와 `GET /home/INDEX`는 동일한 컨트롤러 및 작업 실행을 발생하더라도 다른 것으로 계산됩니다. 그 이유는 URL이 일반적으로 [대/소문자를 구분](https://www.w3.org/TR/WD-html40-970708/htmlweb.html)하기 때문입니다. 대문자로 입력한 URL에 대해 `404`가 항상 발생하는지 확인하고 싶을 수 있습니다. ASP.NET 웹 SDK에서 사용 하 여 자세한 on 요청 이름 컬렉션을 읽을 수는 [블로그 게시물](https://apmtips.com/blog/2015/02/23/request-name-and-url/)합니다.

최대 길이: 1024자

## <a name="id"></a>ID

요청 호출 인스턴스의 식별자입니다. 요청 및 기타 원격 분석 항목 간 상관 관계에 사용됩니다. ID는 전역적으로 고유해야 합니다. 자세한 내용은 [상관 관계](../../azure-monitor/app/correlation.md) 페이지를 참조하세요.

최대 길이: 128자

## <a name="url"></a>Url

모든 쿼리 문자열 매개 변수를 사용하는 요청 URL입니다.

최대 길이: 2048자

## <a name="source"></a>원본

요청의 원본입니다. 호출자의 계측 키 또는 호출자의 IP 주소를 예로 들 수 있습니다. 자세한 내용은 [상관 관계](../../azure-monitor/app/correlation.md) 페이지를 참조하세요.

최대 길이: 1024자

## <a name="duration"></a>기간

요청 기간은 `DD.HH:MM:SS.MMMMMM` 형식으로 나타냅니다. `1000`일보다 작은 양수여야 합니다. 요청 원격 분석은 처음과 끝이 있는 작업을 나타내므로 이 필드는 필수입니다.

## <a name="response-code"></a>응답 코드

요청 실행의 결과입니다. HTTP 요청에 대한 HTTP 상태 코드입니다. 다른 요청 형식에 대한 `HRESULT` 값 또는 예외 형식일 수 있습니다.

최대 길이: 1024자

## <a name="success"></a>성공

성공 또는 실패한 호출을 나타냅니다. 이 필드는 필수입니다. 명시적으로 `false`로 설정되지 않은 경우 - 요청이 성공으로 간주됩니다. 작업이 예외에 의해 중단되었거나 오류 결과 코드를 반환한 경우 이 값을 `false`로 설정합니다.

웹 애플리케이션의 경우 응답 코드가 `400`보다 작거나 `401`과 같은 경우 Application Insights는 요청을 성공한 것으로 정의합니다. 그러나 기본 매핑이 애플리케이션의 의미 체계와 일치하지 않는 경우가 있습니다. 응답 코드 `404`는 정규 흐름의 일부일 수 있는 "기록 없음"을 나타낼 수 있습니다. 또한 끊어진 연결을 나타낼 수도 있습니다. 끊어진 연결의 경우 좀 더 고급 논리를 구현할 수도 있습니다. 끊어진 연결이 동일한 사이트에 있는 경우에만 URL 참조를 분석하여 실패로 표시할 수 있습니다. 또는 회사의 모바일 애플리케이션에서 액세스할 때 실패로 표시합니다. 마찬가지로 `301` 및 `302`는 리디렉션을 지원하지 않는 클라이언트에서 액세스될 때 실패를 나타냅니다.

부분적으로 수락된 콘텐츠 `206`은 전체 요청의 실패를 나타낼 수 있습니다. 예를 들어 Application Insights 엔드포인트는 원격 분석 항목의 일괄 처리를 단일 요청으로 받습니다. 일괄 처리의 일부 항목이 성공적으로 처리되지 않으면 `206`을 반환합니다. `206` 비율이 늘어나면 조사해야 하는 문제가 발생했음을 나타냅니다. 성공이 별도 응답 코드 측면에서는 더 나쁠 수 있는 `207` 다중 상태에도 유사한 논리가 적용됩니다.

[블로그 게시물](https://apmtips.com/blog/2016/12/03/request-success-and-response-code/)에서 요청 결과 코드 및 상태 코드에 대한 자세한 내용을 확인할 수 있습니다.

## <a name="custom-properties"></a>사용자 지정 속성

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>사용자 지정 측정

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>다음 단계

- [사용자 지정 요청 원격 분석을 작성합니다](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest).
- Application Insights 형식 및 데이터 모델에 대한 자세한 내용은 [데이터 모델](data-model.md)을 참조하세요.
- 자세한 방법 Application Insights를 사용하여 [ASP.NET Core 애플리케이션을 구성](../../azure-monitor/app/asp-net.md)하는 방법을 알아봅니다.
- Application Insights에서 지원되는 [플랫폼](../../azure-monitor/app/platforms.md)을 확인합니다.
