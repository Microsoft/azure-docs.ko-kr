---
title: 독립 실행형 Azure Automation 계정 만들기
description: 이 문서는 Azure Automation에서 예제 보안 주체 인증을 만들고 테스트하며 사용하는 과정을 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cdffc339bee1f5456e4eeb619e566b1f9c34b143
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61076796"
---
# <a name="create-a-standalone-azure-automation-account"></a>독립 실행형 Azure Automation 계정 만들기

이 문서에서는 Azure Portal에서 Azure Automation 계정을 만드는 방법을 보여줍니다. 평가 하 여 Azure Monitor 로그를 사용 하 여 추가 관리 솔루션 또는 통합을 사용 하지 않고 Automation에 알아보세요 포털 Automation 계정을 사용할 수 있습니다. 이러한 관리 솔루션 추가 수도 있고 고급 모니터링에 언제 든 지 runbook 작업의 나중에 Azure Monitor 로그를 통합할 수 있습니다.

Automation 계정에서는 Azure Resource Manager 또는 클래식 배포 모델에서 리소스를 관리하여 Runbook을 인증할 수 있습니다. 하나의 Automation 계정으로 해당 테넌트의 모든 영역과 구독 전반의 리소스를 관리할 수 있습니다.

Azure Portal에서 Automation 계정을 만드는 경우 이러한 계정이 자동으로 생성됩니다.

* **실행 계정** 이 계정에서 수행하는 작업은 다음과 같습니다.
  * Azure AD(Azure Active Directory)에서 서비스 주체 만들기
  * 인증서 만들기
  * Runbook을 사용하여 Azure Resource Manager 리소스를 관리하는 참가자 RBAC(역할 기반 액세스 제어)를 할당합니다.
* **클래식 실행 계정** 이 계정은 관리 인증서를 업로드합니다. 인증서는 Runbook을 사용하여 클래식 리소스를 관리합니다.

이러한 계정을 만들면 Runbook을 신속하게 빌드하고 배포하기 시작하여 자동화 요구 사항을 지원할 수 있습니다.

## <a name="permissions-required-to-create-an-automation-account"></a>Automation 계정을 만드는 데 필요한 사용 권한

Automation 계정을 만들거나 업데이트하고 이 문서에서 설명한 작업을 완료하려면 다음과 같은 권한이 있어야 합니다.

