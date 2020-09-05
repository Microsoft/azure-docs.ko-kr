---
title: Azure Monitor for Key Vault(미리 보기)를 사용하여 Key Vault 모니터링 | Microsoft Docs
description: 이 문서에서는 Azure Monitor for Key Vault에 대해 설명합니다.
services: azure-monitor
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/13/2019
ms.openlocfilehash: c669946ab76fcaeaaa6fd681f521408643c5a63c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88531262"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault-preview"></a>Azure Monitor for Key Vault(미리 보기)를 사용하여 키 자격 증명 모음 서비스 모니터링
Azure Monitor for Key Vault(미리 보기)는 Key Vault 요청, 성능, 실패 및 대기 시간에 대한 통합 보기를 제공하여 키 자격 증명 모음을 종합적으로 모니터링합니다.
이 문서는 Azure Monitor for Key Vault(미리 보기) 환경을 온보딩하고 사용자 지정하는 방법을 이해하는 데 도움이 됩니다.

## <a name="introduction-to-azure-monitor-for-key-vault-preview"></a>Azure Monitor for Key Vault(미리 보기) 소개

이 환경을 살펴보기 전에, 이 환경에서 어떤 방식으로 정보가 제공되고 시각화되는지 이해해야 합니다.
-    **규모의 관점에서** 요청, 오류 분석, 작업 및 대기 시간의 개요를 기반으로 성능에 대한 스냅샷 보기를 표시합니다.
-   특정 키 자격 증명 모음의 **드릴다운 분석**을 통해 자세히 분석합니다.
-    **사용자 지정이 가능**하므로 표시할 메트릭을 변경하고, 제한에 맞도록 임계값을 수정 또는 설정하고, 사용자 고유의 통합 문서를 저장할 수 있습니다. 통합 문서의 차트를 Azure 대시보드에 고정할 수 있습니다.

Azure Monitor for Key Vault는 로그와 메트릭을 결합하여 글로벌 모니터링 솔루션을 제공합니다. 모든 사용자가 메트릭 기반 모니터링 데이터에 액세스할 수 있지만, 로그 기반 시각화를 포함하려면 사용자가 [Azure Key Vault 로깅을 사용하도록 설정](../../key-vault/general/logging.md)해야 할 수도 있습니다.

## <a name="configuring-your-key-vaults-for-monitoring"></a>모니터링할 키 자격 증명 모음 구성

> [!NOTE]
> 로그 사용은 추가 모니터링 기능을 제공하는 유료 서비스입니다.

1. [작업 및 대기 시간] 탭은 어떤 키 자격 증명 모음을 몇 개나 사용할 것인지 결정하는 데 도움이 됩니다. 수집을 시작하려면 **사용** 단추를 선택합니다. 그러면 진단 로그를 사용하도록 설정해야 하는 키 자격 증명 모음을 나열하는 별도의 통합 문서가 열립니다.

    ![파란색 [사용] 단추가 표시된 [작업 및 대기 시간] 탭의 스크린샷](./media/key-vaults-insights-overview/enable-logging.png)

2. 진단 로그를 사용하도록 설정하려면 작업 열 아래에서 **사용** 링크를 클릭하고, Log Analytics 작업 영역으로 로그를 보내는 새 진단 설정을 만듭니다. 모든 로그를 동일한 작업 영역으로 보내는 것이 좋습니다.

3. 진단 설정이 저장되면 Key Vault 인사이트 아래에서 모든 로그 기반 차트와 시각화를 볼 수 있습니다. 로그 채우기가 시작될 때까지 몇 분에서 몇 시간이 걸릴 수 있습니다.

4. Key Vault 서비스에 진단 로그를 사용하도록 설정하는 방법에 대한 추가 지원은 [전체 가이드](../../key-vault/general/logging.md)를 참조하세요.

## <a name="view-from-azure-monitor"></a>Azure Monitor에서 보기

Azure Monitor에서 구독에 속한 여러 키 자격 증명 모음의 요청, 대기 시간 및 오류 세부 정보를 볼 수 있으며 성능문제와 제한 시나리오를 식별하는 데 도움이 됩니다.

모든 구독에서 키 자격 증명 모음의 사용률 및 작업을 보려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. Azure Portal의 왼쪽 창에서 **모니터링**을 선택하고, [인사이트] 섹션에서 **키 자격 증명 모음(미리 보기)** 을 선택합니다.

