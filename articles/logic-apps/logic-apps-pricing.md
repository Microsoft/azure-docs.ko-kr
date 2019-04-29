---
title: 가격 책정 및 대금 청구 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps의 가격 책정 및 대금 청구 방식 알아보기
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 03/25/2019
ms.openlocfilehash: 7e1868dd5ce62c28c9a8aac724862c58a5e0e1da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598483"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure Logic Apps용 가격 책정 모델

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 만들고는 자동화 된 통합 워크플로 실행 하는 데 도움이 됩니다. 클라우드에서 확장할 수 있습니다. 이 문서에서는 Azure Logic Apps에 대 한 대금 청구 및 가격 책정 작동 하는 방법을 설명 합니다. 특정 가격 정보 참조 [Azure Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)합니다.

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>소비 가격 책정 모델

공용 또는 "전역" Azure Logic Apps 서비스에서 실행 되는 새 논리 앱에 대해 사용한 만큼에 대해서만 지불 합니다. 이러한 논리 앱은 사용량 기반 계획 및 가격 책정 모델을 사용합니다. 논리 앱 정의에서 각 단계는 작업입니다. 예를 들어 동작은 같습니다. 

* 트리거는 특별 한 동작입니다. 모든 논리 앱에는 첫 번째 단계로 트리거가 필요합니다.
* "기본 제공" 또는 네이티브 작업 HTTP와 같은 Azure Functions 및 API Management를 호출
* Outlook 365, Dropbox 등과 같은 커넥터에 대 한 호출
* 조건문, 루프 등의 흐름 단계를 제어 하 고 등