* Automation 계정을 만들려면 Azure AD 사용자 계정을 **Microsoft. Automation** 리소스에 대한 소유자 역할과 동일한 권한이 있는 역할에 추가해야 합니다. 자세한 내용은 [Azure Automation의 역할 기반 액세스 제어](automation-role-based-access-control.md)를 참조하세요.
* Azure Portal의 **Azure Active Directory** > **관리** > **앱 등록**에서 **앱 등록**이 **예**로 설정된 경우, Azure AD 테넌트에서 관리자가 아닌 사용자는 [Active Directory 애플리케이션을 등록](../active-directory/develop/howto-create-service-principal-portal.md#check-azure-subscription-permissions)할 수 있습니다. **앱 등록 설정**이 **아니요**로 설정된 경우, 이 작업을 수행하는 사용자는 Azure AD의 전역 관리자여야 합니다.

사용자는 구독의 전역 관리자/공동 관리자 역할에 추가되기 전에 해당 구독의 Active Directory 인스턴스에 속한 멤버가 아닌 경우 Active Directory에 게스트로 추가됩니다. 이 시나리오에서는 **Automation 계정 추가** 페이지에 “만들 수 있는 권한이 없습니다.”라는 메시지가 표시됩니다.

사용자가 글로벌 관리자/공동 관리자 역할에 먼저 추가된 경우, 구독의 Active Directory 인스턴스에서 제거한 다음, Active Directory의 전체 사용자 역할에 다시 추가할 수 있습니다.

사용자 역할을 확인하려면 다음을 수행합니다.

1. Azure Portal에서 **Azure Active Directory** 창으로 이동합니다.
1. **사용자 및 그룹**을 선택합니다.
1. **모든 사용자**를 선택합니다.
1. 특정 사용자를 선택한 후에 **프로필**을 선택합니다. 사용자 프로필에서 **사용자 형식** 특성 값은 **게스트**가 아니어야 합니다.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Azure Portal에서 새 Automation 계정 만들기

Azure Portal에서 Azure Automation 계정을 만들려면 다음 단계를 완료합니다.

1. 구독 관리자 역할의 멤버 및 구독의 공동 관리자인 계정으로 Azure Portal에 로그인합니다.
1. **+ 리소스 만들기**를 선택합니다.
1. **Automation**을 검색합니다. 검색 결과에서 **Automation**을 선택합니다.

   ![Azure Marketplace에서 Automation & Control 검색 및 선택](media/automation-create-standalone-account/automation-marketplace-select-create-automationacct.png)

1. 다음 화면에서 **만들기**를 선택합니다.

   ![Automation 계정 추가](media/automation-create-standalone-account/automation-create-automationacct-properties.png)

   > [!NOTE]
   > **Automation 계정 추가** 창에서 다음과 같은 경고가 표시되는 경우 사용자의 계정은 구독 관리자 역할의 구성원 및 구독의 공동 관리자가 아닙니다.
   >
   > ![Automation 계정 경고 추가](media/automation-create-standalone-account/create-account-without-perms.png)

1. **Automation 계정 추가** 창의 **이름** 상자에 새 Automation 계정의 이름을 입력합니다. 이 이름은 선택 후 변경할 수 없습니다. *Automation 계정 이름은 하위 지역 및 리소스 그룹별로 고유합니다. 삭제된 Automation 계정에 대한 이름을 즉시 사용할 수 있습니다.*
1. 구독이 하나 이상인 경우는 **구독** 상자에서 새 계정에 사용하려는 구독을 지정합니다.
1. **리소스 그룹**에서 기존 또는 새 리소스 그룹을 입력하거나 선택합니다.
1. **위치**에서 Azure 데이터 센터 위치를 선택합니다.
1. **Azure 실행 계정 만들기** 옵션에서 **예**를 선택한 다음 **만들기**를 선택하도록 합니다.

   > [!NOTE]
   > **Azure 실행 계정 만들기**에서 **아니요**를 선택하여 실행 계정을 만들지 않는 경우 **Automation 계정 추가** 창에 메시지가 표시됩니다. Azure Portal에서 계정이 생성되지만 계정에는 클래식 모델 구독 또는 Azure Resource Manager 구독 디렉터리 서비스에서 해당하는 인증 ID가 없습니다. 따라서 Automation 계정에는 구독의 리소스에 대한 액세스 권한도 없습니다. 이렇게 하면 이 계정을 참조하는 Runbook이 그러한 배포 모델의 리소스에 대해 작업을 인증하고 수행하지 못하도록 방지합니다.
   >
   > ![Automation 계정 경고 추가](media/automation-create-standalone-account/create-account-decline-create-runas-msg.png)
   >
   > 서비스 주체가 생성되지 않은 경우 참여자 역할은 할당되지 않습니다.
   >

1. Automation 계정을 생성하는 진행률을 추적하려면 메뉴에서 **알림**을 선택합니다.

### <a name="resources-included"></a>포함된 리소스

Automation 계정이 성공적으로 만들어지면 몇 가지 리소스가 자동으로 만들어집니다. 이러한 Runbook을 만든 후 유지하지 않으려는 경우 안전하게 삭제할 수 있습니다. 실행 계정은 Runbook에서 사용자 계정으로 인증하는 데 사용할 수 있으며 다른 실행 계정을 만들거나 필요 없는 상황이 아니라면 남겨두는 것이 좋습니다. 다음 표에는 실행 계정에 대한 리소스가 요약되어 있습니다.

| 리소스 | 설명 |
| --- | --- |
| AzureAutomationTutorial Runbook |실행 계정을 사용하여 인증하는 방법을 보여주는 예제 그래픽 Runbook입니다. Runbook은 Resource Manager 리소스를 모두 가져옵니다. |
| AzureAutomationTutorialScript Runbook |실행 계정을 사용하여 인증하는 방법을 보여주는 예제 PowerShell Runbook입니다. Runbook은 Resource Manager 리소스를 모두 가져옵니다. |
| AzureAutomationTutorialPython2 Runbook |실행 계정을 사용하여 인증하는 방법을 보여주는 예제 Python Runbook입니다. Runbook은 구독에 있는 모든 리소스 그룹을 나열합니다. |
| AzureRunAsCertificate |Automation 계정을 만드는 경우 또는 기존 계정에 대해 PowerShell 스크립트를 사용하여 자동으로 만들어지는 인증서 자산입니다. 인증서는 Runbook에서 Azure Resource Manager 리소스를 관리할 수 있도록 Azure로 인증할 수 있습니다. 이 인증서는 수명이 1년입니다. |
| AzureRunAsConnection |Automation 계정을 만드는 경우 또는 기존 계정에 대해 PowerShell 스크립트를 사용하여 자동으로 만들어지는 연결 자산입니다. |

다음 표에는 클래식 실행 계정에 대한 리소스가 요약되어 있습니다.

| 리소스 | 설명 |
| --- | --- |
| AzureClassicAutomationTutorial Runbook |예제 그래픽 Runbook Runbook은 클래식 실행 계정(인증서)을 사용하여 구독에 있는 모든 클래식 VM을 가져옵니다. 그런 다음 VM 이름 및 상태가 표시됩니다. |
| AzureClassicAutomationTutorial Script Runbook |예제 PowerShell Runbook Runbook은 클래식 실행 계정(인증서)을 사용하여 구독에 있는 모든 클래식 VM을 가져옵니다. 그런 다음 VM 이름 및 상태가 표시됩니다. |
| AzureClassicRunAsCertificate |자동으로 만들어지는 인증서 자산입니다. 인증서는 Runbook에서 Azure 클래식 리소스를 관리할 수 있도록 Azure로 인증할 수 있습니다. 이 인증서는 수명이 1년입니다. |
| AzureClassicRunAsConnection |자동으로 만들어지는 연결 자산입니다. 자산은 Runbook에서 Azure 클래식 리소스를 관리할 수 있도록 Azure로 인증할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

* 그래픽 작성에 대해 자세히 알아보려면 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)을 참조하세요.
* PowerShell Runbook을 시작하려면 [내 첫 번째 PowerShell Runbook](automation-first-runbook-textual-powershell.md)을 참조하세요.
* PowerShell 워크플로 Runbook을 시작하려면 [내 첫 번째 PowerShell 워크플로 Runbook](automation-first-runbook-textual.md)을 참조하세요.
* Python2 Runbook을 시작하려면 [내 첫 번째 Python2 Runbook](automation-first-runbook-textual-python2.md)을 참조하세요.