![여러 그래프가 있는 개요 환경의 스크린샷](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>개요 통합 문서

선택한 구독의 개요 통합 문서에서, 테이블은 구독 내에 그룹화된 키 자격 증명 모음의 대화형 키 자격 증명 모음 메트릭을 표시합니다. 다음 드롭다운 목록에서 선택하는 옵션을 기준으로 결과를 필터링할 수 있습니다.

* 구독 - 키 자격 증명 모음이 있는 구독만 나열됩니다.

* 키 자격 증명 모음 – 기본적으로 최대 5개의 키 자격 증명 모음만 미리 선택됩니다. 범위 선택기에서 모든 또는 여러 키 자격 증명 모음을 선택하면 최대 200개의 키 자격 증명 모음이 반환됩니다. 예를 들어 선택한 3개의 구독에 총 573개의 키 자격 증명 모음이 있으면 그 중 200개만 표시됩니다.

* 시간 범위 - 기본적으로는 선택한 항목을 기준으로 지난 24시간의 정보를 표시합니다.

드롭다운 목록 아래의 카운터 타일은 선택한 구독의 총 키 자격 증명 모음 수를 롤업하고 선택된 총 수를 반영합니다. 통합 문서의 열을 색상으로 구분하고 요청, 실패 및 대기 시간 메트릭을 보고하는 조건부 열 지도가 있습니다. 가장 진한 색이 가장 높은 값을 가지며 밝은 색은 가장 작은 값을 기준으로 합니다.

## <a name="failures-workbook"></a>실패 통합 문서

페이지 맨 위에서 **실패**를 선택하면 [실패] 탭이 열립니다. 이 탭에는 특정 응답 코드의 양과 함께 API 적중 횟수, 시간에 따른 빈도가 표시됩니다.

![실패 통합 문서의 스크린샷](./media/key-vaults-insights-overview/failures.png)

통합 문서에는 파란색 값을 사용하여 API 적중 횟수 메트릭을 보고하는 열에 대한 조건부 컬러 코딩 또는 열 지도가 있습니다. 가장 진한 색이 가장 높은 값을 가지며 밝은 색은 가장 작은 값을 기준으로 합니다.

이 통합 문서에는 성공(2xx 상태 코드), 인증 오류(401/403 상태 코드), 제한(429 상태 코드) 및 기타 실패(4xx 상태 코드)가 표시됩니다.

각 상태 코드가 무엇을 의미하는지 보다 정확하게 이해하려면 [Azure Key Vault 상태 및 응답 코드](../../key-vault/general/authentication-requests-and-responses.md)에 대한 설명서를 정독하는 것이 좋습니다.

## <a name="operations--latency-workbook"></a>작업 및 대기 시간 통합 문서

페이지 맨 위에서 **작업 및 대기 시간**을 선택하면 **작업 및 대기 시간** 탭이 열립니다. 이 탭에서는 모니터링할 키 자격 증명 모음을 온보딩할 수 있습니다. 자세한 단계는 [모니터링할 키 자격 증명 모음 구성](#configuring-your-key-vaults-for-monitoring) 섹션을 참조하세요.

로깅에 사용하도록 설정된 키 자격 증명 모음의 수를 확인할 수 있습니다. 올바르게 구성된 자격 증명 모음이 하나 이상 있으면 각 키 자격 증명 모음의 작업 및 상태 코드를 표시하는 표를 볼 수 있습니다. 행의 세부 정보 섹션을 클릭하면 개별 작업에 대한 추가 정보를 가져올 수 있습니다.

![작업 및 대기 시간 차트의 스크린샷](./media/key-vaults-insights-overview/logs.png)

이 섹션의 데이터가 하나도 표시되지 않으면 Azure Key Vault에 로그를 사용하도록 설정하는 방법에 대한 최상위 섹션을 참조하거나 아래의 문제 해결 섹션을 참조하세요.

## <a name="view-from-a-key-vault-resource"></a>Key Vault 리소스의 보기

키 자격 증명 모음에서 직접 Azure Monitor for Key Vault에 액세스하는 방법은 다음과 같습니다.

1. Azure Portal에서 [키 자격 증명 모음]을 선택합니다.

2. 목록에서 백업할 키 자격 증명 모음을 선택합니다. 모니터링 섹션에서 [인사이트]\(미리 보기)를 선택합니다.

Azure Monitor 수준 통합 문서에서 키 자격 증명 모음의 리소스 이름을 선택해도 이 보기에 액세스할 수 있습니다.

![키 자격 증명 모음 리소스의 보기 스크린샷](./media/key-vaults-insights-overview/key-vault-resource-view.png)

키 자격 증명 모음의 **개요** 통합 문서에는 신속하게 평가하는 데 도움이 되는 다음과 같은 여러 성능 메트릭이 표시됩니다.

- 키 자격 증명 모음 트랜잭션, 대기 시간 및 가용성과 관련된 가장 중요한 정보를 보여주는 대화형 성능 차트

- 서비스 가용성, 키 자격 증명 모음 리소스에 대한 총 트랜잭션 수, 전체 대기 시간이 강조 표시된 메트릭 및 상태 타일

**실패** 또는 **작업**에 대한 다른 탭을 선택하면 해당 통합 문서가 열립니다.

![실패 보기의 스크린샷](./media/key-vaults-insights-overview/resource-failures.png)

실패 통합 문서는 선택한 시간 프레임의 모든 키 자격 증명 모음 요청 결과를 분석하고, 성공(2xx), 인증 오류(401/403), 제한(429) 및 기타 실패를 분류합니다.

![작업 보기의 스크린샷](./media/key-vaults-insights-overview/operations.png)

작업 통합 문서를 통해 사용자는 모든 트랜잭션의 전체 세부 정보를 심층적으로 파악할 수 있으며, 이 정보는 최상위 타일을 사용하여 결과 상태별로 필터링할 수 있습니다.

![작업 보기의 스크린샷](./media/key-vaults-insights-overview/info.png)

또한 사용자는 아래쪽 테이블을 동적으로 업데이트하는 위쪽 테이블의 보기를 트랜잭션 유형별로 자세히 살펴볼 수 있으며, 사용자는 팝업 컨텍스트 창에서 전체 작업 세부 정보를 볼 수 있습니다.

>[!NOTE]
> 사용자는 이 통합 문서를 보려면 진단 설정을 사용하도록 설정해야 합니다. 진단 설정을 사용하도록 설정하는 방법에 대한 자세한 내용은 [Azure Key Vault 로깅](../../key-vault/general/logging.md)을 참조하세요.

## <a name="pin-and-export"></a>고정 및 내보내기

섹션의 오른쪽 위에 있는 압정 아이콘을 선택하여 메트릭 섹션 중 하나를 Azure 대시보드에 고정할 수 있습니다.

여러 구독 및 키 자격 증명 모음 개요 또는 실패 통합 문서는 압정 아이콘 왼쪽에 있는 다운로드 아이콘을 선택하여 결과를 Excel 형식으로 내보낼 수 있도록 지원합니다.

![고정 아이콘이 선택된 스크린샷](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>Azure Monitor for Key Vault 사용자 지정

이 섹션에서는 데이터 분석 요구 사항을 지원하도록 사용자 지정하기 위해 통합 문서를 편집하는 일반적인 시나리오를 중점적으로 설명합니다.
*  항상 특정 구독 또는 키 자격 증명 모음을 선택하도록 통합 문서 범위를 지정합니다.
* 그리드에서 메트릭 변경
* 요청 임계값 변경
* 색 렌더링 변경

위쪽 도구 모음에서 **사용자 지정** 단추를 선택하여 편집 모드를 사용하도록 설정하면 사용자 지정을 시작할 수 있습니다.

![사용자 지정 단추의 스크린샷](./media/key-vaults-insights-overview/customize.png)

사용자 지정 항목은 게시된 통합 문서에서 기본 구성을 덮어쓰지 않도록 사용자 지정 통합 문서에 저장됩니다. 통합 문서는 리소스 그룹 내에서 프라이빗 위치인 [내 보고서] 섹션 또는 리소스 그룹에 대한 액세스 권한을 갖고 있는 모든 사용자가 액세스할 수 있는 [공유 보고서] 섹션에 저장됩니다. 사용자 지정 통합 문서를 저장한 후에는 통합 문서 갤러리로 이동하여 통합 문서를 시작해야 합니다.

![통합 문서 갤러리의 스크린샷](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>구독 또는 키 자격 증명 모음 지정

다음 단계를 수행하여 실행할 때마다 범위를 특정 구독 또는 키 자격 증명 모음으로 지정하도록 여러 구독 및 키 자격 증명 모음 개요 또는 용량 통합 문서를 구성할 수 있습니다.

1. 포털에서 **모니터링**을 선택한 다음, 왼쪽 창에서 **키 자격 증명 모음(미리 보기)** 을 선택합니다.
2. **개요** 통합 문서의 명령 모음에서 **편집**을 선택합니다.
3. **구독** 드롭다운 목록에서 기본값으로 사용할 구독을 하나 이상 선택합니다. 통합 문서에서 최대 10개의 구독을 선택할 수 있습니다.
4. **키 자격 증명 모음** 드롭다운 목록에서 기본값으로 사용할 계정을 하나 이상 선택합니다. 통합 문서에서 최대 200개의 스토리지 계정을 선택할 수 있습니다.
5. 명령 모음에서 **다른 이름으로 저장**을 선택하여 사용자 지정 항목이 있는 통합 문서의 복사본을 저장한 다음, **편집 완료**를 클릭하여 읽기 모드로 돌아갑니다.

## <a name="troubleshooting"></a>문제 해결

일반적인 문제 해결 지침은 전용 통합 문서 기반 insights [문제 해결 문서](troubleshoot-workbooks.md)를 참조 하세요.

이 섹션에서는 Azure Monitor for Key Vault(미리 보기)를 사용할 때 발생할 수 있는 몇 가지 일반적인 문제를 진단하고 해결하는 데 도움이 되는 정보를 제공합니다. 아래 목록에서 특정 문제와 관련된 정보를 찾을 수 있습니다.

### <a name="resolving-performance-issues-or-failures"></a>성능 문제 또는 오류 해결

Azure Monitor for Key Vault(미리 보기)를 사용하여 발견한 키 자격 증명 모음 관련 문제를 해결하려면 [Azure Key Vault 설명서](../../key-vault/index.yml)를 참조하세요.

### <a name="why-can-i-only-see-200-key-vaults"></a>200 주요 자격 증명 모음만 볼 수 있는 이유는 무엇 인가요?

선택하여 볼 수 있는 키 자격 증명 모음의 수가 200개로 제한되어 있습니다. 선택한 구독 수에 관계없이, 선택 가능한 키 자격 증명 모음의 수는 200개로 제한됩니다.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>구독 선택에서 내 구독이 모두 표시 되지 않는 이유는 무엇 인가요?

선택한 구독 필터에서 선택한 구독이 포함된 구독만 표시됩니다. 구독 필터는 Azure Portal 헤더의 "디렉터리 + 구독"에서 선택합니다.

![구독 필터의 스크린샷](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-am-getting-an-error-message-that-the-query-exceeds-the-maximum-number-of-workspacesregions-allowed-what-to-do-now"></a>"쿼리가 허용 되는 최대 작업 영역/영역을 초과 했습니다." 라는 오류 메시지가 표시 됩니다.

현재 25개 지역과 200개 작업 영역으로 제한되어 있으므로, 데이터를 보려면 구독 및/또는 리소스 그룹의 수를 줄여야 합니다.

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Key Vault 정보를 변경 하거나 시각화를 추가 하려면 어떻게 해야 하나요?

변경하려면 "편집 모드"를 선택하여 통합 문서를 수정합니다. 그런 다음, 지정된 구독 및 리소스 그룹에 연결된 새 통합 문서로 작업을 저장할 수 있습니다.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>통합 문서의 모든 부분을 고정 한 후의 시간-그레인

"자동" 시간 범위를 활용하므로 선택한 시간 범위에 따라 달라집니다.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>통합 문서 일부가 고정 된 시간 범위

시간 범위는 대시보드 설정에 따라 달라집니다.

### <a name="why-do-i-not-see-any-data-for-my-key-vault-under-the-operations--latency-sections"></a>작업 & 대기 시간 섹션에서 내 Key Vault에 대 한 데이터가 표시 되지 않는 이유는 무엇 인가요?

로그 기반 데이터를 보려면 모니터링하려는 키 자격 증명 모음마다 로그를 사용하도록 설정해야 합니다. 이 작업은 각 키 자격 증명 모음의 진단 설정에서 수행할 수 있습니다. 지정된 Log Analytics 작업 영역으로 데이터를 보내야 합니다.

### <a name="i-have-already-enabled-logs-for-my-key-vault-why-am-i-still-unable-to-see-my-data-under-operations--latency"></a>내 Key Vault에 대 한 로그를 이미 사용 하도록 설정 했습니다 .이는 여전히 작업 & 대기 시간에 내 데이터를 볼 수 없는 이유입니다.

현재 진단 로그는 소급하여 작동하지 않으므로 키 자격 증명 모음에 대한 작업이 수행된 후에만 데이터가 표시되기 시작합니다. 따라서 키 자격 증명 모음을 활성화하는 방법에 따라 몇 시간에서 하루까지 걸릴 수 있습니다.

또한 선택한 키 자격 증명 모음과 구독의 수가 많은 경우 쿼리 제한 때문에 데이터가 표시되지 않을 수 있습니다. 데이터를 보려면 선택한 구독 또는 자격 증명 모음의 수를 줄여야 할 수도 있습니다. 

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>다른 데이터를 보거나 나만의 시각화를 만들려면 어떻게 해야 하나요? Key Vault Insights를 변경 하려면 어떻게 해야 하나요?

편집 모드를 사용하여 기존 통합 문서를 편집한 다음, 새 변경 내용을 모두 포함하는 새 통합 문서로 작업을 저장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Monitor 통합 문서를 사용하여 대화형 보고서 만들기](../platform/workbooks-overview.md)를 검토하여 통합 문서에서 지원하도록 디자인된 시나리오, 새 보고서를 작성하고 기존 보고서를 사용자 지정하는 방법 등을 알아보세요.
