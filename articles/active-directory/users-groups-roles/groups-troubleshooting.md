---
title: 동적 그룹 멤버 자격 문제 해결-Azure AD | Microsoft Docs
description: Azure Active Directory의 동적 그룹 멤버 자격에 대 한 문제 해결 팁
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f685ac63e3b4a8cf466be4eb4561472fb084d49
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026553"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>문제 해결 및 그룹 문제 해결

## <a name="troubleshooting-group-creation-issues"></a>그룹 만들기 문제 해결

**Azure Portal에서 보안 그룹 만들기를 사용 하지 않도록 설정 했지만 Powershell을 통해 그룹을 계속 만들 수 있습니다** . 사용자는 관리자가 아닌 사용자가 액세스 패널 또는 Azure Portal에서 보안 그룹을 만들 수 있는지 여부를 제어 하는 Azure Portal의 **Azure portal에서 보안 그룹을 만들 수 있습니다** . Powershell을 통해 보안 그룹 만들기를 제어 하지 않습니다.

Powershell에서 관리자가 아닌 사용자에 대 한 그룹 만들기를 사용 하지 않도록 설정 하려면:
1. 관리자가 아닌 사용자가 그룹을 만들 수 있는지 확인합니다.
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. `UsersPermissionToCreateGroupsEnabled : True`가 반환되면 관리자가 아닌 사용자가 그룹을 만들 수 있는 것입니다. 이 기능을 사용하지 않도록 설정하려면
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Powershell에서 동적 그룹을 만들려고 할 때 허용 되는 최대 그룹 오류가 수신 되었습니다.**<br/>
동적 그룹 정책을 나타내는 메시지가 Powershell에 수신 _되는 경우 허용 되는 최대 그룹 수에 도달_하면 테넌트의 동적 그룹에 대 한 최대 제한에 도달 했음을 의미 합니다. 테넌트 당 최대 동적 그룹 수는 5000입니다.

새 동적 그룹을 만들려면 먼저 일부 기존 동적 그룹을 삭제 해야 합니다. 한도를 늘릴 수 있는 방법은 없습니다.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>그룹의 동적 멤버 자격 문제 해결

**그룹에 대한 규칙을 구성했으나 그룹에서 구성원이 업데이트되지 않음**<br/>
1. 규칙의 사용자 또는 장치 특성에 대 한 값을 확인 합니다. 규칙을 충족 하는 사용자가 있는지 확인 합니다. 장치의 경우 장치 속성을 확인 하 여 동기화 된 특성에 예상 값이 포함 되어 있는지 확인 합니다.<br/>
2. 멤버 자격 처리 상태를 확인 하 여 완료 되었는지 확인 합니다. 그룹의 **개요** 페이지에서 [멤버 자격 처리 상태](groups-create-rule.md#check-processing-status-for-a-rule) 와 마지막 업데이트 날짜를 확인할 수 있습니다.

모든 것이 제대로 된 것 같으면 그룹이 채워지도록 잠시 기다립니다. 테넌트 크기에 따라 그룹을 채우는 데 처음 또는 규칙이 변경된 후에는 최대 24시간이 소요될 수 있습니다.

**규칙을 구성했으나 규칙의 기존 구성원이 제거됨**<br/>이는 정상적인 동작입니다. 규칙을 사용하도록 설정하거나 변경하면 그룹의 기존 멤버가 제거됩니다. 규칙 평가에서 반환된 사용자는 그룹에 멤버로 추가됩니다.

**규칙을 추가하거나 변경하는 즉시 멤버 자격 변경 내용이 표시되지 않는 이유는?**<br/>위임된 구성원은 비동기식 백그라운드 프로세스에서 주기적으로 평가되기 때문입니다. 프로세스에서 걸리는 시간은 디렉터리의 사용자 수와 규칙 결과로 만들어진 그룹의 크기로 결정됩니다. 일반적으로 사용자 수가 적은 디렉터리에는 2-3분 내에 그룹 멤버 자격 변경 내용이 표시됩니다. 사용자 수가 많은 디렉터리는 채우는 데 30분 이상이 걸릴 수 있습니다.

**그룹을 지금 강제로 처리 하려면 어떻게 해야 하나요?**<br/>
현재는 요청 시 처리 되도록 그룹을 자동으로 트리거할 수 있는 방법이 없습니다. 그러나 끝에 공백을 추가 하도록 멤버 자격 규칙을 업데이트 하 여 다시 처리를 수동으로 트리거할 수 있습니다.  

**오류 처리 규칙이 발생함**<br/>다음 표에서는 일반적인 동적 멤버 자격 규칙 오류와 이를 수정하는 방법을 나열합니다.

| 규칙 파서 오류 | 잘못된 사용법 | 수정된 사용법 |
| --- | --- | --- |
| 오류: 특성이 지원되지 않습니다. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>[지원되는 속성 목록](groups-dynamic-membership.md#supported-properties)에 있는 특성인지 확인합니다. |
| 오류: 특성에서는 연산자가 지원되지 않습니다. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>사용된 연산자는 속성 형식에 지원되지 않습니다(이 예제에서 -contains는 부울 형식에 사용할 수 없습니다). 속성 형식에 올바른 연산자를 사용합니다. |
| 오류: 쿼리 컴파일 오류입니다. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2.  (user.userPrincipalName -match "*@domain.ext") | 1. 연산자가 없습니다. 두 개의 조인 조건자(-and 또는 -or)를 사용합니다.<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2.-match와 함께 사용 된 정규식에 오류가 있습니다.<br>(user.userPrincipalName -match ".*@domain.ext")<br>or alternatively: (user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>다음 단계

이러한 문서는 Azure Active Directory에 대한 추가 정보를 제공합니다.

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory의 애플리케이션 관리](../manage-apps/what-is-application-management.md)
* [Azure Active Directory란?](../fundamentals/active-directory-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](../hybrid/whatis-hybrid-identity.md)