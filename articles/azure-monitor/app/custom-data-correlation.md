---
title: Azure Application Insights | Microsoft Docs
description: ''
services: application-insights
documentationcenter: ''
author: eternovsky
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.author: Evgeny.Ternovsky
ms.openlocfilehash: cbb144cc8aac6dc8e90d196147b0c154471b7239
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60901495"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>사용자 지정 데이터 원본을 사용하여 Application Insights 데이터 상호 연결

Application Insights는 여러 다른 데이터 형식을 수집합니다(예외, 추적, 페이지 보기 및 기타). 애플리케이션의 성능, 안정성 및 사용량을 조사하기에 충분하지만 Application Insights에서 저장된 데이터를 완전히 사용자 지정 데이터 집합에 상호 연결하는 것이 유용한 경우도 있습니다.

사용자 지정 데이터를 사용하려는 상황은 다음과 같습니다.

- 데이터 보강 또는 조회 테이블: 예를 들어 서버 이름을 서버의 소유자 및 찾을 수 있는 랩 위치로 보완합니다. 
- Application Insights 데이터 원본과 상관 관계: 예를 들어 배송 시간 예측값의 정확도를 확인하려면 구매 처리 서비스의 정보와 웹 스토어에서 구매에 대한 데이터를 상관 관계를 만듭니다. 
- 완전한 사용자 지정 데이터: 대부분의 고객은 Application Insights를 지원하는 Azure Monitor 로그 플랫폼의 쿼리 언어 및 성능을 즐겨 사용하고 Application Insights와 전혀 관련되지 않은 데이터를 쿼리하는 데 사용합니다. 예를 들어 [여기]( https://blogs.catapultsystems.com/cfuller/archive/2017/10/04/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/)에서 설명한 대로 스마트 홈 설치의 일부로 태양 패널 성능을 추적하려고 합니다.

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Application Insights와 사용자 지정 데이터 상관 관계 만들기 

Application Insights가 강력한 Azure Monitor 로그 플랫폼에서 지원되므로 Azure Monitor의 모든 기능을 사용하여 데이터를 수집할 수 있습니다. 그런 다음, Azure Monitor 로그에서 사용할 수 있는 데이터와 이 사용자 지정 데이터의 상관 관계를 만드는 “조인” 연산자를 사용하여 쿼리를 작성합니다. 

## <a name="ingesting-data"></a>데이터 수집

이 섹션에서는 Azure Monitor 로그로 데이터를 가져오는 방법을 검토합니다.

아직 없는 경우 [이러한 지침](../learn/quick-collect-azurevm.md)에 따라 "작업 영역 만들기" 단계를 포함하여 새 Log Analytics 작업 영역을 프로비전합니다.

Azure Monitor로 로그 데이터 보내기를 시작하려면 다음과 같은 여러 옵션이 있습니다.

- 동기화 메커니즘의 경우 [데이터 수집기 API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)를 직접 호출하거나 논리 앱 커넥터를 사용할 수 있습니다. 단순히 "Azure Log Analytics"를 찾아서 "데이터 보내기" 옵션을 선택하면 됩니다.

  ![스크린샷 선택 및 작업](./media/custom-data-correlation/01-logic-app-connector.png)  

- 비동기 옵션의 경우 데이터 수집기 API를 사용하여 처리 파이프라인을 빌드합니다. 자세한 내용은 [이 아티클](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api)을 참조하세요.

## <a name="correlating-data"></a>데이터 상관 관계 설정

Application Insights는 Azure Monitor 로그 플랫폼을 기반으로 합니다. 따라서 [교차 리소스 조인](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search)을 사용하여 Application Insights 데이터와 Azure Monitor로 수집한 모든 데이터의 상관 관계를 만들 수 있습니다.

예를 들어 "myLA"라는 Log Analytics 작업 영역에서 "LabLocations_CL"이라는 테이블에 랩 인벤토리 및 위치를 수집할 수 있습니다. 그런 다음, “myAI”라는 Application Insights 앱에서 추적 요청을 검토하고 앞에서 언급한 사용자 지정 테이블에 저장된 이러한 머신의 위치에 대한 요청을 처리하는 머신 이름의 상관 관계를 만들려는 경우 Application Insights 또는 Azure Monitor 컨텍스트에서 다음 쿼리를 실행할 수 있습니다.

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>다음 단계

- [데이터 수집기 API](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) 참조를 체크 아웃합니다.
- [교차 리소스 조인](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search)에 대한 자세한 정보입니다.
