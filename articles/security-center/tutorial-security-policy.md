---
title: 보안 정책 작업 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 보안 정책을 사용하는 방법을 설명합니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/28/2019
ms.author: monhaber
ms.openlocfilehash: 1931026869e930caef2ff2f92fb85dade15a9c8c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111513"
---
# <a name="working-with-security-policies"></a>보안 정책 작업

이 문서에서는 보안 정책을 구성하는 방법 및 Security Center에서 보안 정책을 보는 방법을 설명합니다. Azure Security Center에서는 등록되는 각 구독에 자동으로 해당 [기본 제공 보안 정책](security-center-policy-definitions.md)을 할당합니다. 관리 그룹 및 여러 구독에서 정책을 설정할 수도 있는 [Azure Policy](../governance/policy/overview.md)에서 보안 정책을 구성할 수 있습니다.

PowerShell을 사용하여 정책을 설정하는 방법에 대한 지침은 [빠른 시작: Azure PowerShell 모듈을 사용하여 비준수 리소스를 식별하는 정책 할당 만들기](../governance/policy/assign-policy-powershell.md)를 참조하세요.

>[!NOTE]
> Security Center와 Azure Policy의 통합이 시작되었습니다. 기존 고객의 경우 Security Center의 이전 보안 정책이 더 이상 사용되지 않으며 새로운 기본 제공 이니셔티브로 자동 마이그레이션됩니다. 새로운 이니셔티브가 Azure Policy에 포함되어 있다는 점을 제외하면 이러한 변경은 리소스나 환경에 영향을 주지 않습니다.

## <a name="what-are-security-policies"></a>보안 정책이란?
보안 정책은 워크로드에서 원하는 구성을 정의하고 회사 또는 규정 보안 요구 사항을 준수하는 데 도움이 됩니다. Azure Policy에서 Azure 구독에 대한 정책을 정의하고 워크로드의 유형 또는 데이터의 민감도에 맞게 정책을 조정할 수 있습니다. 예를 들어 개인 식별 정보와 같이 규제된 데이터를 사용하는 애플리케이션은 다른 작업보다 높은 수준의 보안이 필요할 수 있습니다. 여러 구독이나 관리 그룹에 걸쳐 정책을 설정하려면 [Azure Policy](../governance/policy/overview.md)에서 정책을 설정합니다.

보안 정책에 따라 Azure Security Center에서 얻는 보안 권장 사항이 결정됩니다. 잠재적인 취약성을 식별하고 위협을 완화하는 데 도움이 되려면 보안 권장 사항 규정 준수를 모니터링할 수 있습니다. 적절한 옵션을 결정하는 방법에 대한 자세한 내용은 [기본 제공 보안 정책](security-center-policy-definitions.md) 목록을 참조하세요.

Security Center를 사용하도록 설정하면 Security Center의 기본 제공 보안 정책이 Security Center 범주의 기본 제공 이니셔티브로 Azure Policy에 반영됩니다. 기본 제공 이니셔티브는 Security Center에 등록된 모든 구독(무료 또는 표준 계층)에 자동으로 할당됩니다. 기본 제공 이니셔티브에는 감사 정책만 포함되어 있습니다.


### <a name="management-groups"></a>관리 그룹
조직에 구독이 많으면 구독에 대한 액세스, 정책 및 준수를 효율적으로 관리하는 방법이 필요할 수 있습니다. Azure 관리 그룹은 구독 상위 수준의 범위를 제공합니다. "관리 그룹"이라는 컨테이너에 구독을 구성하고 거버넌스 정책을 관리 그룹에 적용합니다. 관리 그룹에 속하는 모든 구독은 관리 그룹에 적용되는 정책을 자동으로 상속합니다. 각 디렉터리에는 "루트" 관리 그룹이라고 하는 단일 최상위 관리 그룹이 부여됩니다. 이 루트 관리 그룹은 모든 관리 그룹과 구독이 루트 관리 그룹까지 접히도록 만들어집니다. 이 루트 관리 그룹은 전역 정책 및 RBAC 할당을 디렉터리 수준에서 적용하는 것을 허용합니다. Azure Security Center에서 사용하기 위해 관리 그룹을 설정하려면 [Azure Security Center에 대한 테넌트 수준 표시 유형 얻기](security-center-management-groups.md)의 지침을 따릅니다.

