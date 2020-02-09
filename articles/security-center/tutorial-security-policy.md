---
title: 보안 정책 작업 | Microsoft Docs
description: 이 문서에서는 Azure Security Center에서 보안 정책을 사용 하는 방법을 설명 합니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 45a27be400753b56c42310a340334feba8a420c5
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906868"
---
# <a name="working-with-security-policies"></a>보안 정책 작업

이 문서에서는 보안 정책을 구성하는 방법 및 Security Center에서 보안 정책을 보는 방법을 설명합니다. 

## <a name="introduction-to-security-policies"></a>보안 정책 소개

보안 정책은 원하는 워크 로드 구성을 정의 하 고 회사 또는 조정기의 보안 요구 사항을 준수 하는지 확인 하는 데 도움이 됩니다.

Azure Security Center은 선택한 정책에 따라 보안 권장 사항을 적용 합니다. Security Center 정책은 Azure Policy에서 만든 정책 이니셔티브를 기반으로 합니다. [Azure Policy](../governance/policy/overview.md) 를 사용 하 여 정책을 관리 하 고 관리 그룹 및 여러 구독 간에 정책을 설정할 수 있습니다.

Security Center은 보안 정책을 사용 하기 위한 다음과 같은 옵션을 제공 합니다.

* 기본 **제공 기본 정책 보기 및 편집** -Security Center를 사용 하도록 설정 하면 ' ASC default ' 라는 기본 제공 이니셔티브를 모든 Security Center 등록 된 구독 (무료 또는 표준 계층)에 자동으로 할당 합니다. 이 이니셔티브를 사용자 지정 하려면 내부에서 개별 정책을 사용 하거나 사용 하지 않도록 설정할 수 있습니다. [기본 제공 보안 정책](security-center-policy-definitions.md) 목록을 참조 하 여 기본적으로 제공 되는 옵션을 이해 합니다.

* **사용자 고유의 사용자 지정 정책 추가** -구독에 적용 되는 보안 이니셔티브를 사용자 지정 하려는 경우 Security Center 내에서 수행할 수 있습니다. 그러면 컴퓨터가 만든 정책을 따르지 않는 경우 권장 사항을 받게 됩니다. 사용자 지정 정책 빌드 및 할당에 대 한 지침은 [사용자 지정 보안 정책 사용](custom-security-policies.md)을 참조 하세요.

* **규정 준수 정책 추가** -Security Center의 규정 준수 대시보드는 특정 표준 또는 규정 (예: Azure CIS, NIST SP 800-53 4, SWIFT CSP)의 컨텍스트에서 환경 내의 모든 평가 상태를 표시 합니다. CSCF-v2020). 자세한 내용은 [규정 준수 개선](security-center-compliance-dashboard.md)을 참조하세요.


## <a name="managing-your-security-policies"></a>보안 정책 관리

Security Center에서 보안 정책을 보려면

1. **Security Center** 대시보드에서 **보안 정책**을 선택합니다.

    ![정책 관리 창](./media/security-center-policies/security-center-policy-mgt.png)

   **정책 관리** 화면에서 관리 그룹 구조뿐만 아니라 관리 그룹, 구독 및 작업 영역 수를 확인할 수 있습니다.

1. 확인하려는 구독 또는 관리 그룹의 정책을 선택합니다.

