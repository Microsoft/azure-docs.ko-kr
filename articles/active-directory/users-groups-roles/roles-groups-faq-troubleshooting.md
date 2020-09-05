---
title: 클라우드 그룹에 할당 된 역할 문제 해결 FAQ-Azure Active Directory | Microsoft Docs
description: Azure Portal, PowerShell 또는 Graph API의 역할 할당 가능 그룹에 Azure AD 역할을 할당 합니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3f2a23da5baa3a5d1955b10d18411fcedc3acd1
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798298"
---
# <a name="troubleshooting-roles-assigned-to-cloud-groups"></a>클라우드 그룹에 할당된 역할 문제 해결

다음은 Azure Active Directory (Azure AD)에서 그룹에 역할을 할당 하기 위한 몇 가지 일반적인 질문과 문제 해결 팁입니다.

**Q:** 그룹 관리자 이지만 **AZURE AD 역할을 그룹 스위치에 할당할 수** 없습니다.

**A:** 권한 있는 역할 관리자 또는 전역 관리자만 역할 할당에 적합 한 그룹을 만들 수 있습니다. 해당 역할의 사용자만이 컨트롤을 볼 수 있습니다.

**Q:** Azure AD 역할에 할당 된 그룹의 멤버 자격을 수정할 수 있는 사람은 누구 인가요?

**A:** 기본적으로 권한 있는 역할 관리자와 전역 관리자만 역할 할당 그룹의 멤버 자격을 관리 하지만 그룹 소유자를 추가 하 여 역할 할당 그룹의 관리를 위임할 수 있습니다.

**Q**: 내 조직의 기술 지원팀 관리자 이지만 디렉터리 판독기 인 사용자의 암호를 업데이트할 수 없습니다. 왜 발생 하나요?

**A**: 사용자는 역할 할당 가능 그룹을 통해 디렉터리 판독기를 사용할 수 있습니다. 역할 할당 그룹의 모든 멤버 및 소유자는 보호 됩니다. 권한 있는 인증 관리자 또는 전역 관리자 역할의 사용자만 보호 된 사용자에 대 한 자격 증명을 다시 설정할 수 있습니다.

**Q:** 사용자의 암호를 업데이트할 수 없습니다. 할당 된 높은 권한이 있는 역할이 없습니다. 왜 발생 하나요?

**A:** 사용자는 역할 할당 가능 그룹의 소유자 일 수 있습니다. 권한 상승을 방지 하기 위해 역할 할당 그룹의 소유자를 보호 합니다. 예를 들어 그룹 Contoso_Security_Admins 보안 관리자 역할에 할당 된 경우 (Bob은 그룹 소유자이 고 Alice는 조직의 암호 관리자 인 경우)가 될 수 있습니다. 이 보호가 없는 경우 Alice는 Bob의 자격 증명을 다시 설정 하 고 id를 사용할 수 있습니다. 그런 다음 Alice는 그룹 Contoso_Security_Admins 그룹에 자신 또는 사람을 추가 하 여 조직의 보안 관리자가 될 수 있습니다. 사용자가 그룹 소유자 인지 확인 하려면 해당 사용자의 소유 개체 목록을 가져오고 isAssignableToRole가 true로 설정 된 그룹이 있는지 확인 합니다. 그렇다면 해당 사용자는 보호 되 고 동작은 의도 된 것입니다. 소유 개체를 가져오려면 다음 설명서를 참조 하세요.

- [Get-AzureADUserOwnedObject](/powershell/module/azuread/get-azureaduserownedobject?view=azureadps-2.0)  
- [OwnedObjects 나열](/graph/api/user-list-ownedobjects?tabs=http&view=graph-rest-1.0)

**Q:** Azure AD 역할에 할당 될 수 있는 그룹에 대 한 액세스 검토를 만들 수 있습니다 (특히 isAssignableToRole 속성이 true로 설정 된 그룹).  

**A:** 네 당신은 할 수 있어요. 최신 버전의 액세스 검토를 사용 하는 경우 검토자는 기본적으로 내 액세스로 전송 되며 전역 관리자만 역할 할당 가능 그룹에 대 한 액세스 검토를 만들 수 있습니다. 그러나 이전 버전의 액세스 검토를 사용 하는 경우 검토자는 기본적으로 액세스 패널로 전달 되며 전역 관리자와 사용자 관리자는 역할 할당 가능 그룹에 대 한 액세스 검토를 만들 수 있습니다. 새 환경은 2020 년 7 월 28 일에 모든 고객에 게 출시 되지만 곧 업그레이드 하려는 경우 [AZURE AD 액세스 검토-내 액세스 등록의 업데이트 된 검토자 환경](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)에 요청 하세요.

**Q:** 액세스 패키지를 만들고 Azure AD 역할에 할당 될 수 있는 그룹을 배치할 수 있나요?

