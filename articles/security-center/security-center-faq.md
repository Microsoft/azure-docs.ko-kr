---
title: Azure Security Center FAQ(질문과 대답) | Microsoft Docs
description: 이 FAQ는 Azure Security Center에 대한 질문에 답변합니다.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: monhaber
ms.openlocfilehash: 79faab0dcf2dd4c5592fe0543fa63f2538facf36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60909623"
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Azure Security Center FAQ(질문과 대답)
이 FAQ는 증가된 가시성으로 위협을 예방, 감지 및 대응하고 Microsoft Azure 리소스의 보안을 제어하는 서비스인 Azure Security Center에 관한 질문에 답변합니다.

> [!NOTE]
> 2017년 6월 초를 시작으로 Security Center는 Microsoft Monitoring Agent를 사용하여 데이터를 수집 및 저장합니다. 자세한 내용은 [Azure Security Center 플랫폼 마이그레이션](security-center-platform-migration.md)을 참조하세요. 이 문서의 정보는 Microsoft Monitoring Agent로 전환된 후의 Security Center 기능을 나타냅니다.
>
>

## <a name="general-questions"></a>일반적인 질문
### <a name="what-is-azure-security-center"></a>Azure Security Center란?
Azure Security Center는 Azure 리소스의 보안에 대한 향상된 가시성과 제어권을 통해 위협을 예방하고 감지하며 위협에 대응하는 데 도움이 됩니다. 이는 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 보안 솔루션의 광범위한 환경에서 작동합니다.