1. 해당 구독 또는 관리 그룹에 대 한 보안 정책 페이지가 나타납니다. 사용 가능한 정책과 할당 된 정책을 표시 합니다.

   ![정책 화면](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > 기본 정책과 함께 "MG 상속" 이라는 레이블이 있으면 정책이 관리 그룹에 할당 되 고 사용자가 보고 있는 구독에서 상속 된 것입니다.


1. 이 페이지에서 사용할 수 있는 옵션 중에서 선택 합니다.

    1. 산업 정책을 사용 하려면 **더 많은 표준 추가**를 클릭 합니다. 자세한 내용은 [동적 준수 패키지 업데이트를](update-regulatory-compliance-packages.md)참조 하세요.

    1. 사용자 지정 이니셔티브를 할당 하 고 관리 하려면 **사용자 지정 이니셔티브 추가**를 클릭 합니다. 자세한 내용은 [사용자 지정 보안 정책 사용](custom-security-policies.md)을 참조 하세요.

    1. 기본 정책을 보고 편집 하려면 **유효한 정책 보기** 를 클릭 하 고 아래에 설명 된 대로 진행 합니다. 

       ![정책 화면](./media/security-center-policies/policy-screen.png)
       
       이 **보안 정책** 화면은 선택한 구독 또는 관리 그룹에 할당 된 정책에 의해 수행 된 작업을 반영 합니다.
       
       * 맨 위에 있는 링크를 사용 하 여 구독 또는 관리 그룹에 적용 되는 정책 **할당** 을 엽니다. 이러한 링크를 사용 하 여 할당에 액세스 하 고 정책을 편집 하거나 사용 하지 않도록 설정할 수 있습니다. 예를 들어 특정 정책 할당이 엔드포인트 보호를 효과적으로 거부 하는 것으로 확인 되 면이 링크를 사용 하 여 정책을 편집 하거나 사용 하지 않도록 설정 합니다.
       
       * 정책 목록에서 구독 또는 관리 그룹에 정책을 효과적으로 적용하는 것을 확인할 수 있습니다. 범위에 적용 되는 각 정책의 설정을 고려 하 여 정책에서 수행 하는 작업의 누적 결과가 표시 됩니다. 예를 들어 한 할당에서 정책 할당을 사용 하지 않도록 설정 했지만 다른 AuditIfNotExist로 설정 된 경우 누적 효과는 AuditIfNotExist 적용 됩니다. 더 활성화된 효과가 항상 우선합니다.
       
       * 정책의 효과는 Append, Audit, AuditIfNotExists, Deny, DeployIfNotExists, Disabled일 수 있습니다. 효과를 적용하는 방법에 대한 자세한 내용은 [Policy 효과 이해](../governance/policy/concepts/effects.md)를 참조하세요.

       > [!NOTE]
       > 할당된 정책을 보는 경우 여러 할당을 확인할 수 있으며 각 할당이 자체적으로 구성되는 방법도 확인할 수 있습니다.


## <a name="who-can-edit-security-policies"></a>보안 정책은 누가 편집할 수 있나요?

REST API를 통해 또는 Windows PowerShell을 사용하여 Azure Policy 포털을 통해 보안 정책을 편집할 수 있습니다.

Security Center는 Azure에서 사용자, 그룹 및 서비스에 할당할 수 있는 기본 제공 역할을 제공하는 RBAC(역할 기반 액세스 제어)를 사용합니다. 사용자가 Security Center를 열면 액세스 권한이 있는 리소스와 관련된 정보만 표시됩니다. 즉, 사용자에 게 리소스 구독에 대 한 *소유자*, *참가자*또는 *읽기 권한자* 역할이 할당 됩니다. 이러한 역할에는 두 가지 특정 Security Center 역할이 있습니다.

- **보안 읽기 권한자**: 권장 사항, 경고, 정책 및 상태를 비롯한 Security Center에 대한 권한을 볼 수 있지만 변경할 수는 없습니다.
- **보안 관리자**: *보안 읽기 권한자*와 동일한 보기 권한을 갖지만 보안 정책을 업데이트하고 권장 사항 및 경고를 해제할 수 있습니다.


## <a name="disable-security-policies"></a>보안 정책 사용 안 함
기본 보안 정책에서 사용자 환경과 관련이 없는 권장 사항을 생성 하는 경우 권장 사항을 보내는 정책 정의를 사용 하지 않도록 설정 하 여 중지할 수 있습니다.
권장 사항에 대 한 자세한 내용은 [보안 권장 사항 관리](security-center-recommendations.md)를 참조 하세요.

1. Security Center의 **정책 & 준수** 섹션에서 **보안 정책**을 클릭 합니다.

   ![정책 관리](./media/tutorial-security-policy/policy-management.png)

2. 권장 사항을 사용 하지 않도록 설정할 구독 또는 관리 그룹을 클릭 합니다.

   > [!NOTE]
   > 관리 그룹은 해당 정책을 해당 구독에 적용 합니다. 따라서 구독의 정책을 사용 하지 않도록 설정 하 고 구독이 동일한 정책을 계속 사용 하는 관리 그룹에 속하는 경우 정책 권장 사항을 계속 받게 됩니다. 정책은 여전히 관리 수준에서 적용 되며 권장 사항은 계속 생성 됩니다.

1. **유효 정책 보기**를 클릭 합니다.

   ![정책 사용 안 함](./media/tutorial-security-policy/view-effective-policy.png)

1. 할당 된 정책을 클릭 합니다.

   ![정책 사용 안 함](./media/tutorial-security-policy/security-policy.png)

1. **매개 변수** 섹션에서 사용 하지 않도록 설정할 권장 사항을 호출 하는 정책을 검색 하 고 드롭다운 목록에서 **사용 안 함** 을 선택 합니다.

   ![정책 사용 안 함](./media/tutorial-security-policy/disable-policy.png)

1. **Save**를 클릭합니다.

   > [!NOTE]
   > 정책 사용 안 함 변경을 적용 하는 데 최대 12 시간이 걸릴 수 있습니다.



## <a name="next-steps"></a>다음 단계
이 문서에서는 보안 정책에 대해 알아보았습니다. 관련 정보는 다음 문서를 참조 하세요.

* PowerShell을 사용 하 여 정책을 설정 하는 방법에 대 한 지침은 [빠른 시작: Azure PowerShell 모듈을 사용 하 여 비호환 리소스를 식별 하는 정책 할당 만들기를](../governance/policy/assign-policy-powershell.md) 참조 하세요.

* Azure Policy에서 보안 정책을 편집 하는 방법에 대 한 지침은 [규정 준수를 적용 하는 정책 만들기 및 관리](../governance/policy/tutorials/create-and-manage.md)를 참조 하세요.

* Azure Policy를 사용 하 여 구독 또는 관리 그룹에서 정책을 설정 하는 방법에 대 한 지침은 [Azure Policy 무엇입니까?](../governance/policy/overview.md) 를 참조 하세요.