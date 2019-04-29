---
title: Azure Resource Health 개요 | Microsoft Docs
description: Azure Resource Health 개요
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.workload: Supportability
ms.date: 11/16/2018
ms.openlocfilehash: d2a77e831290aa1ee0fcb6d4addf8f6e90786d52
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119854"
---
# <a name="azure-resource-health-overview"></a>Azure Resource Health 개요
 
Azure Resource Health는 Azure 서비스 문제가 리소스에 영향을 미치는 경우 이를 진단하고 지원을 받도록 도와줍니다. 리소스의 현재 및 과거 상태를 알려줍니다. 또한 문제를 완화하는 데 도움이 되는 기술 지원을 제공합니다.

[Azure 상태](https://status.azure.com)는 광범위한 Azure 고객 집합에 영향을 미치는 서비스 문제를 알려주지만, Resource Health는 리소스 상태에 대해 개인 설정된 대시 보드를 제공합니다. Resource Health는 Azure 서비스 문제로 인해 과거에 리소스를 사용할 수 없었던 모든 시간을 보여줍니다. 따라서 SLA가 위반되었는지 쉽게 파악할 수 있습니다. 

## <a name="resource-definition-and-health-assessment"></a>리소스 정의 및 상태 평가
리소스는 Azure 서비스의 특정 인스턴스(예: 가상 머신, 웹앱 또는 SQL 데이터베이스)입니다.

Resource Health는 다양한 Azure 서비스에서 내보낸 신호에 의존하여 리소스의 정상 여부를 평가합니다. 리소스가 정상이 아니면 Resource Health에서 추가 정보를 분석하여 문제 원인을 파악합니다. 또한 Microsoft가 문제를 해결하기 위해 수행하는 작업 또는 문제의 원인을 해결하기 위해 수행할 수 있는 작업도 파악합니다. 

상태가 평가되는 방식에 대한 자세한 내용은 [Azure Resource Health](resource-health-checks-resource-types.md)에서 리소스 유형 및 상태 검사에 대한 전체 목록을 확인하세요.

## <a name="health-status"></a>상태
리소스 상태는 다음 상태 중 하나로 표시됩니다.

### <a name="available"></a>사용 가능
**사용 가능**은 리소스의 상태에 영향을 미치는 이벤트가 서비스에서 검색되지 않은 상태를 말합니다. 리소스가 최근 24시간 동안 계획되지 않은 가동 중지 시간에서 복구된 경우 **최근에 해결됨** 알림이 표시됩니다.

!["최근에 해결됨" 알림이 있는 가상 머신의 “사용 가능” 상태](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>사용할 수 없음
**사용할 수 없음**은 리소스 상태에 영향을 미치는 플랫폼 또는 비-플랫폼 이벤트가 진행되고 있는 것이 서비스에서 검색되었음을 나타내는 상태입니다.

#### <a name="platform-events"></a>플랫폼 이벤트
플랫폼 이벤트는 Azure 인프라의 여러 구성 요소에 의해 트리거됩니다. 여기에는 예정된 작업(예: 계획된 유지 관리)과 예기치 않은 인시던트(예: 계획에 없는 호스트 재부팅)가 모두 포함됩니다.

Resource Health에는 이벤트 및 복구 프로세스에 대한 추가 세부 정보가 제공됩니다. 또한 Microsoft 지원 계약이 유효하지 않은 경우에도 지원 담당자에게 문의할 수 있습니다.

![플랫폼 이벤트로 인한 가상 머신의 "사용할 수 없음" 상태](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>비-플랫폼 이벤트
비-플랫폼 이벤트는 사용자의 작업에 의해 트리거됩니다. 예를 들어, 가상 머신을 중지하거나 Redis의 Azure Cache에 대한 최대 연결 수에 도달하는 경우를 말합니다.

![비-플랫폼 이벤트로 인한 가상 머신의 “사용할 수 없음” 상태](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>알 수 없음
**알 수 없음** 상태는 Resource Health에서 이 리소스에 대한 정보를 10분 넘게 수신하지 못했음을 나타냅니다. 이 상태는 리소스 상태에 대한 결정적인 표시는 아니지만 문제 해결 프로세스의 중요한 데이터 요소입니다.

리소스가 예상한 대로 실행 중이면 몇 분 후 리소스 상태가 **사용 가능**으로 변경됩니다.

리소스에 문제가 발생하는 경우 상태가 **알 수 없음**이면, 플랫폼의 이벤트가 리소스에 영향을 미치고 있다는 것을 의미할 수 있습니다.

![가상 머신의 "알 수 없음" 상태](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>성능 저하됨
**저하됨** 상태는 리소스가 사용 가능한 상태지만 성능 저하가 감지되었음을 나타냅니다.
다양한 리소스에는 리소스가 저하되었음을 나타내는 시기에 대해 자체적인 기준이 있습니다.

![가상 머신의 "저하됨" 상태](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>잘못된 상태 보고
현재 상태가 잘못되었다는 생각이 들면 **Report incorrect health status**(잘못된 상태 보고)를 선택하여 알려 주시기 바랍니다. Azure 문제의 영향을 받는 경우 Resource Health에서 지원을 요청하는 것이 좋습니다. 

![잘못된 상태에 대한 정보를 제출하는 상자](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>기록 정보
Resource Health의 **상태 기록** 섹션에서 최대 14일 동안의 상태 기록에 액세스할 수 있습니다. 

![지난 2주 동안의 Resource Health 이벤트 목록](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>시작
하나의 리소스에 대한 Resource Health를 열려면:
1.  Azure 포털에 로그인합니다.
2.  리소스를 찾습니다.
3.  왼쪽 창의 리소스 메뉴에서 **Resource Health**를 선택합니다.

![리소스 뷰에서 Resource Health 열기](./media/resource-health-overview/from-resource-blade.png)

**모든 서비스**를 선택하고 필터 텍스트 상자에 **Resource Health**를 입력하여 Resource Health에 액세스할 수도 있습니다. **도움말 + 지원** 창에서 [Resource Health](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth)를 선택합니다.

!["모든 서비스"에서 Resource Health 열기](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>다음 단계

Resource Health에 대한 자세한 내용은 다음 리소스를 확인하세요.
-  [Azure Resource Health에서 리소스 유형 및 상태 검사](resource-health-checks-resource-types.md)
-  [Azure Resource Health에 대한 질문과 대답](resource-health-faq.md)