### <a name="how-do-i-get-azure-security-center"></a>Azure Security Center를 이용하려면 어떻게 해야 하나요?
Azure Security Center는 Microsoft Azure 구독을 사용하도록 설정되어 있으며 [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에서 액세스합니다. [포털에 로그인](https://portal.azure.com)하여 **찾아보기**를 선택하고 **Security Center**로 스크롤합니다.  

## <a name="billing"></a>결제
### <a name="how-does-billing-work-for-azure-security-center"></a>Azure Security Center에 대한 청구는 어떻게 작동합니까?
Security Center는 두 계층으로 제공됩니다.

**무료 계층**은 Azure 리소스, 기본 보안 정책, 보안 권장 사항, 그리고 파트너의 보안 제품 및 서비스와의 통합 등의 보안 상태를 볼 수 있게 해줍니다.

**표준 계층**은 인텔리전스, 동작 분석, 변칙 검색, 보안 사고 위협 및 위협 특성 보고서 등 고급 위협 감지 기능을 추가합니다. 표준 계층 무료 평가판을 시작할 수 있습니다. 업그레이드하려면 보안 정책에서 [가격 책정 계층](https://docs.microsoft.com/azure/security-center/security-center-pricing)을 선택합니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

### <a name="how-can-i-track-who-in-my-organization-performed-pricing-tier-changes-in-azure-security-center"></a>내 조직에 Azure Security Center의 가격 책정 계층 변경 내용을 수행 하 게 추적할 수는 방법
Azure 구독의 가격 책정 계층을 변경할 수 있는 권한이 있는 관리자는 여러 수 있으므로, 사용자 가격 책정 계층 변경을 수행한 사람 인지 알고 있어야 할 수도 있습니다. 를 사용 하 여 Azure 활동 로그를 사용할 수 하나. 추가 지침은 하세요 [여기](https://techcommunity.microsoft.com/t5/Security-Identity/Tracking-Changes-in-the-Pricing-Tier-for-Azure-Security-Center/td-p/390832)

## <a name="permissions"></a>권한
Azure Security Center는 Azure에서 사용자, 그룹 및 서비스에 [기본 제공 역할](../role-based-access-control/built-in-roles.md)을 제공하는 [RBAC(역할 기반 Access Control)](../role-based-access-control/role-assignments-portal.md)를 사용합니다.

Security Center는 리소스 구성을 평가하여 보안 문제 및 취약성을 식별합니다. Security Center에서는 리소스가 속한 구독이나 리소스 그룹에 대한 소유자, 참가자 또는 독자 역할을 할당 받을 때 리소스와 관련된 항목만 볼 수 있습니다.

Security Center의 역할 및 허용된 작업에 대한 자세한 내용은 [Azure Security Center의 권한](security-center-permissions.md)을 참조하세요.

## <a name="data-collection-agents-and-workspaces"></a>데이터 수집, 에이전트 및 작업 영역
Security Center는 Azure virtual machines (Vm), 가상 머신 확장 집합 (VMSS), IaaS 컨테이너 및 보안 취약성과 위협을 모니터링 하는 온-프레미스) (포함 비 Azure 컴퓨터에서 데이터를 수집 합니다. Microsoft Monitoring Agent를 사용하여 데이터를 수집합니다. Microsoft Monitoring Agent는 머신에서 다양한 보안 관련 구성 및 이벤트 로그를 읽고 분석용으로 작업 영역에 데이터를 복사합니다.

### <a name="am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center"></a>Security Center에서 만든 작업 영역에서 Azure Monitor 로그에 대 한 요금은 청구 되나요?
아니요. Security Center에서 만든 작업 영역은 노드 요금 청구 당 Azure Monitor 로그에 대 한 구성 하는 동안 Azure Monitor 로그 요금이 발생 하지 않습니다. Security Center 청구는 항상 작업 영역에 설치된 Security Center 보안 정책 및 솔루션에 기반합니다.

- **무료 계층** – Security Center는 기본 작업 영역에서 'SecurityCenterFree' 솔루션을 사용하도록 설정합니다. 체험 계층에 대한 요금이 청구되지 않습니다.
- **표준 계층** – Security Center는 기본 작업 영역에서 'Security' 솔루션을 사용하도록 설정합니다.

자세한 내용은 [Security Center 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)을 참조하세요. 가격 책정 페이지 주소는 2017년 6월부터 보안 데이터 저장소 및 비례 배분 청구로 변경됩니다.

> [!NOTE]
> Log analytics 가격 책정 계층은 Security Center에서 만든 작업 영역에는 Security Center 청구 영향을 주지 않습니다.
>
>

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Microsoft Monitoring Agent 설치의 자동 프로비전을 위한 VM의 요건은 무엇입니까?
다음과 같은 경우 Windows 또는 Linux IaaS VM이 요건 충족됩니다.

- Microsoft Monitoring Agent 확장이 VM에 현재 설치되어 있지 않습니다.
- VM이 실행 중인 상태입니다.
- Windows 또는 Linux [Azure Virtual Machine 에이전트](https://docs.microsoft.com/en-us/azure/virtual-machines/extensions/agent-windows) 설치 됩니다.
- VM이 웹 애플리케이션 방화벽이나 차세대 방화벽과 같은 어플라이언스로 사용되지 않습니다.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Security Center에서 만든 기본 작업 영역을 삭제할 수 있나요?
**기본 작업 영역을 삭제하지 않는 것이 좋습니다.** Security Center는 기본 작업 영역을 사용하여 VM의 보안 데이터를 저장합니다.  작업 영역을 삭제하는 경우 Security Center는 이 데이터 및 일부 보안 권장 사항을 수집할 수 없고 경고를 사용할 수 없습니다.

복구하려면 삭제된 작업 영역에 연결된 VM에서 Microsoft Monitoring Agent를 제거합니다. Security Center는 에이전트를 다시 설치하고 새 기본 작업 영역을 만듭니다.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>기존 Log Analytics 작업 영역을 사용하려면 어떻게 해야 하나요?

기존 Log Analytics 작업 영역을 선택하여 Security Center에서 수집한 데이터를 저장할 수 있습니다. 기존 Log Analytics 작업 영역을 사용하려면 다음을 수행합니다.

- 작업 영역이 선택한 Azure 구독과 연결되어 있어야 합니다.
- 작업 영역에 액세스하려면 읽기 이상의 권한이 필요합니다.

기존 Log Analytics 작업 영역을 선택하려면 다음을 수행합니다.

1. **보안 정책 - 데이터 수집** 아래에서 **다른 작업 영역 사용**을 선택합니다.

   ![다른 작업 영역 사용][5]

2. 풀다운 메뉴에서 수집된 데이터를 저장할 작업 영역을 선택합니다.

   > [!NOTE]
   > 풀다운 메뉴에는 액세스 권한이 있으며 Azure 구독에 포함된 작업 영역만 표시됩니다.
   >
   >

3. **저장**을 선택합니다.
4. **저장**을 선택하고 나면 모니터링되는 VM을 다시 구성할지를 묻는 메시지가 표시됩니다.

   - 새 작업 영역 설정을 **새 VM에만 적용**하려면 **아니요**를 선택합니다. 그러면 새 작업 영역 설정이 새 Agent 설치(Microsoft Monitoring Agent가 설치되어 있지 않은 새로 검색된 VM)에만 적용됩니다.
   - 새 작업 영역 설정을 **모든 VM에 적용**하려면 **예**를 선택합니다. 이 경우에는 Security Center에서 만든 작업 영역에 연결된 모든 VM이 새 대상 작업 영역에 다시 연결됩니다.

   > [!NOTE]
   > 예를 선택하는 경우 모든 VM이 새 대상 작업 영역에 다시 연결될 때까지는 Security Center에서 작성된 작업 영역을 삭제하면 안 됩니다. 작업 영역을 너무 빨리 삭제하면 이 작업이 실패합니다.
   >
   >

   - 작업을 취소하려면 **취소**를 선택합니다.

### 그렇다면 Microsoft Monitoring Agent vm 확장으로 이미 설치 된?<a name="mmaextensioninstalled"></a>
Monitoring Agent 확장으로 설치 될 때만 단일 작업 영역에 보고 확장 구성을 허용 합니다. Security Center는 사용자 작업 영역에 대한 기존 연결을 재정의하지 않습니다. "보안" 또는 "SecurityCenterFree" 솔루션 설치가 완료 된 후에 security Center 이미 연결 되어 있는 작업 영역에 VM에서 보안 데이터를 저장 합니다. Security Center는이 프로세스에서 최신 버전으로 확장 버전을 업그레이드할 수 있습니다.

자세한 내용은 [기존 에이전트 설치의 경우에서 자동 프로 비전](security-center-enable-data-collection.md#preexisting)합니다.


### Microsoft Monitoring Agent 했습니다 어떻게 확장 (직접 에이전트) 것이 아니라 컴퓨터에서 직접 설치 되어 있습니까?<a name="directagentinstalled"></a>
Microsoft Monitoring Agent (을 Azure 확장이 아니라) VM에 직접 설치 되어 있으면 Security Center가 Microsoft Monitoring Agent 확장을 설치 하 고 Microsoft Monitoring agent를 최신 버전으로 업그레이드할 수 있습니다.
설치 된 에이전트는 계속 해당 이미 구성 된 작업 영역에 보고 하 고 또한 Security Center에서 구성 된 작업 영역에 보고 됩니다 (멀티 호 밍 지원 됨).
설치 해야 구성된 된 작업 영역 사용자 작업 영역 (없습니다 Security Center의 기본 작업 영역) 인 경우는 "보안"SecurityCenterFree"Vm 및 컴퓨터에서 이벤트 처리를 시작 하려면 Security center에 솔루션을 보고 / 작업 영역입니다.

2019-03-17, 기존 에이전트는 감지 하면 전에 Security Center에 구독 등록의 기존 컴퓨터에 Microsoft Monitoring Agent 확장을 설치 하지 않습니다 및 컴퓨터에는 영향을 받지 않습니다. 이러한 컴퓨터를이 컴퓨터에 에이전트 설치 문제를 해결 하는 "컴퓨터에서 에이전트 상태 문제 모니터링 해결" 권장 사항이 참조 하세요.

 자세한 내용은 다음 섹션 [SCOM 또는 OMS 직접 에이전트가 VM에 이미 설치되어 있으면 어떻게 되나요?](#scomomsinstalled)를 참조하세요.

### System Center Operations Manager (SCOM) 에이전트를 VM에 이미 설치 되어 있으면 어떻게 되나요?<a name="scomomsinstalled"></a>
기존 System Center Operations Manager 에이전트를 security center는 Microsoft Monitoring Agent 확장-side-by-side를 설치 합니다. 기존 SCOM 에이전트는 일반적으로 System Center Operations Manager 서버에 보고 계속 됩니다. System Center Operations Manager 에이전트 및 Microsoft Monitoring Agent가이 프로세스 중 최신 버전으로 업데이트 될 공용 런타임 라이브러리를 공유 하는 note 하십시오. System Center Operations Manager 2012 에이전트 버전을 설치 하면 자동으로 프로 비전 설정 하지 하는 경우 (관리 효율성 기능 손실 될 수 있습니다 System Center Operations Manager server가 버전 2012).

### <a name="what-is-the-impact-of-removing-these-extensions"></a>이러한 확장을 제거할 경우 어떤 영향이 있나요?
Microsoft Monitoring 확장을 제거하는 경우 Security Center는 VM의 보안 데이터 및 일부 보안 권장 사항을 수집할 수 없고 경고를 사용할 수 없습니다. 24시간 이내에 Security Center는 VM이 확장을 누락하고 확장을 다시 설치했는지 확인합니다.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>자동 에이전트 설치 및 작업 영역 생성을 중지하려면 어떻게 할까요?
보안 정책에서 구독에 대해 자동 프로비저닝을 해제할 수는 있지만, 해제하지 않는 것이 좋습니다. 자동 프로비저닝을 해제하면 Security Center 권장 사항 및 경고 기능이 제한됩니다. 자동 프로비저닝을 사용하지 않도록 설정하려면 다음을 수행합니다.

1. 표준 계층에 대한 구독이 구성된 경우 해당 구독에 대한 보안 정책을 열고 **체험** 계층을 선택합니다.

   ![가격 책정 계층 ][1]

2. 다음으로 **보안 정책 - 데이터 수집** 블레이드에서 **끄기**를 선택하여 자동 프로비저닝을 해제합니다.
   ![데이터 수집][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>자동 에이전트 설치 및 작업 영역 생성을 옵트아웃해야 하나요?

> [!NOTE]
> 자동 프로비전을 옵트아웃하도록 선택하는 경우 [옵트아웃의 의미는 무엇인가요?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) 및 [옵트아웃에 권장되는 단계](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)를 참조하세요.
>
>

다음에 해당하는 경우 자동 프로비전을 옵트아웃하는 것이 좋습니다.

- Security Center에 의한 자동 에이전트 설치가 전체 구독에 적용됩니다.  VM의 하위 집합에 자동 설치를 적용할 수 없습니다. Microsoft Monitoring Agent와 함께 설치할 수 없는 중요한 VM이 있는 경우 자동 프로비전을 옵트아웃해야 합니다.
- 에이전트의 버전을 업데이트 하는 Microsoft 모니터링 에이전트 (MMA) 확장을 설치 합니다. 이 직접 에이전트와 SCOM 에이전트에 적용 됩니다 (후자의 경우에 SCOM 및 MMA 공유 프로세스에서 업데이트 될 공용 런타임 라이브러리-). 설치된 SCOM 에이전트가 2012 버전이고 업그레이드된 경우 SCOM 서버도 2012 버전이면 관리 기능이 손실될 수 있습니다. 설치된 SCOM 에이전트가 2012 버전인 경우 자동 프로비전을 옵트아웃하는 것이 좋습니다.
- 구독 외부의 사용자 지정 작업 영역(중앙 집중식 작업 영역)이 있는 경우 자동 프로비전을 옵트아웃해야 합니다. Microsoft Monitoring Agent 확장을 수동으로 설치하고 Security Center가 연결을 재정의하지 않고 작업 영역에 연결할 수 있습니다.
- 구독 내에 자신의 사용자 지정 작업 영역이 있고 구독당 작업 영역을 여러 개 만들지 않으려는 경우 두 가지 옵션이 있습니다.

   1. 자동 프로비전을 옵트아웃할 수 있습니다. 마이그레이션 후 기본 작업 영역 설정을 [기존 Log Analytics 작업 영역을 사용하려면 어떻게 해야 하나요?](#how-can-i-use-my-existing-log-analytics-workspace)의 설명에 따라 설정합니다.
   2. 또는 마이그레이션이 완료되고, Microsoft Monitoring Agent가 VM에 설치되고, VM이 생성된 작업 영역에 연결되도록 허용합니다. 그런 다음 이미 설치된 에이전트를 재구성하도록 선택하여 기본 작업 영역 설정을 설정하고 자신의 사용자 지정 작업 영역을 선택합니다. 자세한 내용은 [기존 Log Analytics 작업 영역을 사용하려면 어떻게 해야 하나요?](#how-can-i-use-my-existing-log-analytics-workspace)를 참조하세요.

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>자동 프로비전을 옵트아웃하는 의미는 무엇인가요?
마이그레이션이 완료되면 Security Center는 VM에서 보안 데이터를 수집할 수 없으며 일부 보안 권장 사항과 경고를 사용할 수 없습니다. 옵트아웃하는 경우 Microsoft Monitoring Agent를 수동으로 설치해야 합니다. [옵트아웃에 권장되는 단계](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning)를 참조하세요.

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>자동 프로비전을 옵트아웃하는 경우 권장되는 단계는 무엇인가요?

Security Center가 VM의 보안 데이터를 수집하고 권장 사항 및 경고를 제공할 수 있도록 Microsoft Monitoring Agent 확장을 수동으로 설치해야 합니다. 설치 지침에 대해서는 [Windows VM용 에이전트 설치](../virtual-machines/extensions/oms-windows.md) 또는 [Linux VM용 에이전트 설치](../virtual-machines/extensions/oms-linux.md)를 참조하세요.

에이전트를 기존 사용자 지정 작업 영역이나 Security Center가 만든 작업 영역에 연결할 수 있습니다. 사용자 지정 작업 영역에 'Security' 또는 'SecurityCenterFree' 솔루션이 활성화되어 있지 않으면 솔루션을 적용해야 합니다. 적용하려면 사용자 지정 작업 영역 또는 구독을 선택하고 **보안 정책 - 가격 책정 계층** 블레이드를 통해 가격 책정 계층을 적용합니다.

   ![가격 책정 계층 ][1]

Security Center는 선택한 가격 책정 계층을 기반으로 작업 영역에 올바른 솔루션을 사용하도록 설정합니다.

### Security Center에서 설치한 OMS 확장을 제거하려면 어떻게 할까요?<a name="remove-oms"></a>
Microsoft Monitoring Agent를 수동으로 제거할 수 있습니다. Security Center 권장 사항 및 경고를 제한하기 때문에 권장되지는 않습니다.

> [!NOTE]
> 데이터 수집을 사용하는 경우 Security Center는 에이전트를 제거한 후에 다시 설치합니다.  수동으로 에이전트를 제거하기 전에 데이터 수집을 사용하지 않도록 설정해야 합니다. 데이터 수집을 사용하지 않도록 설정하는 지침은 자동 에이전트 설치 및 작업 영역 생성을 중지하려면 어떻게 할까요?를 참조하세요.
>
>

수동으로 에이전트를 제거하려면:

1.  포털에서 **Log Analytics**을 엽니다.
2.  Log Analytics 블레이드에서 작업 영역을 선택합니다.
3.  모니터링하지 않을 각 VM을 선택하고 **연결 끊기**를 선택합니다.

   ![에이전트 제거][3]

> [!NOTE]
> Linux VM에 이미 비확장 OMS 에이전트가 설치된 경우 확장명을 제거하면 에이전트도 제거되고 고객이 다시 설치해야 합니다.
>
>
### <a name="how-do-i-disable-data-collection"></a>데이터 컬렉션을 사용하지 않도록 설정하려면 어떻게 해야 하나요?
자동 프로비전은 기본적으로 해제되어 있습니다. 보안 정책에서 자동 프로비저닝 설정을 해제하여 언제든지 리소스에서 자동 프로비저닝을 사용하지 않도록 설정할 수 있습니다. 시스템 업데이트, OS 취약성 및 엔드포인트 보호에 대한 보안 경고와 권장 사항을 받으려면 자동 프로비저닝을 사용하는 것이 좋습니다.

데이터 수집을 해제하려면 [Azure Portal에 로그인](https://portal.azure.com)하여 **찾아보기**, **Security Center**, **정책 선택**을 차례로 선택합니다. 자동 프로비저닝을 사용하지 않도록 설정할 구독을 선택합니다. 구독을 선택하면 **보안 정책 - 데이터 수집**이 열립니다. **자동 프로비전**에서 **끔**을 선택합니다.

### <a name="how-do-i-enable-data-collection"></a>데이터 컬렉션을 사용하도록 설정하려면 어떻게 해야 하나요?
보안 정책에서 Azure 구독에 대한 데이터 수집을 사용하도록 설정할 수 있습니다. 데이터 수집을 사용하도록 설정하려면 [Azure Portal에 로그인](https://portal.azure.com)하여 **찾아보기**, **Security Center**, **보안 정책**을 차례로 선택합니다. 자동 프로비전을 사용할 구독을 선택합니다. 구독을 선택하면 **보안 정책 - 데이터 수집**이 열립니다. **자동 프로비전**에서 **켬**을 선택합니다.

### <a name="what-happens-when-data-collection-is-enabled"></a>데이터 수집을 사용하도록 설정하면 어떻게 될까요?
자동 프로비저닝을 사용하도록 설정하면 Security Center는 지원되는 모든 Azure VM 및 새로 만든 Azure VM에 Microsoft Monitoring Agent를 프로비전합니다. 자동 프로비저닝을 사용하는 것이 좋지만 수동 에이전트 설치도 사용할 수 있습니다. [Microsoft Monitoring Agent 확장을 설치하는 방법을 알아봅니다](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension). 

에이전트는 프로세스 생성 이벤트 4688 및 이벤트 4688 내의 *CommandLine* 필드를 활성화합니다. VM에서 생성된 새로운 프로세스는 이벤트 로그에서 기록되고 Security Center의 검색 서비스에 의해 모니터링됩니다. 각 새 프로세스에 대해 기록된 세부 정보에 대한 내용은 [4688의 설명 필드](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=4688#fields)를 참조하세요. 또한 에이전트는 VM에서 생성되는 4688 이벤트를 수집하고 검색에 저장합니다.

또한 에이전트는 [적응형 애플리케이션 제어](security-center-adaptive-application.md)에 데이터 수집을 사용하도록 설정하고, Security Center는 모든 애플리케이션을 허용하도록 감사 모드에서 로컬 AppLocker 정책을 구성합니다. 이렇게 하면 AppLocker가 이벤트를 생성하게 되고, 이 이벤트를 Security Center에서 수집하여 활용합니다. 이 정책은 이미 AppLocker 정책이 구성된 컴퓨터에서는 구성할 수 없습니다. 

Security Center가 VM에서 의심스러운 작업을 감지하면 고객은 경우 [보안 연락처 정보](security-center-provide-security-contact-details.md)가 제공된 경우 전자 메일을 통해 알림을 받습니다. 경고는 또한 Security Center의 보안 경고 대시보드에 표시됩니다.



### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>Monitoring Agent가 서버의 성능에 미치는 영향
에이전트는 시스템 리소스의 명목 양을 소비하며 성능에 거의 영향을 미치지 않습니다. 성능 영향과 에이전트 및 확장에 대한 자세한 내용은 [계획 및 작업 가이드](security-center-planning-and-operations-guide.md#data-collection-and-storage)를 참조하세요.

### <a name="where-is-my-data-stored"></a>내 데이터는 어디에 저장되나요?
이 에이전트에서 수집된 데이터는 구독 또는 새 작업 영역에 연결된 기존 Log Analytics 작업 영역 중 하나에 저장됩니다. 자세한 내용은 [데이터 보안](security-center-data-security.md)을 참조하세요.

## 기존 Azure Monitor 로그 고객<a name="existingloganalyticscust"></a>

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center에서 VM과 작업 영역 간의 모든 기존 연결을 재정의하나요?
VM에 Azure 확장으로 Microsoft Monitoring Agent가 이미 설치되어 있으면 Security Center에서 기존 작업 영역 연결을 재정의하지 않습니다. 대신 Security Center에서 기존 작업 영역을 사용합니다. "보안" 또는 "SecurityCenterFree" 솔루션에 대 한 보고 작업 영역에 설치 되어 있는 VM은 보호할 수 있습니다. 

Security Center 솔루션이 데이터 수집 화면에서 선택한 작업 영역에 설치 된 경우에 없는 및 솔루션 관련 Vm에만 적용 됩니다. 솔루션을 추가하면 기본적으로 Log Analytics 작업 영역에 연결된 모든 Windows 및 Linux 에이전트에 의해 배포됩니다. [솔루션 대상 지정](../operations-management-suite/operations-management-suite-solution-targeting.md)을 사용하면 솔루션에 범위를 적용할 수 있습니다.

Microsoft Monitoring Agent를 Azure 확장으로 설치하는 것이 아니라 VM에 직접 설치하는 경우 Security Center는 Microsoft Monitoring Agent를 설치하지 않으며, 보안 모니터링 기능이 제한됩니다.

### <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center에서 기존 Log Analytics 작업 영역에 솔루션을 설치하나요? 요금 청구에 영향을 주는 요인은 무엇인가요?
Security Center에서 VM이 만든 작업 영역에 이미 연결되어 있는지를 식별하는 경우 Security Center를 통해 가격 책정 계층에 따라 이 작업 영역에서 솔루션을 사용할 수 있습니다. 솔루션이 [솔루션 대상 지정](../operations-management-suite/operations-management-suite-solution-targeting.md)을 통해 관련 Azure VM에만 적용되므로 청구는 동일하게 유지됩니다.

- **체험 계층** – Security Center는 작업 영역에서 'SecurityCenterFree' 솔루션을 설치합니다. 체험 계층에 대한 요금이 청구되지 않습니다.
- **표준 계층** – Security Center가 작업 영역에 'Security' 솔루션을 설치합니다.

   ![기본 작업 영역의 솔루션][4]

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>내 환경에 이미 작업 영역이 있는 경우 보안 데이터를 수집하는 데 사용할 수 있나요?
VM에 Azure 확장으로 Microsoft Monitoring Agent가 이미 설치되어 있으면 Security Center에서 기존 연결된 작업 영역 연결을 사용합니다. Security Center 솔루션이 작업 영역에 없는 경우 설치되고 솔루션은 [솔루션 대상 지정](../operations-management-suite/operations-management-suite-solution-targeting.md)을 통해 관련 VM에만 적용됩니다.

Security Center에서 VM에 Microsoft Monitoring Agent를 설치하면 Security Center에서 만든 기본 작업 영역을 사용합니다.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>내 작업 영역에 보안 솔루션이 이미 있습니다. 요금 청구에 영향을 주는 요인은 무엇인가요?
보안 및 감사 솔루션은 Azure VM에 대한 Security Center 표준 계층 기능을 활성화하는 데 사용됩니다. 보안 및 감사 솔루션이 작업 영역에 이미 설치되어 있는 경우 Security Center에서는 기존 솔루션을 사용합니다. 요금 청구는 변하지 않습니다.

## <a name="using-azure-security-center"></a>Azure Security Center 사용
### <a name="what-is-a-security-policy"></a>보안 정책이란?
보안 정책은 지정된 구독 내에서 리소스에 대해 권장되는 제어 집합을 정의합니다. Azure Security Center에서 회사의 보안 요구 사항 및 애플리케이션 형식 또는 각 구독의 데이터 민감도에 따라 Azure 구독에 대한 정책을 정의합니다.

Azure Security Center에서 사용하도록 설정한 보안 정책에 따라 보안 권장 사항과 모니터링이 결정됩니다. 보안 정책에 대해 자세히 알아보려면 [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md)을 참조하세요.

### <a name="who-can-modify-a-security-policy"></a>보안 정책을 누가 수정할 수 있나요?
보안 정책을 수정하려면 해당 구독의 보안 관리자이거나 소유자 또는 참가자여야 합니다.

보안 정책을 구성하는 방법을 자세히 알아보려면 [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md)을 참조하세요.

### <a name="what-is-a-security-recommendation"></a>보안 권장 사항이란?
Azure Security Center에서는 Azure 리소스의 보안 상태를 분석합니다. 잠재적인 보안 취약성이 식별되면 권장 사항이 생성됩니다. 권장 사항은 필요한 컨트롤을 구성하는 과정을 안내합니다. 예:

* 악성 소프트웨어를 식별하여 제거하는 데 도움을 주는 맬웨어 방지 프로그램 프로비전
* 가상 머신의 트래픽을 제어하는 [네트워크 보안 그룹](../virtual-network/security-overview.md) 및 규칙
* 웹 애플리케이션의 대상을 지정한 공격에 대해 방어하는 데 도움이 되는 웹 애플리케이션 방화벽 프로비전
* 누락된 시스템 업데이트 배포
* 권장 기준과 일치하지 않는 OS 구성 해결

보안 정책에 사용하도록 설정된 권장 사항만 여기에 표시됩니다.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>내 Azure 리소스의 현재 보안 상태를 확인하려면 어떻게 해야 하나요?
**Security Center 개요** 블레이드는 Compute, Networking, Storage &amp; 데이터 및 애플리케이션별로 분류된 환경의 전반적인 보안 상태를 보여 줍니다. 각 리소스 종류에는 잠재적 보안 취약성이 식별되었는지 나타내는 표시기가 있습니다. 각 타일을 클릭하면 구독의 리소스 인벤토리와 함께 Security Center에서 식별하는 보안 문제 목록이 표시됩니다.

### <a name="what-triggers-a-security-alert"></a>보안 경고를 트리거하는 것은 무엇인가요?
Azure Security Center는 리소스, 네트워크 및 맬웨어 방지 프로그램과 방화벽 같은 파트너 솔루션에서 자동으로 로그 데이터를 수집, 분석 및 결합합니다. 위협이 감지되었을 때 보안 경고가 생성됩니다. 감지되는 사항의 예:

* 알려진 악성 IP 주소와 통신하는 손상된 가상 머신
* Windows 오류 보고를 사용 하여 감지된 고급 맬웨어
* 가상 머신에 대한 무작위 공격
* 맬웨어 방지 프로그램 또는 웹 애플리케이션 방화벽 등과 같은 통합된 파트너 보안 솔루션에서의 보안 경고

### 왜 점수 값 변경 보안 않았습니다? <a name="secure-score-faq"></a>
2019 년 2 월을 기준으로 Security Center의 심각도 따라 하기 위해 몇 가지 권장 사항, 점수를 조정 합니다. 이러한 조정의 결과로 있을 점수 값을 보호 하는 전체에서 변경 합니다.  보안 점수에 대 한 자세한 내용은 참조 하세요. [점수를 계산할 보안](security-center-secure-score.md)합니다.

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Microsoft 보안 응답 센터와 Azure Security Center에서 감지 및 경고된 위협 간의 차이점은 무엇입니까?
Microsoft 보안 대응 센터(MSRC)는 Azure 네트워크 및 인프라의 선택 보안 모니터링을 수행하고 타사에서 위협 인텔리전스 및 남용 불만 사항을 받습니다. MSRC는 불법적인 또는 권한 없는 당사자가 고객 데이터에 액세스했거나 고객의 Azure 사용이 사용 제한에 대한 조건을 준수하지 않는 것을 인식하면 보안 사고 관리자는 고객에게 알립니다. 보안 연락처를 지정하지 않은 경우 대개 Azure Security Center에 지정된 보안 연락처 또는 Azure 구독 소유자에게 메일을 전송하는 방식으로 알림이 수행됩니다.

보안 센터는 지속적으로 고객의 Azure 환경을 모니터링하고 다양한 잠재적인 악의적 활동을 자동으로 검색하도록 분석을 적용하는 Azure 서비스입니다. 이러한 감지는 보안 센터 대시보드에서 보안 경고로 표시됩니다.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Azure Security Center에서는 어떤 Azure 리소스를 모니터링하나요?
Azure Security Center에서는 다음과 같은 Azure 리소스를 모니터링합니다.

* 가상 머신(VM)( [Cloud Services](../cloud-services/cloud-services-choose-me.md)포함)
* 가상 머신 확장 집합 (VMSSs)
* Azure Virtual Networks
* Azure SQL 서비스
* Azure Storage 계정
* Azure Web Apps([App Service Environment](../app-service/environment/intro.md))
* VM 및 App Service Environment에서 웹 애플리케이션 방화벽 같이 Azure 구독과 통합된 파트너 솔루션

또한 온-프레미스) (포함 비 Azure 컴퓨터도 모니터링할 수 있습니다 Azure Security Center에서 (둘 다 [Windows 컴퓨터](./quick-onboard-windows-computer.md) 하 고 [Linux 컴퓨터](./quick-onboard-linux-computer.md) 지)

## <a name="virtual-machines"></a>Virtual Machines
### <a name="what-types-of-virtual-machines-are-supported"></a>어떤 유형의 가상 머신이 지원되나요?
[클래식 및 Resource Manager 배포 모델](../azure-classic-rm.md)을 모두 사용하여 작성된 VM(가상 머신)에 대해 모니터링 및 권장 사항이 제공됩니다.

지원되는 플랫폼 목록은 [Azure Security Center에서 지원되는 플랫폼](security-center-os-coverage.md)을 참조하세요.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Azure Security Center가 내 Azure VM에서 실행 중인 맬웨어 방지 솔루션을 인식하지 못하는 이유는 무엇인가요?
Azure Security Center는 Azure 확장을 통해 설치된 맬웨어 방지 프로그램을 확인할 수 있습니다. 예를 들어 보안 센터는 사용자가 제공한 이미지에 미리 설치되어 있거나, 구성 관리 시스템 등의 고유한 프로세스를 사용하여 가상 머신에 설치한 맬웨어 방지 프로그램을 검색할 수 없습니다.

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>VM에 대해 "검사 데이터 누락" 메시지가 표시되는 이유는 무엇인가요?
VM에 대한 검색 데이터가 없는 경우 이 메시지가 표시됩니다. Azure Security Center에서 데이터 수집을 사용하도록 설정한 후 검사 데이터가 입력될 때까지는 다소 시간이 걸릴 수 있습니다(1시간 이내). 검색 데이터를 처음 입력한 후에는 검색 데이터가 전혀 없거나 최근 검색 데이터가 없기 때문에 이 메시지가 나타날 수 있습니다. VM이 중지된 상태이면 검사를 수행해도 데이터가 입력되지 않습니다. Windows 에이전트의 보존 정책에 따라 최근에(기본값은 30일) 검색 데이터가 입력되지 않은 경우에도 이 메시지가 나타날 수 있습니다.

### <a name="how-often-does-security-center-scan-for-operating-system-vulnerabilities-system-updates-and-endpoint-protection-issues"></a>Security Center는 운영 체제 취약점, 시스템 업데이트 및 Endpoint Protection 문제를 얼마나 자주 검사합니까?
Security Center에서 취약점, 업데이트 및 문제를 검색하는 대기 시간은 다음과 같습니다.

- 운영 체제 보안 구성 - 데이터가 48시간 이내 업데이트됩니다.
- 시스템 업데이트 – 데이터가 24시간 이내 업데이트됩니다.
- Endpoint Protection 문제 – 데이터가 8시간 이내 업데이트됩니다.

Security Center는 일반적으로 한 시간마다 새 데이터를 검사하며 그에 따라 권장 사항을 새로 고칩니다. 

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>"VM 에이전트가 누락됨" 메시지가 표시되는 이유는 무엇인가요?
데이터 수집을 사용하도록 설정하려면 VM에 VM 에이전트를 설치해야 합니다. Azure Marketplace에서 배포된 VM에 VM 에이전트가 기본적으로 설치됩니다. 다른 VM에 VM 에이전트를 설치하는 방법에 대한 자세한 내용은 블로그 게시물 [VM 에이전트 및 확장](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)을 참조하세요.


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
