---
title: Azure Arc 지원 서버(미리 보기) 개요
description: Azure Arc 지원 서버(미리 보기)를 사용하여 Azure 리소스인 것처럼 Azure 외부에 호스팅되는 머신을 관리하는 방법에 대해 알아봅니다.
keywords: Azure Automation, DSC, PowerShell, Desired State Configuration, 업데이트 관리, 변경 내용 추적, 인벤토리, Runbook, Python, 그래픽, 하이브리드
ms.custom: references_regions
ms.date: 08/06/2020
ms.topic: overview
ms.openlocfilehash: dfc4d8e046cef5af7c3eaf0d31ce2d6120dfc8f2
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88211322"
---
# <a name="what-is-azure-arc-enabled-servers-preview"></a>Azure Arc 지원 서버(미리 보기)란?

Azure Arc 지원 서버(미리 보기)를 사용하면 회사 네트워크 또는 다른 클라우드 공급자의 Azure 외부에 호스팅되는 Windows 및 Linux 머신을 네이티브 Azure 가상 머신을 관리하는 방법과 비슷하게 관리할 수 있습니다. 하이브리드 머신은 Azure에 연결되면 연결된 머신이 되어 Azure에서 리소스로 취급됩니다. 연결된 머신마다 리소스 ID가 있고 구독 내에서 리소스 그룹의 일부로 관리되며, Azure Policy 및 태그 적용과 같은 표준 Azure 구성의 이점을 활용할 수 있습니다.

Azure 외부에 호스팅되는 하이브리드 머신에 이 환경을 제공하려면 Azure에 연결하려는 각 머신에 Azure Connected Machine 에이전트를 설치해야 합니다. 이 에이전트는 다른 기능을 제공하지 않으며, Azure [Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md)를 대체하지 않습니다. 머신에서 실행되는 OS 및 워크로드를 사전에 모니터링하거나, 자동화 Runbook 또는 업데이트 관리 같은 솔루션을 사용하여 관리하거나, [Azure Security Center](../../security-center/security-center-intro.md) 같은 다른 Azure 서비스를 사용하려는 경우에는 Windows 및 Linux용 Log Analytics 에이전트가 필요합니다.

>[!NOTE]
>이 미리 보기 릴리스는 평가 목적으로 제공되며 중요한 프로덕션 머신 관리에는 사용하지 않는 것이 좋습니다.
>

## <a name="supported-scenarios"></a>지원되는 시나리오

Azure Arc 지원 서버(미리 보기)에 머신을 연결하면 다음과 같은 구성 관리 작업을 수행할 수 있습니다.

- Azure 가상 머신의 정책 할당과 동일한 환경을 사용하여 [Azure Policy 게스트 구성](../../governance/policy/concepts/guest-configuration.md)을 할당합니다.

- 연결된 머신 게스트 운영 체제 성능을 모니터링하고 애플리케이션 구성 요소를 검색하여 [VM용 Azure Monitor](../../azure-monitor/insights/vminsights-overview.md)를 통해 애플리케이션에서 통신하는 다른 리소스의 프로세스 및 종속성을 모니터링합니다.

- 비 Azure Windows 또는 Linux 머신에 대해 지원되는 [Azure VM 확장](manage-vm-extensions.md)을 사용하여 Azure Automation 상태 구성 및 Azure Monitor Log Analytics 작업 영역과 같은 다른 Azure 서비스와의 배포를 간소화합니다. 여기에는 사용자 지정 스크립트 확장을 사용하여 배포 후 구성 또는 소프트웨어 설치를 수행하는 작업이 포함됩니다.

하이브리드 머신에서 Log Analytics 작업 영역에 수집되어 저정된 로그 데이터에는 이제 리소스 ID와 같은 머신 관련 속성이 포함되어 있습니다. [resource-context](../../azure-monitor/platform/design-logs-deployment.md#access-mode) 로그 액세스를 지원하는 데 사용할 수 있습니다.

## <a name="supported-regions"></a>지원되는 지역

Azure Arc 지원 서버(미리 보기)를 사용하면 특정 영역만 지원됩니다.

- EastUS
- WestUS2
- WestEurope
- SoutheastAsia

대부분의 경우 설치 스크립트를 만들 때 선택한 위치는 머신의 위치와 지리적으로 가장 가까운 Azure 지역이어야 합니다. 미사용 데이터는 사용자가 지정한 지역을 포함하는 Azure 지리 내에 저장되며, 데이터 상주 요구 사항이 있는 경우 선택한 지역에 영향을 줄 수도 있습니다. 머신이 연결된 Azure 지역이 작동 중단의 영향을 받는 경우 연결된 머신에는 영향을 주지 않지만 Azure를 사용하는 관리 작업은 완료하지 못할 수 있습니다. 지역 가동 중단 시 지리적으로 중복된 서비스를 지원하는 여러 위치가 있는 경우 각 위치의 머신을 다른 Azure 지역에 연결하는 것이 가장 좋습니다.

### <a name="agent-status"></a>에이전트 상태

Connected Machine 에이전트는 5분마다 정기적인 하트비트 메시지를 서비스에 보냅니다. 서비스가 머신에서 이러한 하트비트 메시지 수신을 중지하면 해당 머신은 오프라인으로 간주되며 15~30분 내에 포털에서 상태가 자동으로 **연결 끊김**으로 변경됩니다. Connected Machine 에이전트로부터 후속 하트비트 메시지를 받으면 상태가 자동으로 **연결됨**으로 변경됩니다.

## <a name="next-steps"></a>다음 단계

여러 하이브리드 머신에서 Arc 지원 서버(미리 보기)를 평가하거나 활성화하기 전에 [연결된 머신 에이전트 개요](agent-overview.md) 문서를 검토하여 필요한 사항, 에이전트에 대한 기술 세부 정보 및 배포 방법을 이해하세요.
