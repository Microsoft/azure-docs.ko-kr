---
title: 가격 책정 & 청구 모델
description: 가격 책정 및 청구 모델의 작동 방식에 대 한 개요 Azure Logic Apps
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/19/2019
ms.openlocfilehash: 1c21a84bd9aaa259d0459b4e16c7a62aabaa615d
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896393"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Azure Logic Apps용 가격 책정 모델

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 를 사용 하면 클라우드에서 규모를 확장할 수 있는 자동화 된 통합 워크플로를 만들고 실행할 수 있습니다. 이 문서에서는 Azure Logic Apps 요금 청구 및 가격 책정에 대해 설명 합니다. 가격 책정 요금은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 참조 하세요.

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>소비 가격 책정 모델

공용 또는 "글로벌" Azure Logic Apps 서비스에서 실행 되는 새 논리 앱의 경우 사용한 양만큼만 요금을 지불 하면 됩니다. 이러한 논리 앱은 사용량 기반 계획 및 가격 책정 모델을 사용합니다. 논리 앱에서 각 단계는 작업 이며, 논리 앱에서 실행 되는 모든 작업의 Azure Logic Apps 미터입니다.

예를 들어 작업에는 다음이 포함 됩니다.

* 트리거는 특별 한 동작입니다. 모든 논리 앱에는 첫 번째 단계로 트리거가 필요 합니다.
* ["기본 제공" 또는](../connectors/apis-list.md#built-in) HTTP와 같은 네이티브 작업, Azure Functions 및 API Management에 대 한 호출 등
* Outlook 365, Dropbox 등과 같은 [관리 되는 커넥터](../connectors/apis-list.md#managed-connectors) 에 대 한 호출
* 루프, 조건문 등의 제어 흐름 단계

[표준 커넥터](../connectors/apis-list.md#managed-connectors) 는 [표준 커넥터 가격](https://azure.microsoft.com/pricing/details/logic-apps)으로 요금이 청구 됩니다. 일반적으로 사용할 수 있는 [엔터프라이즈 커넥터](../connectors/apis-list.md#managed-connectors) 는 [엔터프라이즈 커넥터 가격](https://azure.microsoft.com/pricing/details/logic-apps)으로 청구 되 고, 공개 미리 보기 엔터프라이즈 커넥터는 [표준 커넥터 가격](https://azure.microsoft.com/pricing/details/logic-apps)으로 청구 됩니다.

[트리거](#triggers) 및 [작업](#actions)에 대 한 청구의 작동 방식에 대해 자세히 알아보세요.

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>고정 가격 책정 모델

[ISE ( *통합 서비스 환경* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 는 Azure 가상 네트워크의 리소스에 액세스할 수 있는 논리 앱을 만들고 실행 하는 전용 하 고 격리 된 전용 방법을 제공 합니다. ISE 내에서 실행 되는 새 논리 앱의 경우 다음과 같은 기능에 대 한 [고정 된 월별 가격](https://azure.microsoft.com/pricing/details/logic-apps) 을 지불 합니다.

* [기본 제공 트리거 및 작업](../connectors/apis-list.md#built-in)

* [표준 커넥터](../connectors/apis-list.md#managed-connectors)

* 원하는 만큼의 연결을 사용 하는 [엔터프라이즈 커넥터](../connectors/apis-list.md#enterprise-connectors)

* [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)에 따라 추가 비용 없이 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 사용:

  * **프리미엄 SKU**: 단일 [표준 계층](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 통합 계정

  * **개발자 SKU**: 단일 [무료 계층](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 통합 계정

  각 ISE SKU는 총 통합 계정 5 개로 제한 됩니다. 추가 비용을 위해 ISE SKU에 따라 더 많은 통합 계정을 사용할 수 있습니다.

  * **PREMIUM SKU**: 최대 4 개의 표준 계정이 있습니다. 무료 또는 기본 계정이 없습니다.

  * **개발자 SKU**: 최대 4 개의 표준 계정 또는 최대 5 개의 총 표준 계정입니다. 기본 계정이 없습니다.

통합 계정 제한에 대 한 자세한 내용은 [Logic Apps 제한 및 구성](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)을 참조 하세요. [통합 계정 계층 및 해당 가격 책정 모델](#integration-accounts) 에 대 한 자세한 내용은이 항목의 뒷부분에서 확인할 수 있습니다.

프리미엄 ISE SKU의 경우 기본 단위는 고정 용량을 가지 므로, 더 많은 처리량이 필요한 경우에는 만들거나 나중에 [확장 단위를 추가할](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#add-capacity)수 있습니다. 개발자 ISE SKU에는 배율 단위를 추가 하는 기능이 없습니다. ISE에서 실행 되는 논리 앱은 데이터 보존 비용을 초래 하지 않습니다.

> [!NOTE]
> ISE 내에서 기본 제공 트리거 및 작업은 **코어** 레이블을 표시 하 고 논리 앱과 동일한 ISE에서 실행 됩니다. **Ise** 레이블을 표시 하는 표준 및 엔터프라이즈 커넥터는 논리 앱과 동일한 ise에서 실행 됩니다. ISE 레이블을 표시 하지 않는 커넥터는 글로벌 Logic Apps 서비스에서 실행 됩니다.

가격 책정 요금은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 참조 하세요.

<a name="connectors"></a>

## <a name="connectors"></a>커넥터

Azure Logic Apps 커넥터를 사용 하면 [트리거](#triggers), [작업](#actions)또는 둘 다를 제공 하 여 논리 앱에서 클라우드 또는 온-프레미스의 앱, 서비스 및 시스템에 액세스할 수 있습니다. 커넥터는 Standard 또는 Enterprise 중 하나로 분류 됩니다. 이러한 커넥터에 대 한 개요는 [Azure Logic Apps 커넥터](../connectors/apis-list.md)를 참조 하십시오. 논리 앱에서 사용 하려는 REST Api에 대해 미리 빌드된 커넥터를 사용할 수 없는 경우 이러한 REST Api를 중심으로 하는 래퍼로 [사용자 지정 커넥터](https://docs.microsoft.com/connectors/custom-connectors)를 만들 수 있습니다. 사용자 지정 커넥터는 표준 커넥터로 청구 됩니다. 다음 섹션에서는 트리거 및 작업의 요금 청구 방법에 대 한 자세한 정보를 제공 합니다.

<a name="triggers"></a>

## <a name="triggers"></a>트리거

트리거는 특정 이벤트가 발생할 때 논리 앱 인스턴스를 만드는 특수 작업입니다. 트리거는 논리 앱이 계량되는 방식에 영향을 주는 다양한 방법으로 작동합니다. Azure Logic Apps에 존재 하는 다양 한 종류의 트리거는 다음과 같습니다.

* **폴링 트리거**:이 트리거는 논리 앱 인스턴스를 만들고 워크플로를 시작 하기 위한 조건을 충족 하는 메시지에 대해 엔드포인트을 지속적으로 확인 합니다. 논리 앱 인스턴스를 만들지 않은 경우에도 Logic Apps는 각 폴링 요청을 실행으로 측정합니다. 폴링 간격을 설정하려면 논리 앱 디자이너를 통해 트리거를 설정합니다.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Webhook 트리거**:이 트리거는 클라이언트가 특정 엔드포인트에 요청을 보낼 때까지 대기 합니다. 웹후크 엔드포인트로 전송된 각 요청은 작업 실행으로 계산됩니다. 예를 들어, 요청 및 HTTP 웹후크 트리거는 둘 다 웹후크 트리거입니다.

* **되풀이 트리거**:이 트리거는 트리거에서 설정한 되풀이 간격을 기반으로 논리 앱 인스턴스를 만듭니다. 예를 들어 3 일 마다 또는 보다 복잡 한 일정으로 실행 되는 되풀이 트리거를 설정할 수 있습니다.

<a name="actions"></a>

## <a name="actions"></a>작업

기본 작업으로 "기본 제공" 작업 (예: HTTP)을 Azure Logic Apps 합니다. 예를 들어 기본 제공 작업에는 HTTP 호출, Azure Functions 또는 API Management의 호출 및 조건, 루프, switch 문 등의 제어 흐름 단계가 포함 됩니다. 각 작업에는 고유한 작업 유형이 있습니다. 예를 들어 [커넥터](https://docs.microsoft.com/connectors) 를 호출 하는 작업에는 "ApiConnection" 형식이 있습니다. 이러한 커넥터는 표준 또는 엔터프라이즈 커넥터로 분류 되며 해당 [가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)에 따라 측정 됩니다. *미리 보기* 의 엔터프라이즈 커넥터는 표준 커넥터로 요금이 청구 됩니다.

Azure Logic Apps 성공 및 실패 한 모든 작업을 실행으로 측정 합니다. 그러나 Logic Apps는 이러한 작업을 측정 하지 않습니다.

* 충족되지 않은 조건으로 인해 건너뛰는 작업
* 완료하기 전에 논리 앱이 중지되어 실행되지 않는 작업

루프 내에서 실행 되는 작업의 경우 루프의 각 주기에 대해 각 작업을 계산 Azure Logic Apps 합니다. 예를 들어 목록을 처리하는 "for each" 루프가 있다고 가정합니다. Logic Apps는 목록 항목의 수를 루프의 작업 수와 곱하여 해당 루프에서 작업을 측정하고, 루프를 시작하는 작업을 추가합니다. 따라서 10 개 항목 목록에 대 한 계산은 (10 * 1) + 1 이며이로 인해 11 개의 작업 실행이 발생 합니다.

## <a name="disabled-logic-apps"></a>사용 하지 않는 논리 앱

사용 하지 않도록 설정 된 논리 앱은 사용 하지 않도록 설정 된 상태에서 새 인스턴스를 만들 수 없으므로 요금이 청구 되지 않습니다 논리 앱을 사용 중지하면 현재 실행 중인 인스턴스가 완전히 중지되기까지 조금 시간이 걸릴 수 있습니다.

<a name="integration-accounts"></a>

## <a name="integration-accounts"></a>통합 계정

[고정 가격 책정 모델](https://azure.microsoft.com/pricing/details/logic-apps) 은 추가 비용 없이 Azure Logic Apps에서 [B2B 및 EDI](logic-apps-enterprise-integration-b2b.md) 및 [XML 처리](logic-apps-enterprise-integration-xml.md) 기능을 탐색, 개발 및 테스트할 수 있는 [통합 계정](logic-apps-enterprise-integration-create-integration-account.md) 에 적용 됩니다. 각 Azure 구독은 [통합 계정의 특정 한도](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)를 포함할 수 있습니다. 각 통합 계정에는 거래 업체, 규약, 맵, 스키마, 어셈블리, 인증서, 일괄 처리 구성 등을 포함 하 여 특정 한 수 [의 아티팩트](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits)를 저장할 수 있습니다.

Azure Logic Apps는 무료, 기본 및 표준 통합 계정을 제공 합니다. 기본 및 표준 계층은 Logic Apps SLA (서비스 수준 계약)에서 지원 되지만, 무료 계층은 SLA에서 지원 되지 않으며 처리량 및 사용에 제한이 있습니다. 무료 계층 통합 계정을 제외 하 고 각 Azure 지역에 둘 이상의 통합 계정을 사용할 수 있습니다. 가격 책정 요금은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 참조 하세요.

[프리미엄 또는 개발자](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)인 [ise ( *통합 서비스 환경* ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md))가 있는 경우 ise에는 총 5 개의 통합 계정이 있을 수 있습니다. ISE에 대 한 고정 가격 책정 모델이 작동 하는 방법을 알아보려면이 항목의 이전 [고정 가격 책정 모델](#fixed-pricing) 섹션을 참조 하세요. 가격 책정 요금은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 참조 하세요.

무료, 기본 또는 표준 통합 계정 중에서 선택 하려면 다음과 같은 사용 사례 설명을 검토 합니다.

* **무료**: 프로덕션 시나리오가 아닌 예비 시나리오를 시도 하려는 경우

* **기본**: 더 큰 비즈니스 엔터티와 거래 파트너 관계가 있는 소규모 비즈니스 파트너 역할을 하는 메시지 처리만 하려는 경우

* **표준**: 더 복잡 한 B2B 관계와 관리 해야 하는 엔터티 수가 증가 하는 경우

<a name="data-retention"></a>

## <a name="data-retention"></a>데이터 보존

ISE (integration service environment)에서 실행 되는 논리 앱을 제외 하 고 논리 앱의 실행 기록에 저장 된 모든 입력 및 출력은 논리 앱의 [실행 보존 기간](logic-apps-limits-and-config.md#run-duration-retention-limits)을 기준으로 청구 됩니다. ISE에서 실행 되는 논리 앱은 데이터 보존 비용을 초래 하지 않습니다. 가격 책정 요금은 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps)을 참조 하세요.

논리 앱의 저장소 사용량을 모니터링 하는 데 도움이 되도록 다음을 수행할 수 있습니다.

* 논리 앱에서 월별 사용 하는 저장소 단위 수 (GB)를 표시 합니다.
* 논리 앱의 실행 기록에서 특정 작업의 입력 및 출력에 대 한 크기를 확인 합니다.

<a name="storage-consumption"></a>

### <a name="view-logic-app-storage-consumption"></a>논리 앱 저장소 사용량 보기

1. Azure Portal에서 논리 앱을 찾아서 엽니다.

1. 논리 앱 메뉴의 **모니터링**아래에서 **메트릭**을 선택 합니다.

1. 오른쪽 창의 **차트 제목**아래에 있는 **메트릭** 목록에서 **저장소 사용량 실행에 대 한 청구 사용량**을 선택 합니다.

   이 메트릭은 청구 되는 월간 저장소 소비 단위 수 (GB 단위)를 제공 합니다.

<a name="input-output-sizes"></a>

### <a name="view-action-input-and-output-sizes"></a>작업 입력 및 출력 크기 보기

1. Azure Portal에서 논리 앱을 찾아서 엽니다.

1. 논리 앱의 메뉴에서 **개요**를 선택 합니다.

1. 오른쪽 창의 **실행 기록**에서 확인 하려는 입력 및 출력이 포함 된 실행을 선택 합니다.

1. **논리 앱 실행**에서 **실행 세부 정보**를 선택 합니다.

1. **논리 앱 실행 세부 정보** 창의 작업 테이블에서 각 작업의 상태 및 기간을 나열 하는 작업 테이블에서 보려는 작업을 선택 합니다.

1. **논리 앱 작업** 창에서 해당 작업의 입력 및 출력에 대 한 크기를 **입력 링크** 및 **출력 링크**아래에 각각 표시 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에 대 한 자세한 정보](logic-apps-overview.md)
* [첫 번째 논리 앱 만들기](quickstart-create-first-logic-app-workflow.md)
