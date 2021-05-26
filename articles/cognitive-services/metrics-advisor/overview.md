---
title: Azure Metrics Advisor 서비스란?
titleSuffix: Azure Applied AI Services
description: Metrics Advisor란?
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 34ab524cfc00cf4f74d632883ffeb18b904342a6
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110376850"
---
# <a name="what-is-azure-metrics-advisor-preview"></a>Azure Metrics Advisor(미리 보기)란? 

Metrics Advisor는 AI를 사용하여 시계열 데이터에서 데이터 모니터링과 변칙 검색을 수행하는 [Azure Applied AI Services](../../applied-ai-services/what-are-applied-ai-services.md)의 일부입니다. 이 서비스는 데이터에 모델을 적용하는 프로세스를 자동화하고 데이터 수집, 변칙 검색 및 진단용 API 및 웹 기반 작업 영역 세트를 제공하므로 기계 학습을 몰라도 상관없습니다. 개발자는 서비스 위에 AIOps, 예측 유지 관리 및 비즈니스 모니터 애플리케이션을 빌드할 수 있습니다. Metrics Advisor를 사용하여 다음을 수행합니다.

* 여러 데이터 원본에서 다차원 데이터 분석
* 변칙 식별 및 상관 관계 지정
* 데이터에서 사용되는 변칙 검색 모델을 구성하고 미세 조정
* 변칙 진단 및 근본 원인 분석 지원

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="Metrics Advisor 개요":::

이 설명서에는 다음과 같은 유형의 문서가 포함되어 있습니다.
* [빠른 시작](./Quickstarts/web-portal.md)은 서비스를 호출하고 짧은 시간 내에 결과를 얻을 수 있는 단계별 지침입니다. 
* [방법 가이드](./how-tos/onboard-your-data.md)에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.
* [개념 문서](glossary.md)에서는 서비스의 기능 및 기능에 대한 자세한 설명을 제공합니다.

## <a name="connect-to-a-variety-of-data-sources"></a>다양한 데이터 원본에 연결

Metrics Advisor는 다음을 포함한 여러 데이터 저장소의 [다차원 메트릭 데이터에 연결하고 수집](how-tos/onboard-your-data.md)할 수 있습니다. SQL Server, Azure Blob Storage, MongoDB 등

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>사용하기 쉽고 사용자 지정이 가능한 변칙 검색

* Metrics Advisor는 데이터에 가장 적합한 모델을 자동으로 선택하므로 기계 학습을 몰라도 상관없습니다.
* [다차원 메트릭](glossary.md#multi-dimensional-metric) 내의 모든 시계열을 자동으로 모니터링합니다.
* [매개 변수 튜닝](how-tos/configure-metrics.md) 및 [대화형 피드백](how-tos/anomaly-feedback.md)을 사용하여 데이터에 적용된 모델 및 향후 변칙 검색 결과를 사용자 지정합니다.

## <a name="real-time-alerts-through-multiple-channels"></a>여러 채널을 통한 실시간 경고

변칙이 검색될 때마다 Metrics Advisor는 이메일 후크, 웹후크 및 Azure DevOps 후크와 같은 후크를 사용하여 여러 채널을 통해 [실시간 경고를 보낼](how-tos/alerts.md) 수 있습니다. 유연한 경고 규칙을 사용하면 전송되는 경고와 대상을 사용자 지정할 수 있습니다.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>변칙을 분석하는 스마트 분석 인사이트

다차원 메트릭에서 검색된 변칙을 분석하고 가장 가능성이 높은 근본 원인, 진단 트리, 메트릭 드릴링 등을 비롯한 [스마트 진단 인사이트](how-tos/diagnose-incident.md)를 생성합니다. [메트릭 그래프](how-tos/metrics-graph.md)를 구성하면 인시던트를 시각화하는 데 도움이 되는 교차 메트릭 분석을 사용하도록 설정할 수 있습니다.


## <a name="typical-workflow"></a>일반적인 워크플로

워크플로는 간단합니다. 데이터를 온보딩한 후 변칙 검색을 미세 조정하고 시나리오에 맞게 구성을 만들 수 있습니다.

1. Metrics Advisor용 [Azure 리소스를 만듭니다](https://go.microsoft.com/fwlink/?linkid=2142156). 
2. 웹 포털을 사용하여 첫 번째 모니터를 빌드합니다.
    1. 데이터 온보딩
    2. 변칙 검색 미세 조정
    3. 알림 구독
    4. 진단 인사이트 보기
3. REST API를 사용하여 인스턴스를 사용자 지정합니다.

## <a name="next-steps"></a>다음 단계

* 빠른 시작 살펴보기: [웹에서 첫 번째 메트릭 모니터링](quickstarts/web-portal.md)
* 빠른 시작 살펴보기: [REST API를 사용하여 솔루션 사용자 지정](./quickstarts/rest-api-and-client-library.md)
