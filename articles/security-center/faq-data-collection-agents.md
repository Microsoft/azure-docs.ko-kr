---
title: Azure Security Center FAQ - 데이터 수집 및 에이전트
description: 위협을 예방, 감지 및 대응하는 데 도움이 되는 제품인 Azure Security Center의 데이터 수집, 에이전트 및 작업 영역에 대한 질문과 대답
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 0bcf81e0c762dd2a8e63ae242fec77d30f5b2c3d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011857"
---
# <a name="faq---questions-about-data-collection-agents-and-workspaces"></a>FAQ - 데이터 수집, 에이전트 및 작업 영역에 대한 질문

Security Center는 Azure VM(Virtual Machines) 및 가상 머신 확장 집합, IaaS 컨테이너 및 비 Azure 컴퓨터에서 데이터를 수집하여 보안 취약성과 위협을 모니터링합니다. 데이터는 머신에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석용으로 작업 영역에 데이터를 복사하는 Log Analytics 에이전트를 사용하여 수집됩니다.


## <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Security Center에서 만든 작업 영역에서 Azure Monitor 로그에 대한 요금을 청구하나요?

아니요. Security Center에서 만든 작업 영역은 노드 요금 청구당 Azure Monitor 로그에 구성된 동안 Azure Monitor 로그 요금이 청구되지 않습니다. Security Center 청구는 항상 작업 영역에 설치된 Security Center 보안 정책 및 솔루션에 기반합니다.

- **무료 계층** – Security Center는 기본 작업 영역에서 'SecurityCenterFree' 솔루션을 사용하도록 설정합니다. 체험 계층에 대한 요금이 청구되지 않습니다.

- **표준 계층** – Security Center는 기본 작업 영역에서 'Security' 솔루션을 사용하도록 설정합니다.

자세한 내용은 [Security Center 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)을 참조하세요.

> [!NOTE]
> Security Center에서 만든 작업 영역의 Log Analytics 가격 책정 계층은 Security Center 청구에 영향을 주지 않습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a name="what-is-the-log-analytics-agent"></a>Log Analytics 에이전트는 무엇 인가요?

보안 취약점 및 위협을 모니터링 하려면 Azure Security Center [Log Analytics 에이전트](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) 에 따라 달라 집니다 .이 에이전트는 Azure Monitor 서비스에서 사용 하는 것과 동일한 에이전트입니다. 

에이전트를 Microsoft Monitoring Agent (또는 "MMA") 라고도 합니다. 

에이전트는 연결 된 컴퓨터에서 다양 한 보안 관련 구성 세부 정보 및 이벤트 로그를 수집한 다음 추가 분석을 위해 Log Analytics 작업 영역에 데이터를 복사 합니다. 이러한 데이터의 예로는 운영 체제 유형 및 버전, 운영 체제 로그(Windows 이벤트 로그), 실행 중인 프로세스, 머신 이름, IP 주소, 로그인된 사용자를 들 수 있습니다.

다음 페이지에 설명 된 대로 컴퓨터에서 에이전트에 대해 지원 되는 운영 체제 중 하나를 실행 하 고 있는지 확인 합니다.