Azure Logic Apps는 논리 앱에서 실행 되는 모든 작업을 측정 합니다. 청구에 대 한 작동 하는 방법에 대해 자세히 알아보려면 [트리거](#triggers) 하 고 [작업](#actions)합니다.

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>고정 가격 책정 모델

[ *통합 서비스 환경* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) Azure 가상 네트워크의 리소스에 액세스할 수 있는 논리 앱 만들기 및 실행 하는 개인, 격리 및 전용 방법을 제공 합니다. ISE를 내에서 실행 되는 새 논리 앱에 대해 기본 제공 작업 및 표준 커넥터에 대 한 고정된 월별 가격을 지불 합니다. ISE에 원하는 만큼 많은 연결을 포함 하는 하나의 무료 엔터프라이즈 커넥터를도 포함 되어 있습니다. 추가 엔터프라이즈 커넥터에 대 한 사용량 기반 엔터프라이즈 소비 가격으로 요금이 청구 됩니다. 

ISE 기본 단위 용량을 가지 며 고정에 더 많은 처리량을 할 경우 수 있도록 [배율 단위를 더 추가](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity), 생성 중 전이나 합니다.

> [!NOTE]
> ISE에는 [ *공개 미리 보기*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)합니다. 특정 가격 정보 참조 [Azure Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)합니다.

<a name="connectors"></a>

## <a name="connectors"></a>커넥터

Azure Logic Apps 커넥터를 제공 하 여 논리 앱 액세스 앱, 서비스 및 클라우드 또는 온-프레미스 시스템을 도움말 [트리거](#triggers)를 [작업](#actions), 또는 둘 다. 커넥터는 Standard 또는 Enterprise로 분류 됩니다. 이러한 커넥터에 대 한 개요를 보려면 [Azure Logic Apps 용 커넥터](../connectors/apis-list.md)합니다. 다음 섹션에 대 한 청구 트리거 하는 방법에 대 한 자세한 정보를 제공 및 작업이 작동 합니다.

<a name="triggers"></a>

## <a name="triggers"></a>트리거

트리거는 특정 이벤트가 발생할 때 논리 앱 인스턴스를 만드는 특수 작업입니다. 트리거는 논리 앱이 계량되는 방식에 영향을 주는 다양한 방법으로 작동합니다. Azure Logic Apps에 있는 트리거의 다양 한 종류는 다음과 같습니다.

* **폴링 트리거**: 이 트리거는 논리 앱 인스턴스를 만들고 워크플로 시작 하기 위한 조건을 충족 하는 메시지에 대 한 끝점을 지속적으로 확인 합니다. 논리 앱 인스턴스를 만들지 않은 경우에도 Logic Apps는 각 폴링 요청을 실행으로 측정합니다. 폴링 간격을 설정하려면 논리 앱 디자이너를 통해 트리거를 설정합니다.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **웹 후크 트리거**: 이 트리거는 클라이언트가 특정 끝점에 요청을 전송할을 대기 합니다. 웹후크 엔드포인트로 전송된 각 요청은 작업 실행으로 계산됩니다. 예를 들어, 요청 및 HTTP 웹후크 트리거는 둘 다 웹후크 트리거입니다.

* **되풀이 트리거**: 이 트리거는 트리거에 설정 하는 되풀이 간격에 따라 논리 앱 인스턴스를 만듭니다. 예를 들어 3 일 마다 실행 하는 되풀이 트리거 또는 더 복잡 한 일정에 따라 설정할 수 있습니다.

<a name="actions"></a>

## <a name="actions"></a>작업

Azure Logic Apps는 원시 작업으로 HTTP와 같은 "기본 제공" 작업을 측정합니다. 예를 들어, 기본 제공 HTTP 호출, Azure Functions 또는 API Management에서 호출을 포함 하 고 제어 흐름 단계 조건, 루프 등 작업과 switch 문. 각 작업에 자신의 작업 형식이 있습니다. 호출 하는 작업 예를 들어 [커넥터](https://docs.microsoft.com/connectors) "ApiConnection" 유형이 있어야 합니다. 이러한 커넥터는 표준으로 분류 됩니다 또는 엔터프라이즈 커넥터에는 요금이 청구는 해당 [가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)합니다. 엔터프라이즈 커넥터 *미리 보기* 표준 커넥터로 요금이 청구 됩니다.

Azure Logic Apps는 모든 성공 및 실패 한 작업 실행으로 측정합니다. 그러나 Logic Apps는 이러한 작업을 측정 하지 않습니다.

* 충족되지 않은 조건으로 인해 건너뛰는 작업
* 완료하기 전에 논리 앱이 중지되어 실행되지 않는 작업

루프 내에서 실행 되는 작업의 경우 Azure Logic Apps 루프의 각 주기 마다 각 동작을 계산 합니다. 예를 들어 목록을 처리하는 "for each" 루프가 있다고 가정합니다. Logic Apps는 목록 항목의 수를 루프의 작업 수와 곱하여 해당 루프에서 작업을 측정하고, 루프를 시작하는 작업을 추가합니다. 따라서 10 항목 목록에 대 한 계산은 (10 * 1) + 1 11 작업 실행의 결과입니다.

## <a name="disabled-logic-apps"></a>비활성화 된 logic apps

논리 앱에는 사용 하지 않도록 설정 하는 동안 새 인스턴스를 만들 수 있으므로 요금이 청구 되지 않습니다를 사용 하지 않도록 설정 합니다.
논리 앱을 사용 중지하면 현재 실행 중인 인스턴스가 완전히 중지되기까지 조금 시간이 걸릴 수 있습니다.

## <a name="integration-accounts"></a>통합 계정

소비 가격에 적용 됩니다 [통합 계정](logic-apps-enterprise-integration-create-integration-account.md) 고 수 있는 탐색, 개발, 테스트 합니다 [B2B 및 EDI](logic-apps-enterprise-integration-b2b.md) 하 고 [XML 처리](logic-apps-enterprise-integration-xml.md) 없이 Azure Logic Apps의 기능 추가 비용입니다.
각 Azure 지역에서 통합 계정이 있습니다. 각 통합 계정은 거래 업체, 규약, 맵, 스키마, 어셈블리, 인증서, 일괄 처리 구성 등을 포함하는 특정 [아티팩트 수](../logic-apps/logic-apps-limits-and-config.md)까지 저장할 수 있습니다.

Azure Logic Apps에는 또한 지원 되는 Logic Apps SLA를 사용 하 여 기본 및 표준 통합 계정을 제공합니다. 기본 또는 표준 통합 계정을 사용할지 여부를 선택할 수 있습니다 하는 방법으로 다음과 같습니다.

* 하려는 경우에 메시지 처리는 더 큰 비즈니스 엔터티를 사용 하 여 거래 파트너 관계가 있는 소규모 비즈니스 파트너 역할 기본 통합 계정을 사용 합니다.

* 더 복잡 한 B2B 관계를 관리할 수 있습니다 하는 엔터티 수를 늘려야 할 경우 표준 통합 계정을 사용 합니다.

특정 가격 정보 참조 [Azure Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)합니다.

<a name="data-retention"></a>

## <a name="data-retention"></a>데이터 보존

논리 앱의 기반으로 모든 입력 및 출력 논리 앱의 실행된 기록에 저장 되는 요금이 청구 [보존 기간 실행](logic-apps-limits-and-config.md#run-duration-retention-limits)합니다. 특정 가격 정보 참조 [Azure Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)합니다.

를 논리 앱의 저장소 사용량을 모니터링할 수 있도록 하려면 다음을 수행할 수 있습니다.

* 월별 논리 앱에서 사용 되는 GB 단위로 저장소 단위의 수를 봅니다.
* 논리 앱의 실행된 기록에서 특정 작업의 입력 및 출력 크기를 검토 합니다.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>논리 앱 저장소 사용 보기

1. Azure Portal에서 논리 앱을 찾아서 엽니다.

1. 논리 앱 메뉴에서 아래 **모니터링**를 선택 **메트릭**합니다.

1. 오른쪽 창에서 아래 **차트 제목을**에서 **메트릭** 목록에서 **저장소 소비 실행에 대 한 청구 사용량**합니다.

   이 메트릭은 저장소 소비 단위 gb에서 월별 요금이 청구 되는 수를 제공 합니다.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>보고 작업 입력 및 출력 크기

1. Azure Portal에서 논리 앱을 찾아서 엽니다.

1. 논리 앱 메뉴에서 선택 **개요**합니다.

1. 오른쪽 창에서 아래 **실행 기록**, 입력 및 확인 하려면 출력 실행을 선택 합니다.

1. 아래 **논리 앱 실행**, 선택 **실행 세부 정보**합니다.

1. 에 **논리 앱 실행 정보** 창 작업 표에 각 작업의 상태 및 기간 목록에서 보려는 작업을 선택 합니다.

1. 에 **논리 앱 작업** 창에서 해당 작업의 입력 및 출력의 크기를 각각의 표시 찾기 **입력 링크** 및 **출력 링크**합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에 자세히 알아보기](logic-apps-overview.md)
* [첫 번째 논리 앱 만들기](quickstart-create-first-logic-app-workflow.md)