---
title: 데이터 쿼리-Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2의 데이터 쿼리 개념 및 REST API 개요.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: cbf1c678307d76361e6083f4e2231fb9e48a112f
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855149"
---
# <a name="querying-data-from-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2에서 데이터 쿼리

Azure Time Series Insights Gen2를 사용 하면 공용 surface Api를 통해 환경에 저장 된 메타 데이터 및 이벤트에 대 한 데이터를 쿼리할 수 있습니다. 이러한 Api는 [Azure Time Series Insights TSI 탐색기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)에서도 사용 됩니다.

Azure Time Series Insights Gen2에서 세 가지 기본 API 범주를 사용할 수 있습니다.

* **환경 api**: 이러한 api는 Azure Time Series Insights Gen2 환경 자체에 대 한 쿼리를 사용 하도록 설정 합니다. 이러한 도구는 호출자가 및 환경 메타 데이터에 액세스할 수 있는 환경 목록을 수집 하는 데 사용할 수 있습니다.
* **시계열 모델-쿼리 (TSM-Q) api**: 환경의 시계열 모델에 저장 된 메타 데이터에 대 한 CRUD (만들기, 읽기, 업데이트 및 삭제) 작업을 활성화 합니다. 인스턴스, 유형 및 계층 구조에 액세스 하 고 편집 하는 데 사용할 수 있습니다.
* **TSQ (Time Series Query) api**: 원본 공급자에서 기록 된 원격 분석 또는 이벤트 데이터를 검색 하 고 고급 스칼라 및 집계 함수를 사용 하 여 데이터에 대 한 성능 계산과 집계를 가능 하 게 합니다.

Azure Time Series Insights Gen2는 시계열 [변수](./concepts-variables.md)에서 계산을 표현 하는 데 사용 되는 다양 한 문자열 기반 식 언어인 [TSX (시계열 식)](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)를 사용 합니다.

## <a name="azure-time-series-insights-gen2-apis-overview"></a>Azure Time Series Insights Gen2 Api 개요

다음과 같은 핵심 API가 지원됩니다.

[![시계열 쿼리 개요](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Environment API

* [환경 가져오기 API](https://docs.microsoft.com/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment): 호출자에 게 액세스 권한이 부여 된 환경 목록을 반환 합니다.
* [환경 가용성 API 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/getavailability): 이벤트 타임 스탬프에 대 한 이벤트 수의 분포를 반환 합니다 `$ts` . 이 API는 시간 간격으로 분할 된 이벤트 수 (있는 경우)를 반환 하 여 환경에 이벤트가 있는지 확인 하는 데 도움이 됩니다.
* [이벤트 스키마 가져오기 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/geteventschema): 지정 된 검색 범위에 대 한 이벤트 스키마 메타 데이터를 반환 합니다. 이 API는 지정된 검색 범위의 스키마에서 사용 가능한 모든 메타데이터 및 속성을 검색할 수 있습니다.

## <a name="time-series-model-query-tsm-q-apis"></a>TSM-Q(Time Series Model-Query) API

이러한 대부분의 Api는 일괄 처리 실행 작업을 지원 하 여 여러 시계열 모델 엔터티에 대해 일괄 처리 CRUD 작업을 활성화 합니다.

* [모델 설정 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis): 기본 형식 및 환경의 모델 이름에 대해 *GET* 및 *PATCH* 를 사용 하도록 설정 합니다.
* [TYPES API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#types-api): 시계열 유형 및 연관 된 변수에 대 한 CRUD를 사용 하도록 설정 합니다.
* [계층 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#hierarchies-api): 시계열 계층 구조와 관련 필드 경로에 대 한 CRUD를 사용 하도록 설정 합니다.
* [INSTANCES API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#instances-api): 시계열 인스턴스와 연결 된 인스턴스 필드에 대해 CRUD를 사용 하도록 설정 합니다. 또한 인스턴스 API는 다음과 같은 작업을 지원 합니다.
  * [검색](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search): 인스턴스 특성에 따라 시계열 인스턴스 검색에 대 한 적중의 일부 목록을 검색 합니다.
  * [제안](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest): 인스턴스 특성에 따라 시계열 인스턴스 검색에 대 한 일부 적중 목록을 검색 하 고 제안 합니다.

## <a name="time-series-query-tsq-apis"></a>TSQ(Time Series Query) API

이러한 Api는 다중 계층 저장소 솔루션에서 매장 (웜 및 콜드) 모두에서 사용할 수 있습니다. 쿼리 URL 매개 변수를 사용 하 여 쿼리를 실행 해야 하는 [저장소 유형을](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters) 지정 합니다.

* [Get EVENTS API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents): 원본 공급자의 Azure Time Series Insights Gen2에 기록 된 대로 원시 이벤트와 관련 이벤트 타임 스탬프가 쿼리 및 검색을 사용 하도록 설정 합니다. 이 API는 지정 된 시계열 ID 및 검색 범위에 대 한 원시 이벤트 검색을 허용 합니다. 이 API는 선택한 입력에 대 한 전체 응답 데이터 집합을 검색 하는 페이지 매김을 지원 합니다.

  > [!IMPORTANT]

  > * 앞으로 제공 되는 [JSON 평면화 및 이스케이프 규칙 변경](https://docs.microsoft.com/azure/time-series-insights/ingestion-rules-update)의 일부로 배열은 **동적** 형식으로 저장 됩니다. 이 유형으로 저장 된 페이로드 속성은 **Get EVENTS API를 통해서만 액세스할 수**있습니다.

* [SERIES API 가져오기](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries): 원시 이벤트에 대 한 변수로 정의 된 계산을 적용 하 여 계산 된 값 및 관련 이벤트 타임 스탬프의 쿼리 및 검색을 수행할 수 있습니다. 이러한 변수는 시계열 모델에서 정의 하거나 쿼리에 인라인으로 제공할 수 있습니다. 이 API는 선택한 입력에 대 한 전체 응답 데이터 집합을 검색 하는 페이지 매김을 지원 합니다.

* [집계 계열 API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries): 원시 이벤트에 대 한 변수로 정의 된 계산을 적용 하 여 집계 된 값 및 관련 간격 타임 스탬프에 대 한 쿼리 및 검색을 가능 하 게 합니다. 이러한 변수는 시계열 모델에서 정의 하거나 쿼리에 인라인으로 제공할 수 있습니다. 이 API는 선택한 입력에 대 한 전체 응답 데이터 집합을 검색 하는 페이지 매김을 지원 합니다.
  
  지정 된 검색 범위 및 간격에 대해이 API는 시계열 ID에 대 한 변수 당 간격당 집계 된 응답을 반환 합니다. 응답 데이터 집합의 간격 수는 epoch 틱 (Unix epoch-Jan 1, 1970 이후 경과 된 시간 (밀리초))을 계산 하 고 틱을 쿼리에 지정 된 간격 범위 크기로 나누는 방법으로 계산 됩니다.

  응답 집합에서 반환 된 타임 스탬프는 간격에서 샘플링 된 이벤트가 아니라 왼쪽 간격 경계입니다.

## <a name="next-steps"></a>다음 단계

* [시계열 모델](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)에서 정의할 수 있는 다양 한 변수에 대해 자세히 알아보세요.
* [Azure Time Series Insights TSI 탐색기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-explorer)에서 데이터를 쿼리 하는 방법에 대해 자세히 알아보세요.
