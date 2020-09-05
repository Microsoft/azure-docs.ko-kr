---
title: 관리 단위 문제 해결 및 FAQ-Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 제한 된 범위가 포함 된 사용 권한을 부여 하는 관리 단위를 조사 합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: b48bebe6aa5f9862d5f51fea257c4b7f3057639b
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88794476"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD 관리 단위: 문제 해결 및 FAQ

Azure Active Directory (Azure AD)의 보다 세부적인 관리 제어를 위해 하나 이상의 au (관리 단위)로 제한 되는 범위를 사용 하 여 Azure AD 역할에 사용자를 할당할 수 있습니다. 일반 작업에 대 한 샘플 PowerShell 스크립트는 [관리 단위 작업](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)을 참조 하세요.

## <a name="frequently-asked-questions"></a>질문과 대답

**Q: 관리 단위를 만들 수 없는 이유는 무엇입니까?**

**A:** *전역 관리자* 또는 권한 있는 *역할 관리자* 만 Azure AD에서 관리 단위를 만들 수 있습니다. 관리 단위를 만들려고 하는 사용자에 게 *전역 관리자* 또는 *권한 있는 역할 관리자* 역할이 할당 되어 있는지 확인 합니다.

**Q: 관리 단위에 그룹을 추가 했습니다. 그룹 구성원이 아직 표시 되지 않는 이유는 무엇 인가요?**

**A:** 관리 단위에 그룹을 추가 하면 그룹의 모든 멤버가 추가 되지 않습니다. 사용자는 관리 단위에 직접 할당 해야 합니다.

**Q: 관리 단위의 멤버를 추가 (또는 제거) 합니다. 구성원이 사용자 인터페이스에서 표시 되지 않는 이유는 무엇 인가요?**

**A:** 관리 단위에서 하나 이상의 멤버를 추가 하거나 제거 하는 프로세스를 **관리 단위** 페이지에 반영 하는 데 몇 분 정도 걸릴 수 있습니다. 또는 연결 된 리소스의 속성으로 직접 이동 하 여 작업이 완료 되었는지 여부를 확인할 수 있습니다. Au의 사용자 및 그룹에 대 한 자세한 내용은 [사용자에 대 한 관리 단위 나열](roles-admin-units-add-manage-users.md) 및 [그룹에 대 한 관리 단위](roles-admin-units-add-manage-groups.md)나열을 참조 하세요.

**Q: 관리 단위에서 위임 된 암호 관리자입니다. 특정 사용자의 암호를 다시 설정할 수 없는 이유는 무엇입니까?**

**A:** 관리 단위의 관리자는 관리 단위에 할당 된 사용자에 대해서만 암호를 다시 설정할 수 있습니다. 암호 재설정이 실패 한 사용자가 할당 된 관리 단위에 속하는지 확인 합니다. 사용자가 동일한 관리 단위에 속하지만 자신의 암호를 다시 설정할 수 없는 경우 사용자에 게 할당 된 역할을 확인 합니다. 

권한 상승을 방지 하기 위해 관리 단위 범위 관리자는 조직 전체 범위에서 역할에 할당 된 사용자의 암호를 다시 설정할 수 없습니다.

**Q: 관리 단위가 필요한 이유는 무엇 인가요? 범위를 정의 하는 방법으로 보안 그룹을 사용할 수 없습니까?**

**A:** 보안 그룹에는 기존 목적과 권한 부여 모델이 있습니다. 예를 들어 *사용자 관리자*는 Azure AD 조직에 있는 모든 보안 그룹의 멤버 자격을 관리할 수 있습니다. 이 역할은 그룹을 사용 하 여 Salesforce와 같은 응용 프로그램에 대 한 액세스를 관리할 수 있습니다. *사용자 관리자* 는 위임 모델 자체를 관리할 수 없어야 합니다 .이는 보안 그룹이 "리소스 그룹화" 시나리오를 지원 하도록 확장 된 경우에 발생 합니다. Windows Server Active Directory의 조직 구성 단위와 같은 관리 단위는 광범위 한 디렉터리 개체의 관리 범위를 지정 하는 방법을 제공 하기 위한 것입니다. 보안 그룹 자체는 리소스 범위의 멤버일 수 있습니다. 보안 그룹을 사용 하 여 관리자가 관리할 수 있는 보안 그룹 집합을 정의 하면 혼동 될 수 있습니다.

**Q: 관리 단위에 그룹을 추가 하는 것은 무엇을 의미 하나요?**

**A:** 관리 단위에 그룹을 추가 하면 해당 관리 단위로 범위가 지정 된 모든 *사용자 관리자* 의 관리 범위로 그룹을 가져옵니다. 관리 단위의 사용자 관리자는 그룹 자체의 이름 및 멤버 자격을 관리할 수 있습니다. *사용자 관리자* 에 게 그룹의 사용자를 관리 하는 관리자 권한을 부여 하지 않습니다 (예: 암호 재설정). 사용자 *관리자* 에 게 사용자를 관리할 수 있는 권한을 부여 하려면 사용자가 직접 관리 단위의 구성원 이어야 합니다.

**Q: 리소스 (사용자 또는 그룹)가 두 개 이상의 관리 장치의 구성원이 될 수 있나요?**

**A:** 예, 리소스는 둘 이상의 관리 단위 멤버일 수 있습니다. 리소스에 대 한 사용 권한이 있는 모든 조직 전체 및 관리 단위 범위 관리자가 리소스를 관리할 수 있습니다.

**Q: B2C 조직에서 관리 단위를 사용할 수 있나요?**

**A:** 아니요. B2C 조직에서는 관리 단위를 사용할 수 없습니다.

**Q: 중첩 된 관리 단위가 지원 되나요?**

**A:** 아니요, 중첩 된 관리 단위는 지원 되지 않습니다.

**Q: PowerShell 및 Graph API에서 지원 되는 관리 단위는 무엇입니까?**

**A:** 예. [PowerShell cmdlet 설명서](/powershell/module/Azuread/?view=azureadps-2.0-preview) 및 [샘플 스크립트](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview)에서 관리 단위에 대 한 지원을 찾을 수 있습니다. 

Microsoft Graph에서 [administrativeUnit 리소스 종류](/graph/api/resources/administrativeunit?view=graph-rest-beta) 에 대 한 지원을 찾습니다.

## <a name="next-steps"></a>다음 단계

- [관리 단위를 사용 하 여 역할의 범위 제한](directory-administrative-units.md)
- [관리 단위 관리](roles-admin-units-manage.md)