* [Windows에서 지원 되는 운영 체제에 대 한 Log Analytics 에이전트](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

* [Linux 지원 운영 체제에 대 한 Log Analytics 에이전트](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

[Log Analytics 에이전트가 수집 하는 데이터](security-center-enable-data-collection.md)에 대해 자세히 알아보세요.




## <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-log-analytics-agent-installation"></a>Log Analytics 에이전트 설치의 자동 프로비저닝을 위한 VM의 요건은 무엇입니까?

다음과 같은 경우 Windows 또는 Linux IaaS VM이 요건 충족됩니다.

- Log Analytics 에이전트 확장이 VM에 현재 설치되어 있지 않습니다.
- VM이 실행 중인 상태입니다.
- Windows 또는 Linux [Azure Virtual Machine 에이전트](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)가 설치되어 있습니다.
- VM이 웹 애플리케이션 방화벽이나 차세대 방화벽과 같은 어플라이언스로 사용되지 않습니다.


## <a name="where-is-the-default-log-analytics-workspace-created"></a>기본 Log Analytics 작업 영역은 어디에서 생성되나요?

기본 작업 영역의 위치는 Azure 지역에 따라 다릅니다.

- 미국 및 브라질에 있는 VM의 경우 작업 영역 위치는 미국입니다.
- 캐나다에 있는 VM의 경우 작업 영역 위치는 캐나다입니다.
- 유럽에 있는 VM의 경우 작업 영역 위치는 유럽입니다.
- 영국에 있는 VM의 경우 작업 영역 위치는 영국입니다.
- 동아시아 및 동남아시아에 있는 VM의 경우 작업 영역 위치는 아시아입니다.
- 대한민국에 있는 VM의 경우 작업 영역 위치는 대한민국입니다.
- 인도에 있는 VM의 경우 작업 영역 위치는 인도입니다.
- 일본에 있는 VM의 경우 작업 영역 위치는 일본입니다.
- 중국에 있는 VM의 경우 작업 영역 위치는 중국입니다.
- 오스트레일리아에 있는 VM의 경우 작업 영역 위치는 오스트레일리아입니다.


## <a name="what-data-is-collected-by-the-log-analytics-agent"></a>Log Analytics 에이전트에서 수집 하는 데이터는 무엇 인가요?

에이전트에서 모니터링 하는 응용 프로그램 및 서비스의 전체 목록은 [Azure Monitor에서 모니터링 하는 기능](https://docs.microsoft.com/azure/azure-monitor/monitor-reference#azure-services)을 참조 하세요.

> [!IMPORTANT]
> Azure 방화벽과 같은 일부 서비스의 경우 로깅을 사용 하도록 설정 하 고 번잡 리소스 (예: 로그를 *자세한 정보*로 설정)를 선택한 경우 Log Analytics 작업 영역 저장소 요구에 상당한 영향을 줄 수 있습니다. 


## <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Security Center에서 만든 기본 작업 영역을 삭제할 수 있나요?

**기본 작업 영역을 삭제하지 않는 것이 좋습니다.** Security Center는 기본 작업 영역을 사용하여 VM의 보안 데이터를 저장합니다. 작업 영역을 삭제하는 경우 Security Center는 이 데이터 및 일부 보안 권장 사항을 수집할 수 없고 경고를 사용할 수 없습니다.

복구하려면 삭제된 작업 영역에 연결된 VM에서 Log Analytics 에이전트를 제거합니다. Security Center는 에이전트를 다시 설치하고 새 기본 작업 영역을 만듭니다.

## <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>기존 Log Analytics 작업 영역을 사용하려면 어떻게 해야 하나요?

기존 Log Analytics 작업 영역을 선택하여 Security Center에서 수집한 데이터를 저장할 수 있습니다. 기존 Log Analytics 작업 영역을 사용하려면 다음을 수행합니다.

- 작업 영역이 선택한 Azure 구독과 연결되어 있어야 합니다.
- 작업 영역에 액세스하려면 읽기 이상의 권한이 필요합니다.

기존 Log Analytics 작업 영역을 선택하려면 다음을 수행합니다.

1. **보안 정책 - 데이터 수집** 아래에서 **다른 작업 영역 사용**을 선택합니다.

    ![다른 작업 영역 사용][4]

1. 풀다운 메뉴에서 수집된 데이터를 저장할 작업 영역을 선택합니다.

    > [!NOTE]
    > 풀다운 메뉴에는 액세스 권한이 있으며 Azure 구독에 포함된 작업 영역만 표시됩니다.

1. **저장**을 선택합니다. 모니터링되는 VM을 다시 구성할지를 묻는 메시지가 표시됩니다.

    - 새 작업 영역 설정을 **새 VM에만 적용**하려면 **아니요**를 선택합니다. 그러면 새 작업 영역 설정이 새 에이전트 설치(Log Analytics 에이전트가 설치되어 있지 않은 새로 검색된 VM)에만 적용됩니다.
    - 새 작업 영역 설정을 **모든 VM에 적용**하려면 **예**를 선택합니다. 이 경우에는 Security Center에서 만든 작업 영역에 연결된 모든 VM이 새 대상 작업 영역에 다시 연결됩니다.

    > [!NOTE]
    > **예**를 선택하는 경우 모든 VM이 새 대상 작업 영역에 다시 연결될 때까지는 Security Center에서 생성된 작업 영역을 삭제하면 안 됩니다. 작업 영역을 너무 빨리 삭제하면 이 작업이 실패합니다.

    - 작업을 취소하려면 **취소**를 선택합니다.

## <a name="what-if-the-log-analytics-agent-was-already-installed-as-an-extension-on-the-vm"></a>Log Analytics 에이전트가 VM에 확장으로 이미 설치되어 있으면 어떻게 되나요?<a name="mmaextensioninstalled"></a>

모니터링 에이전트가 확장으로 설치될 때 확장 구성은 단일 작업 영역에만 보고하도록 허용합니다. Security Center는 사용자 작업 영역에 대한 기존 연결을 재정의하지 않습니다. Security Center는 “Security” 또는 “SecurityCenterFree” 솔루션이 설치된 경우 이미 연결된 작업 영역에 있는 VM의 보안 데이터를 저장합니다. Security Center는 이 프로세스에서 확장 버전을 최신 버전으로 업그레이드할 수 있습니다.

자세한 내용은 [기존 에이전트 설치 시 자동 프로비저닝](security-center-enable-data-collection.md#preexisting)을 참조하세요.



## <a name="what-if-a-log-analytics-agent-is-directly-installed-on-the-machine-but-not-as-an-extension-direct-agent"></a>Log Analytics 에이전트가 컴퓨터에 직접 설치되어 있지만 확장(직접 에이전트)이 아닌 경우 어떻게 되나요?<a name="directagentinstalled"></a>

Log Analytics 에이전트가 VM에 직접 설치되어 있는 경우(Azure 확장이 아님) Security Center는 Log Analytics 에이전트 확장을 설치하고 Log Analytics 에이전트를 최신 버전으로 업그레이드할 수 있습니다.

설치된 에이전트는 이미 구성된 작업 영역에 계속 보고하고, Security Center에서 구성된 작업 영역에도 보고합니다(Windows 컴퓨터에서는 멀티 호밍이 지원됨).

구성된 작업 영역이 사용자 작업 영역(Security Center의 기본 작업 영역이 아님)인 경우 해당 작업 영역에 보고하는 VM 및 컴퓨터에서 이벤트 처리를 시작하려면 Security Center에 대해 “Security”/“SecurityCenterFree” 솔루션을 설치해야 합니다.

Linux 컴퓨터의 경우 에이전트 멀티 호밍은 아직 지원되지 않습니다. 따라서 기존 에이전트 설치가 감지되면 자동 프로비저닝이 발생하지 않고 컴퓨터의 구성이 변경되지 않습니다.

기존 에이전트가 탐지되면 2019년 3월 17일 이전에 Security Center에 등록된 구독 중인 기존 컴퓨터의 경우 Log Analytics 에이전트 확장이 설치되지 않고 컴퓨터에 영향을 주지 않습니다. 이러한 컴퓨터의 경우 해당 컴퓨터에서 에이전트 설치 문제를 해결하려면 “컴퓨터에서 모니터링 에이전트 상태 문제 해결” 권장 사항을 참조하세요.

자세한 내용은 다음 섹션 [System Center Operations Manager 또는 OMS 직접 에이전트가 내 VM에 이미 설치되어 있으면 어떻게 되나요?](#scomomsinstalled)를 참조하세요.

## <a name="what-if-a-system-center-operations-manager-agent-is-already-installed-on-my-vm"></a>System Center Operations Manager 에이전트가 내 VM에 이미 설치되어 있으면 어떻게 되나요?<a name="scomomsinstalled"></a>

Security Center는 기존 System Center Operations Manager 에이전트에 Log Analytics 에이전트 확장을 함께 설치합니다. 기존 에이전트는 System Center Operations Manager 서버에 정상적으로 계속 보고합니다. Operations Manager 에이전트와 Log Analytics 에이전트는 이 프로세스 중에 최신 버전으로 업데이트되는 일반적인 런타임 라이브러리를 공유합니다. 참고 - Operations Manager 에이전트의 버전 2012가 설치되어 있는 경우 자동 프로비저닝을 설정하지 마십시오(Operations Manager 서버가 2012 버전인 경우에도 관리 기능이 손실될 수 있음).


## <a name="what-is-the-impact-of-removing-these-extensions"></a>이러한 확장을 제거할 경우 어떤 영향이 있나요?

Microsoft Monitoring 확장을 제거하는 경우 Security Center는 VM의 보안 데이터 및 일부 보안 권장 사항을 수집할 수 없고 경고를 사용할 수 없습니다. 24시간 이내에 Security Center는 VM이 확장을 누락하고 확장을 다시 설치했는지 확인합니다.


## <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>자동 에이전트 설치 및 작업 영역 생성을 중지하려면 어떻게 할까요?

보안 정책에서 구독에 대해 자동 프로비저닝을 해제할 수는 있지만, 해제하지 않는 것이 좋습니다. 자동 프로비저닝을 해제하면 Security Center 권장 사항 및 경고 기능이 제한됩니다. 자동 프로비저닝을 사용하지 않도록 설정하려면 다음을 수행합니다.

1. 표준 계층에 대 한 구독이 구성 된 경우 해당 구독에 대 한 보안 정책을 열고 **무료** 계층을 선택 합니다.

   ![가격 책정 계층][1]

1. 다음으로, **보안 정책 - 데이터 수집** 페이지에서 **끄기**를 선택하여 자동 프로비저닝을 해제합니다.
   ![데이터 수집][2]


## <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>자동 에이전트 설치 및 작업 영역 생성을 옵트아웃해야 하나요?

> [!NOTE]
> 자동 프로비전을 옵트아웃하도록 선택하는 경우 [옵트아웃의 의미는 무엇인가요?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) 및 [옵트아웃에 권장되는 단계](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)를 참조하세요.

다음에 해당하는 경우 자동 프로비전을 옵트아웃하는 것이 좋습니다.

- Security Center에 의한 자동 에이전트 설치가 전체 구독에 적용됩니다. VM의 하위 집합에 자동 설치를 적용할 수 없습니다. Log Analytics 에이전트와 함께 설치할 수 없는 중요한 VM이 있는 경우 자동 프로비저닝을 옵트아웃해야 합니다.
- Log Analytics 에이전트 확장을 설치하면 에이전트 버전이 업데이트됩니다. 이는 직접 에이전트와 System Center Operations Manager 에이전트에 적용됩니다. 후자의 경우에는 Operations Manager 및 Log Analytics 에이전트가 공통 런타임 라이브러리를 공유하며, 이는 프로세스에서 업데이트됩니다. 설치된 Operations Manager 에이전트가 2012 버전이고 업그레이드된 경우 Operations Manager 서버도 2012 버전이면 관리 기능이 손실될 수 있습니다. 설치된 Operations Manager 에이전트가 2012 버전인 경우 자동 프로비저닝을 옵트아웃하는 것이 좋습니다.
- 외부 구독의 사용자 지정 작업 영역(중앙 집중식 작업 영역)이 있는 경우 자동 프로비저닝을 옵트아웃해야 합니다. Log Analytics 에이전트 확장을 수동으로 설치하고 Security Center가 연결을 재정의하지 않고 작업 영역에 연결할 수 있습니다.
- 구독 내에 자신의 사용자 지정 작업 영역이 있고 구독당 작업 영역을 여러 개 만들지 않으려는 경우 두 가지 옵션이 있습니다.

   1. 자동 프로비전을 옵트아웃할 수 있습니다. 마이그레이션 후 기본 작업 영역 설정을 [기존 Log Analytics 작업 영역을 사용하려면 어떻게 해야 하나요?](#how-can-i-use-my-existing-log-analytics-workspace)의 설명에 따라 설정합니다.

   1. 또는 마이그레이션이 완료되고, Log Analytics 에이전트가 VM에 설치되고, VM이 생성된 작업 영역에 연결되도록 허용할 수 있습니다. 그런 다음 이미 설치된 에이전트를 재구성하도록 선택하여 기본 작업 영역 설정을 설정하고 자신의 사용자 지정 작업 영역을 선택합니다. 자세한 내용은 [기존 Log Analytics 작업 영역을 사용하려면 어떻게 해야 하나요?](#how-can-i-use-my-existing-log-analytics-workspace)를 참조하세요.


## <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>자동 프로비전을 옵트아웃하는 의미는 무엇인가요?

마이그레이션이 완료되면 Security Center는 VM에서 보안 데이터를 수집할 수 없으며 일부 보안 권장 사항과 경고를 사용할 수 없습니다. 옵트아웃하는 경우 Log Analytics 에이전트를 수동으로 설치합니다. [옵트아웃에 권장되는 단계](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)를 참조하세요.


## <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>자동 프로비전을 옵트아웃하는 경우 권장되는 단계는 무엇인가요?

Security Center가 VM의 보안 데이터를 수집하고 권장 사항 및 경고를 제공할 수 있도록 Log Analytics 에이전트 확장을 수동으로 설치합니다. 설치 지침에 대해서는 [Windows VM용 에이전트 설치](../virtual-machines/extensions/oms-windows.md) 또는 [Linux VM용 에이전트 설치](../virtual-machines/extensions/oms-linux.md)를 참조하세요.

에이전트를 기존 사용자 지정 작업 영역이나 Security Center가 만든 작업 영역에 연결할 수 있습니다. 사용자 지정 작업 영역에 ‘Security’ 또는 ‘SecurityCenterFree’ 솔루션이 활성화되어 있지 않으면 솔루션을 적용해야 합니다. 적용하려면 사용자 지정 작업 영역 또는 구독을 선택하고 **보안 정책 - 가격 책정 계층** 페이지를 통해 가격 책정 계층을 적용합니다.

   ![가격 책정 계층][1]

Security Center는 선택한 가격 책정 계층을 기반으로 작업 영역에 올바른 솔루션을 사용하도록 설정합니다.


## <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Security Center에서 설치한 OMS 확장을 제거하려면 어떻게 할까요?<a name="remove-oms"></a>

Log Analytics 에이전트를 수동으로 제거할 수 있습니다. Security Center 권장 사항 및 경고를 제한하기 때문에 권장되지는 않습니다.

> [!NOTE]
> 데이터 수집을 사용하는 경우 Security Center는 에이전트를 제거한 후에 다시 설치합니다.  수동으로 에이전트를 제거하기 전에 데이터 수집을 사용하지 않도록 설정해야 합니다. 데이터 수집을 사용하지 않도록 설정하는 지침은 자동 에이전트 설치 및 작업 영역 생성을 중지하려면 어떻게 할까요?를 참조하세요.

수동으로 에이전트를 제거하려면:

1.    포털에서 **Log Analytics**을 엽니다.

1.    Log Analytics 페이지에서 작업 영역을 선택합니다.

1.    모니터링하지 않을 VM을 선택하고 **연결 끊기**를 선택합니다.

   ![에이전트 제거][3]

> [!NOTE]
> Linux VM에 이미 비확장 OMS 에이전트가 설치된 경우 확장을 제거하면 에이전트도 제거되고 다시 설치해야 합니다.


## <a name="how-do-i-disable-data-collection"></a>데이터 컬렉션을 사용하지 않도록 설정하려면 어떻게 해야 하나요?

시스템 업데이트, OS 취약성 및 엔드포인트 보호에 대한 보안 경고와 권장 사항을 받으려면 자동 프로비저닝을 사용하는 것이 좋습니다. 기본적으로 자동 프로 비전은 사용 하지 않도록 설정 되어 있습니다.

사용 하도록 설정 했지만 이제 사용 하지 않도록 설정 하려는 경우:

1. [Azure Portal](https://portal.azure.com)에서 **Security Center** 를 열고 **보안 정책**을 선택 합니다.

1. 자동 프로비저닝을 사용 하지 않도록 설정할 구독을 선택 합니다.

    **보안 정책-데이터 수집이** 열립니다.

1. **자동 프로비전**에서 **끔**을 선택합니다.


## <a name="how-do-i-enable-data-collection"></a>데이터 컬렉션을 사용하도록 설정하려면 어떻게 해야 하나요?

보안 정책에서 Azure 구독에 대한 데이터 수집을 사용하도록 설정할 수 있습니다. 데이터 수집을 사용하도록 설정하려면 [Azure Portal에 로그인](https://portal.azure.com)하여 **찾아보기**, **Security Center**, **보안 정책**을 차례로 선택합니다. 자동 프로비전을 사용할 구독을 선택합니다. 구독을 선택하면 **보안 정책 - 데이터 수집**이 열립니다. **자동 프로비전**에서 **켬**을 선택합니다.


## <a name="what-happens-when-data-collection-is-enabled"></a>데이터 수집을 사용하도록 설정하면 어떻게 될까요?

자동 프로비저닝을 사용하도록 설정한 경우 Security Center에서 지원되는 모든 Azure VM 및 새로 만든 Azure VM에 Log Analytics 에이전트를 프로비저닝합니다. 자동 프로비저닝을 사용하는 것이 좋지만, 에이전트를 수동으로 설치할 수도 있습니다. [Log Analytics 에이전트 확장을 설치하는 방법에 대해 알아봅니다](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

에이전트는 프로세스 생성 이벤트 4688 및 이벤트 4688 내의 *CommandLine* 필드를 활성화합니다. VM에서 생성된 새로운 프로세스는 EventLog에서 기록되고 Security Center의 검색 서비스에 의해 모니터링됩니다. 각 새로운 프로세스에 대해 기록된 세부 정보에 대한 자세한 내용은 [4688의 설명 필드](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields)를 참조하세요. 또한 에이전트는 VM에서 생성되는 4688 이벤트를 수집하고 검색에 저장합니다.

또한 에이전트는 [적응형 애플리케이션 제어](security-center-adaptive-application.md)에 데이터 수집을 사용하도록 설정하고, Security Center는 모든 애플리케이션을 허용하도록 감사 모드에서 로컬 AppLocker 정책을 구성합니다. 이 정책으로 인해 AppLocker에서는 이벤트를 생성하고, Security Center에서 수집하여 활용합니다. 이 정책은 이미 AppLocker 정책이 구성된 컴퓨터에서는 구성할 수 없습니다. 

Security Center가 VM에서 의심스러운 작업을 감지하면 고객은 경우 [보안 연락처 정보](security-center-provide-security-contact-details.md)가 제공된 경우 전자 메일을 통해 알림을 받습니다. 경고는 또한 Security Center의 보안 경고 대시보드에 표시됩니다.


## <a name="will-security-center-work-using-an-oms-gateway"></a>Security Center는 OMS 게이트웨이를 사용하여 작동하나요?

예. Azure Security Center는 Log Analytics 에이전트를 사용하여 Azure VM 및 서버에서 데이터를 수집하는 Azure Monitor를 활용합니다.
데이터를 수집하려면 각 VM과 서버가 HTTPS를 사용하여 인터넷에 연결되어야 합니다. 직접 연결하거나 프록시를 사용하거나 [OMS 게이트웨이](../azure-monitor/platform/gateway.md)를 통해 연결할 수 있습니다.


## <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent가 서버의 성능에 미치는 영향

에이전트는 시스템 리소스의 명목 양을 소비하며 성능에 거의 영향을 미치지 않습니다. 성능 영향과 에이전트 및 확장에 대한 자세한 내용은 [계획 및 작업 가이드](security-center-planning-and-operations-guide.md#data-collection-and-storage)를 참조하세요.




<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/use-another-workspace.png
