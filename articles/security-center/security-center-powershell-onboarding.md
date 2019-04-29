---
title: PowerShell을 사용하여 Azure Security Center를 온보딩 및 네트워크 보호 | Microsoft Docs
description: 이 문서에서는 PowerShell cmdlet을 사용하여 Azure Security Center 온보딩 프로세스를 단계별로 안내합니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e400fcbf-f0a8-4e10-b571-5a0d0c3d0c67
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/2/2018
ms.author: rkarlin
ms.openlocfilehash: 73043680ea7b8b63a329d0a457449b635b7b80f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703594"
---
# <a name="automate-onboarding-of-azure-security-center-using-powershell"></a>PowerShell을 사용하여 Azure Security Center 온보딩 자동화

Azure Security Center PowerShell 모듈을 사용하여 Azure 워크로드를 프로그래밍 방식으로 보호할 수 있습니다.
PowerShell을 사용하면 작업을 자동화하고 수동 작업에 내재된 사용자 오류를 피할 수 있습니다. 이는 특히 모두 처음부터 보호해야 하는 수십만 개의 리소스가 있는 수십 개의 구독이 포함된 대규모 배포에 유용합니다.

PowerShell을 사용하여 Azure Security Center를 온보딩하면 Azure 리소스의 온보딩 및 관리를 프로그래밍 방식으로 자동화하고 필요한 보안 컨트롤을 추가할 수 있습니다.

이 문서에서는 전체 구독에서 Security Center를 배포하기 위해 환경에서 수정 및 사용될 수 있는 샘플 PowerShell 스크립트를 제공합니다. 

이 예제에서는 ID가 d07c0080-170c-4c24-861d-9c817742786c인 구독에서 Security Center를 사용하도록 설정하고 고급 위협 보호 및 검색 기능을 제공하는 Security Center의 표준 계층을 구현하여 높은 수준의 보호를 제공합니다.

1. [보호의 ASC 표준 수준](https://azure.microsoft.com/pricing/details/security-center/)을 설정합니다. 
 
2. Microsoft Monitoring Agent가 구독과 연결된 VM에서 수집하는 데이터를 보낼 Log Analytics 작업 영역을 설정합니다(이 예제에서는 기존 사용자 정의 작업 영역(myWorkspace)).

3. [Microsoft Monitoring Agent를 배포](security-center-enable-data-collection.md#auto-provision-mma)하는 Security Center의 자동 에이전트 프로비저닝을 활성화합니다.

5. 조직의 [CISO를 ASC 경고 및 주요 이벤트의 보안 연락처](security-center-provide-security-contact-details.md)로 설정합니다.

6. Azure Security Center의 [기본 보안 정책](tutorial-security-policy.md)을 할당합니다.

## <a name="prerequisites"></a>필수 조건

다음 단계는 Security Center cmdlet을 실행하기 전에 수행해야 합니다.

1.  관리자 권한으로 PowerShell을 실행합니다.
2.  PowerShell에서 다음 명령을 실행합니다.
      
        Set-ExecutionPolicy -ExecutionPolicy AllSigned
        Install-Module -Name Az.Security -Force

## <a name="onboard-security-center-using-powershell"></a>PowerShell을 사용하여 Security Center 온보딩

1.  Security Center 리소스 공급자에 구독을 등록합니다.

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security' 

2.  선택 사항: 구독의 적용 범위 수준(가격 책정 계층)을 설정합니다(정의되지 않은 경우 가격 책정 계층이 무료로 설정됨).

        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
        Set-AzSecurityPricing -Name "default" -PricingTier "Standard"

3.  에이전트가 보고할 Log Analytics 작업 영역을 구성합니다. 구독의 VM이 보고할 기존에 만든 Log Analytics 작업 영역이 있어야 합니다. 동일한 작업 영역에 보고할 여러 구독을 정의할 수 있습니다. 정의되지 않은 경우 기본 작업 영역이 사용됩니다.

        Set-AzSecurityWorkspaceSetting -Name "default" -Scope
        "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"

4.  Azure VM에서 Microsoft Monitoring Agent 설치를 자동 프로비전합니다.
    
        Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    
        Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision

    > [!NOTE]
    > 자동 프로비저닝을 사용하도록 설정하여 Azure Virtual Machines가 Azure Security Center를 통해 자동으로 보호되도록 하는 것이 좋습니다.
    >

5.  선택 사항: Security Center에서 생성한 경고 및 알림의 수신자로 사용될, 온보딩하는 구독의 보안 연락처 세부 정보를 정의하는 것이 좋습니다.

        Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -Phone "2142754038" -AlertAdmin -NotifyOnAlert 

6.  기본 Security Center 정책 이니셔티브를 할당합니다.

        Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
        $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ '[Preview]: Enable Monitoring in Azure Security Center'}
        New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Security Center Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'

이제 PowerShell을 사용하여 Azure Security Center를 온보딩했습니다.

이제 이러한 PowerShell cmdlet을 자동화 스크립트와 함께 사용하여 구독 및 리소스를 프로그래밍 방식으로 반복할 수 있습니다. 이렇게 하면 시간이 절약되고 사용자 오류 가능성이 줄어듭니다. 이 [샘플 스크립트](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1)를 참조로 사용할 수 있습니다.






## <a name="see-also"></a>참고 항목
PowerShell을 사용하여 Security Center에 대한 온보딩을 자동화하는 방법을 자세히 알아보려면 다음 문서를 참조하세요.

* [Az.Security](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Security/Commands.Security/help/Az.Security.md).

Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
