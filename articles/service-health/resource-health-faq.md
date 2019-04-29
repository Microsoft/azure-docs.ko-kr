---
title: Azure Resource Health FAQ | Microsoft Docs
description: Azure Resource Health 개요
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 01/29/2019
ms.workload: Supportability
ms.openlocfilehash: b4062b3f0bc389de4403ac81b56688508f5ea50e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620830"
---
# <a name="azure-resource-health-faq"></a>Azure Resource Health FAQ
Azure Resource Health에 대해 자주 묻는 질문과 답변에 대해 알아보세요.

## <a name="what-is-azure-resource-health"></a>Azure Resource Health란?
Resource Health는 Azure 문제가 리소스에 영향을 주는 경우 진단 및 지원을 도와줍니다. 리소스의 현재 및 이전 상태에 대해 알려주고 문제를 완화하는 데 도움이 됩니다. Resource Health는 Azure 서비스 문제와 관련된 도움이 필요할 때 기술 지원을 제공합니다.  

## <a name="what-is-the-resource-health-intended-for"></a>리소스 상태는 어떤 목적으로 고안되었나요?
리소스에 문제가 감지되면 리소스 상태를 통해 근본 원인을 진단할 수 있습니다. Azure 서비스 문제와 관련된 도움이 더 필요할 때 기술 지원을 제공하고 문제를 완화하도록 지원합니다.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Resource Health에서 어떤 상태 검사를 수행하나요?
리소스 상태에서는 [리소스 유형](resource-health-checks-resource-types.md)에 따라 다양한 검사를 수행합니다. 이러한 검사는 3가지 문제 유형을 구현하도록 설계되었습니다. 
- 계획되지 않은 이벤트(예: 예기치 않은 호스트 재부팅)
- 계획된 이벤트(예약된 호스트 OS 업데이트)
- 사용자 작업으로 트리거된 이벤트(예: 사용자가 가상 머신을 다시 부팅)

## <a name="what-does-each-of-the-health-status-mean"></a>각 상태는 무엇을 의미하나요?
3가지 상태가 있습니다.
- 사용 가능: Azure 플랫폼에 이 리소스에 영향을 줄 수 있는 알려진 문제가 없습니다.
- 사용할 수 없음: 리소스 상태에서 리소스에 영향을 주는 문제를 감지했습니다.
- 알 수 없음: Resource Health에서 리소스에 대한 정보 수신을 중지했으므로 리소스의 상태를 확인할 수 없습니다. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>알 수 없는 상태는 무엇을 의미하나요? 내 리소스에 문제가 있나요?
Resource Health에서 특정 리소스에 대한 정보 수신을 중지하면 상태가 알 수 없음으로 설정됩니다. 이 상태는 리소스 상태의 최종적인 표시는 아니지만 문제가 발생한 경우 Azure 문제가 있음을 나타낼 수 있습니다.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>사용할 수 없는 리소스에 대한 도움을 받으려면 어떻게 하나요?
Resource Health 블레이드에서 지원 요청을 제출할 수 있습니다. 플랫폼 이벤트로 인해 리소스를 사용할 수 없는 경우 요청을 열기 위해 Microsoft와 지원 계약이 필요하지 않습니다.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>Resource Health에서는 플랫폼 문제로 인한 사용 불가와 사용자가 의도한 사용 불가가 구분되나요?
예, 리소스를 사용할 수 없는 경우 Resource Health는 다음과 같은 범주 중 하나에서 근본 원인을 식별합니다. 
-   사용자가 시작한 작업
-   계획된 이벤트 
-   계획되지 않은 이벤트

포털에서는 사용자가 시작한 작업이 파란색 알림 아이콘을 사용하여 표시되지만 계획 및 계획되지 않은 이벤트는 빨간색 경고 아이콘을 사용하여 표시됩니다. 자세한 내용은 [Resource Health 개요](Resource-health-overview.md)에 제공됩니다.  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Resource Health를 내 모니터링 도구에 통합할 수 있나요?
리소스 상태에 [지원 미리 보기](resource-health-alert-arm-template-guide.md) 활동 로그 기반 경고에 대 한 합니다. 활동 로그 경고를 사용 하 여 [작업 그룹](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/action-groups) 사용자에 게 알리는 경고가 트리거 되었습니다. 작업 그룹 다양 한 전자 메일, SMS, 웹 후크, ITSM 작업 같은 알림 채널을 지원합니다.