> [!NOTE]
> 관리 그룹 및 구독의 계층 구조를 이해하는 것은 중요합니다. 관리 그룹, 루트 관리 및 관리 그룹 액세스에 대해 자세히 알아보려면 [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/index.md#root-management-group-for-each-directory)을 참조하세요.
>

## <a name="how-security-policies-work"></a>보안 정책 작동 방법
Security Center는 각 Azure 구독에 대한 기본 보안 정책을 자동으로 만듭니다. Azure Policy에서 정책을 편집하여 다음을 수행할 수 있습니다.
- 새 정책 정의를 만듭니다.
- 전체 조직 또는 조직 내 비즈니스 단위를 나타낼 수 있는 관리 그룹 및 구독에 정책을 할당합니다.
- 정책 준수를 모니터링합니다.

Azure Policy에 대한 자세한 내용은 [규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)를 참조하세요.

Azure 정책은 다음 구성 요소로 구성됩니다.

- **정책**은 규칙입니다.
- **이니셔티브**는 정책 컬렉션입니다.
- **할당**은 특정 범위(관리 그룹, 구독 또는 리소스 그룹)에 이니셔티브 또는 정책을 적용하는 것입니다.

## <a name="view-security-policies"></a>보안 정책 보기

Security Center에서 보안 정책을 보려면

1. **Security Center** 대시보드에서 **보안 정책**을 선택합니다.

    ![정책 관리 창](./media/security-center-policies/security-center-policy-mgt.png)

   **정책 관리** 화면에서 관리 그룹 구조뿐만 아니라 관리 그룹, 구독 및 작업 영역 수를 확인할 수 있습니다.

   > [!NOTE]
   > - Security Center 대시보드는 **정책 관리** 아래에 표시된 구독 수보다 **구독 적용 범위** 아래에 더 많은 구독 수를 표시할 수 있습니다. 구독 적용 범위는 표준, 무료 및 "포함 안 된" 구독 수를 표시합니다. "포함 안 된" 구독은 Security Center를 사용하도록 설정하지 않고 **정책 관리** 아래에 표시되지도 않습니다.
   >

   테이블의 열은 다음과 같이 표시됩니다.

   - **정책 이니셔티브 할당** – 구독 또는 관리 그룹에 할당되는 Security Center [기본 제공 정책](security-center-policy-definitions.md) 및 이니셔티브입니다.
   - **적용 범위** – 관리 그룹, 구독 또는 작업 영역이 실행되고 있는 무료 또는 평균의 가격 책정 계층을 식별합니다.  Security Center의 가격 책정 계층에 대해 자세히 알아보려면 [가격 책정](security-center-pricing.md)을 참조하세요.
   - **설정** - 구독에는 **설정 편집** 링크가 있습니다. **설정 편집**을 선택하면 각 구독 또는 관리 그룹에 대한 [Security Center 설정](security-center-policies-overview.md)을 업데이트할 수 있습니다.
   - **보안 점수** - 워크로드 보안 태세의 안정성 척도를 제공하는 [보안 점수](security-center-secure-score.md)를 활용하면 보안 개선을 위한 권장 사항의 우선 순위를 지정할 수 있습니다.

2. 확인하려는 구독 또는 관리 그룹의 정책을 선택합니다.

   - **보안 정책** 화면에는 선택한 구독 또는 관리 그룹에 할당된 정책에서 수행된 동작이 표시됩니다.
   - 맨 위에서 구독 또는 관리 그룹에 적용된 각 정책 **할당**을 열기 위해 제공된 링크를 사용합니다. 해당 링크를 사용하여 할당에 액세스하고 정책을 편집하거나 사용하지 않도록 설정할 수 있습니다. 예를 들어 특정 정책 할당이 엔드포인트 보호를 효과적으로 거부하는지 확인하는 경우 해당 링크를 사용하여 정책에 액세스하여 편집하거나 사용하지 않도록 설정할 수 있습니다.
   - 정책 목록에서 구독 또는 관리 그룹에 정책을 효과적으로 적용하는 것을 확인할 수 있습니다. 즉, 범위에 적용되는 각 정책의 설정이 고려되고 정책에서 수행되는 작업의 누적 결과가 제공됩니다. 예를 들어 하나의 할당에서 정책이 사용하지 않도록 설정되지만 다른 할당에서 정책이 AuditIfNotExist로 설정되는 경우 누적 효과는 AuditIfNotExist로 적용됩니다. 더 활성화된 효과가 항상 우선합니다.
   - 정책의 효과는 Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled일 수 있습니다. 효과를 적용하는 방법에 대한 자세한 내용은 [Policy 효과 이해](../governance/policy/concepts/effects.md)를 참조하세요.

   ![정책 화면](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> - 할당된 정책을 보는 경우 여러 할당을 확인할 수 있으며 각 할당이 자체적으로 구성되는 방법도 확인할 수 있습니다.

## <a name="edit-security-policies"></a>보안 정책 편집
[Azure Policy](../governance/policy/tutorials/create-and-manage.md)에서 각 Azure 구독 및 관리 그룹에 대한 기본 보안 정책을 편집할 수 있습니다. 보안 정책을 수정하려면 구독 또는 포함된 관리 그룹의 소유자, 참가자 또는 보안 관리자여야 합니다.

Azure Policy에서 보안 정책을 편집하는 방법에 대한 지침은 [규정 준수를 적용하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)를 참조하세요.

REST API를 통해 또는 Windows PowerShell을 사용하여 Azure Policy 포털을 통해 보안 정책을 편집할 수 있습니다. 다음 예제는 REST API를 사용하여 편집하는 지침을 제공합니다.


## <a name="disable-security-policies"></a>보안 정책을 사용 하지 않도록 설정
기본 보안 정책 환경에 관련 되지 않은 권장을 생성 하는 경우에 권장을 전송 하는 정책 정의 사용 하지 않도록 설정 하 여 중지할 수 있습니다.
권장 사항에 대 한 자세한 내용은 참조 [보안 권장 사항 관리](security-center-recommendations.md)합니다.

1. Security Center에서에서 합니다 **정책 및 규정 준수** 섹션에서 클릭 **보안 정책**합니다.

   ![정책 관리](./media/tutorial-security-policy/policy-management.png)

2. 권장 사항을 사용 하지 않도록 설정 하려는 구독 또는 관리 그룹을 클릭 합니다.

1. 할당된 정책을 클릭 합니다.

   ![정책 사용 안 함](./media/tutorial-security-policy/security-policy.png)

1. 에 **매개 변수** 섹션에서 검색을 사용 하지 않으려면 원하는 것이 좋습니다를 호출 하는 정책에 대 한 드롭다운 목록에서 선택 **사용 안 함**

   ![정책 사용 안 함](./media/tutorial-security-policy/disable-policy.png)
1. **저장**을 클릭합니다.
   > [!Note]
   > 사용 안 함 정책 변경이 적용 되려면 최대 12 시간이 걸릴 수 있습니다.


### <a name="configure-a-security-policy-using-the-rest-api"></a>REST API를 사용하여 보안 정책 구성

Azure Policy와 네이티브 통합의 일환으로, Azure Security Center를 사용하면 Azure Policy의 REST API를 활용하여 정책 할당을 생성할 수 있습니다. 다음 지침은 기존 할당에 대한 사용자 지정은 물론 정책 할당 생성 과정을 안내합니다. 

Azure Policy의 중요 개념: 

-  **정책 정의** 는 규칙입니다. 

-  **이니셔티브** 는 정책 정의 컬렉션입니다(규칙). 

-  **할당** 은 특정 범위(관리 그룹, 구독 등)에 이니셔티브 또는 정책을 적용하는 것입니다. 

Security Center에는 모든 보안 정책을 포함하는 기본 제공 이니셔티브가 있습니다. Azure 리소스에 대한 Security Center의 정책을 평가하려면 평가하려는 관리 그룹 또는 구독에 대한 할당을 만들어야 합니다.  

기본 제공 이니셔티브에는 모든 Security Center의 정책이 기본적으로 사용하도록 설정되어 있습니다. 기본 제공 이니셔티브에서 특정 정책을 사용하지 않도록 선택할 수 있습니다. 예를 들어 정책의 효과 매개 변수 값을 **사용 안 함**로 변경하면, **웹 애플리케이션 방화벽**을 제외한 모든 Security Center의 정책을 적용할 수 있습니다. 

### <a name="api-examples"></a>API 예제

다음 예제에서 다음 변수를 바꿉니다.

- **{scope}** - 정책을 적용할 관리 그룹 또는 구독의 이름을 입력합니다.
- **{policyAssignmentName}** - [관련 정책 할당의 이름](#policy-names)을 입력합니다.
- **{name}** 이름을 입력하거나 정책 변경을 승인한 관리자의 이름을 입력합니다.

이 예제는 구독 또는 관리 그룹에 기본 제공 Security Center 이니셔티브를 할당하는 방법을 보여줍니다.
 
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 

이 예제는 다음 정책을 사용하지 않도록 설정하고, 기본 제공 Security Center 이니셔티브를 구독에 할당하는 방법을 보여줍니다. 

- 시스템 업데이트(“systemUpdatesMonitoringEffect”) 

- 보안 구성("systemConfigurationsMonitoringEffect") 

- 엔드포인트 보호("endpointProtectionMonitoringEffect") 


    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    요청 본문 (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName": "Azure Security Center에서 모니터링 사용", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 

이 예제는 할당을 제거하는 방법을 보여줍니다.

    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 


### 정책 이름 참조 <a name="policy-names"></a>

|Security Center의 정책 이름|Azure Policy에 표시되는 정책 이름 |정책 효과 매개 변수 이름|
|----|----|----|
|SQL 암호화 |Azure Security Center에서 암호화되지 않은 SQL 데이터베이스 모니터링 |sqlEncryptionMonitoringEffect| 
|SQL 감사 |Azure Security Center에서 감사되지 않은 SQL 데이터베이스 모니터링 |sqlAuditingMonitoringEffect|
|시스템 업데이트 |Azure Security Center에서 누락된 시스템 업데이트 모니터링 |systemUpdatesMonitoringEffect|
|저장소 암호화 |스토리지 계정에 대해 누락된 Blob 암호화 감사 |storageEncryptionMonitoringEffect|
|JIT 네트워크 액세스 |Azure Security Center에서 가능한 네트워크 JIT(Just-In-time) 액세스 모니터링 |jitNetworkAccessMonitoringEffect |
|적응형 애플리케이션 제어 |Azure Security Center에서 가능한 앱 허용 목록 모니터링 |adaptiveApplicationControlsMonitoringEffect|
|네트워크 보안 그룹 |Azure Security Center에서 관대한 네트워크 액세스 모니터링 |networkSecurityGroupsMonitoringEffect| 
|보안 구성 |Azure Security Center에서 OS 취약성 모니터링 |systemConfigurationsMonitoringEffect| 
|Endpoint Protection |Azure Security Center에서 누락된 Endpoint Protection 모니터링 |endpointProtectionMonitoringEffect |
|디스크 암호화 |Azure Security Center에서 암호화되지 않은 VM 디스크 모니터링 |diskEncryptionMonitoringEffect|
|취약점 평가 |Azure Security Center에서 VM 취약성 모니터링 |vulnerabilityAssessmentMonitoringEffect|
|웹 애플리케이션 방화벽 |Azure Security Center에서 보호되지 않은 웹 애플리케이션 모니터링 |webApplicationFirewallMonitoringEffect |
|차세대 방화벽 |Azure Security Center에서 보호되지 않은 네트워크 엔드포인트 모니터링| |


### <a name="who-can-edit-security-policies"></a>보안 정책은 누가 편집할 수 있나요?
Security Center는 Azure에서 사용자, 그룹 및 서비스에 할당할 수 있는 기본 제공 역할을 제공하는 RBAC(역할 기반 액세스 제어)를 사용합니다. 사용자가 Security Center를 열면 액세스 권한이 있는 리소스와 관련된 정보만 표시됩니다. 즉, 리소스가 속한 리소스 그룹이나 구독에 대한 소유자, 참가자 또는 읽기 권한자 역할이 사용자에게 할당됩니다. 이러한 역할 외에도 두 개의 특정한 Security Center 역할이 있습니다.

- 보안 읽기 권한자: 권장 사항, 경고, 정책 및 상태를 비롯한 Security Center에 대한 권한을 볼 수 있지만 변경할 수는 없습니다.
- 보안 관리자: 보안 읽기 권한자와 동일한 보기 권한을 갖지만 보안 정책을 업데이트하고 권장 사항 및 경고를 해제할 수 있습니다.



## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Polic의 보안 정책을 편집하는 방법을 알아보았습니다. Security Center에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Security Center 계획 및 작업 가이드](security-center-planning-and-operations-guide.md): Azure Security Center에 대한 디자인 고려 사항을 계획하고 이해하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md): Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 응답](security-center-managing-and-responding-alerts.md): 보안 경고를 관리하고 응답하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md): 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에 대한 테넌트 전체 가시성 얻기](security-center-management-groups.md): Azure Security Center에 대한 관리 그룹을 설정하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md): 서비스 사용에 관한 질문과 대답에 대한 답을 가져옵니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/): Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

Azure Policy에 대해 자세히 알아보려면 [Azure Policy란?](../governance/policy/overview.md)을 참조하세요.