**A:** 네 당신은 할 수 있어요. 전역 관리자 및 사용자 관리자는 액세스 패키지에 모든 그룹을 배치할 수 있습니다. 전역 관리자에 대 한 변경 사항은 없지만 사용자 관리자 역할 사용 권한은 약간 변경 됩니다. 액세스 패키지에 역할 할당 가능 그룹을 추가 하려면 사용자 관리자 및 역할 할당 가능 그룹의 소유자 여야 합니다. 엔터프라이즈 라이선스 관리에서 액세스 패키지를 만들 수 있는 사람을 보여 주는 전체 표는 다음과 같습니다.

Azure AD 디렉터리 역할 | 자격 관리 역할 | 보안 그룹을 추가할 수 있음\* | Microsoft 365 그룹 추가 가능\* | 앱 추가 가능 | SharePoint Online 사이트 추가 가능
----------------------- | --------------------------- | ----------------------- | ------------------------- | ----------- |  -----------------------------
전역 관리자 | 해당 없음 | ✔️ | ✔️ | ✔️  | ✔️
사용자 관리자  | 해당 없음  | ✔️  | ✔️  | ✔️
Intune 관리자 | 카탈로그 소유자 | ✔️  | ✔️  | &nbsp;  | &nbsp;
Exchange 관리자  | 카탈로그 소유자  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
팀 서비스 관리자 | 카탈로그 소유자  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
SharePoint 관리자 | 카탈로그 소유자 | &nbsp; | ✔️  | &nbsp;  | ✔️ 
애플리케이션 관리자 | 카탈로그 소유자  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
클라우드 애플리케이션 관리자 | 카탈로그 소유자  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
사용자 | 카탈로그 소유자 | 그룹 소유자 인 경우에만 | 그룹 소유자 인 경우에만 | 앱 소유자 인 경우에만  | &nbsp;

\*그룹에 역할을 할당할 수 없습니다. 즉, isAssignableToRole = false입니다. 그룹이 역할에 할당 가능 하 게 되 면 액세스 패키지를 만드는 사용자도 역할 할당 가능 그룹의 소유자 여야 합니다.

**Q:** "할당 된 역할"에서 "할당 제거" 옵션을 찾을 수 없습니다. 사용자에 게 역할 할당을 삭제 어떻게 할까요? 시겠습니까?

**A:** 이 대답은 Azure AD Premium P1 조직에만 적용 됩니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 여 **Azure Active Directory**를 엽니다.
1. 사용자를 선택 하 고 사용자 프로필을 엽니다.
1. **할당 된 역할**을 선택 합니다.
1. 기어 아이콘을 선택 합니다. 이 정보를 제공할 수 있는 창이 열립니다. 직접 할당 옆에는 "제거" 단추가 있습니다. 간접 역할 할당을 제거 하려면 역할이 할당 된 그룹에서 사용자를 제거 합니다.

**Q:** 역할을 할당할 수 있는 모든 그룹을 볼 어떻게 할까요? 있나요?

**A:** 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 여 **Azure Active Directory**를 엽니다.
1. **그룹**  >  **모든 그룹**을 선택 합니다.
1. **필터 추가**를 선택 합니다.
1. 할당 가능한 **역할**에 필터링 합니다.

**Q:** 보안 주체에 직접적이 고 간접적으로 할당 되는 역할을 어떻게 할까요? 하는 것은 무엇 인가요?

**A:** 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 여 **Azure Active Directory**를 엽니다.
1. 사용자를 선택 하 고 사용자 프로필을 엽니다.
1. **할당 된 역할**을 선택 하 고 다음을 수행 합니다.

    - Azure AD Premium P1 사용이 허가 된 조직: 기어 아이콘을 선택 합니다. 이 정보를 제공할 수 있는 창이 열립니다.
    - Azure AD Premium P2 사용이 허가 된 조직: **멤버 자격** 열에서 직접 및 상속 된 라이선스 정보를 찾을 수 있습니다.

**Q:** 역할에 할당 하기 위해 새 클라우드 그룹을 만들도록 적용 하는 이유는 무엇 인가요?  

**A:** 역할에 기존 그룹을 할당 하는 경우 기존 그룹 소유자는 새 멤버가 역할을 갖게 될 것을 인식 하지 않고도이 그룹에 다른 구성원을 추가할 수 있습니다. 역할 할당 가능한 그룹이 강력 하기 때문에 보호 하는 데 많은 제한이 있습니다. 그룹을 관리 하는 사용자에 게는 그룹 변경을 원하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [클라우드 그룹을 사용 하 여 역할 할당 관리](roles-groups-concept.md)
- [역할 할당 가능 그룹 만들기](roles-groups-create-eligible.md)