## <a name="where-do-i-find-resource-health"></a>Resource Health는 어디서 찾을 수 있나요?
Azure Portal에 로그인하면 여러 가지 방법으로 Resource Health에 액세스할 수 있습니다.
- 리소스로 이동합니다. 왼쪽 탐색에서 **Resource Health**를 선택합니다.
- Azure Service Health 블레이드로 이동합니다.  왼쪽 탐색에서 **Resource Health**를 선택합니다.
- 포털 오른쪽 위 모서리에서 물음표를 선택하고 **도움말 + 지원**을 선택하여 **도움말 + 지원** 블레이드를 엽니다. 블레이드가 열리면 **Resource Health**를 선택합니다.

Resource Health API를 사용하여 리소스 상태에 대한 정보를 얻을 수도 있습니다.

## <a name="is-resource-health-available-for-all-resource-types"></a>모든 리소스 유형에 Resource Health를 사용할 수 있나요?
Resource Health를 통해 지원되는 상태 검사 및 리소스 유형 목록은 [여기](resource-health-checks-resource-types.md)에서 확인할 수 있습니다.

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>내 리소스가 사용 가능으로 표시되지만 그렇지 않다고 생각되면 어떻게 해야 하나요?
리소스 상태를 검사할 때는 상태 오른쪽 아래에서 **Report incorrect health status(잘못된 상태 보고)** 를 클릭하면 됩니다. 보고서를 제출하기 전에 현재 상태가 잘못되었다고 생각하는 이유에 대해 자세히 입력하는 옵션이 제공됩니다.

## <a name="is-resource-health-available-for-all-azure-regions"></a>모든 Azure 지역에서 Resource Health가 제공되나요? 
리소스 상태는 모든 Azure 지역에서 사용할 수 있습니다.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Resource Health는 Azure 상태 또는 Service Health 대시보드와 어떻게 다른가요?
Resource Health에서 제공하는 정보는 Azure 상태 또는 Service Health 대시보드에서 제공하는 정보보다 더 구체적입니다.

[Azure 상태](https://status.azure.com)와 Service Health 대시보드는 광범위한 고객 집합(예: Azure 지역)에 영향을 주는 서비스 문제를 알려주는 반면, Resource Health는 특정 리소스에만 관련된 더 세분화된 이벤트를 노출합니다. 예를 들어 호스트가 예기치 않게 재부팅되는 경우 Resource Health는 가상 머신이 해당 호스트에서 실행 중인 고객에게만 경고합니다.

리소스에 영향을 주는 이벤트에 대한 충분한 가시성을 제공하는 것이 중요하며 Resource Health는 Service Health 대시보드에 게시된 이벤트를 표시합니다.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>각 리소스에 대해 Resource Health를 활성화해야 하나요?
아니요, 상태 정보는 Resource Health를 통해 제공되는 모든 리소스 유형에 대해 사용 가능합니다. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>내 조직에 대한 Resource Health를 활성화해야 하나요?
아니요.  Azure Resource Health는 어떠한 설정 없이도 Azure Portal 내에서 액세스 가능합니다.

## <a name="is-resource-health-available-free-of-charge"></a>Resource Health는 비용 없이 제공되나요?
예.  Azure Resource Health에는 비용이 없습니다.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Resource Health에서 어떤 권장 사항을 제공하나요?
Resource Health는 상태에 따라 문제 해결에 소요되는 시간을 줄이는 것을 목적으로 권장 사항을 제공합니다. 사용 가능한 리소스에 대한 권장 사항은 고객이 직면하는 가장 일반적인 문제를 해결하는 방법에 중점을 둡니다. Azure 계획되지 않은 이벤트로 인해 리소스를 사용할 수 없는 경우 복구 프로세스 동안과 복구 이후 지원에 중점을 둡니다. 

## <a name="next-steps"></a>다음 단계

Resource Health에 대해 알아봅니다.
-  [Azure Resource Health 개요](Resource-health-overview.md)
-  [Azure Resource Health를 통해 사용할 수 있는 리소스 유형 및 상태 검사](resource-health-checks-resource-types.md)
