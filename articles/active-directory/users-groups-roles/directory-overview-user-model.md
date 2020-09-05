---
title: Azure Active Directory의 사용자, 그룹, 라이선스 및 역할
description: Azure Active Directory에서 사용자와 할당된 라이선스, 관리자 역할, 그룹 멤버십 간의 관계
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.reviewer: vincesm
ms.date: 06/16/2020
ms.topic: overview
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
services: active-directory
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a752eb8f38c5a2ccc88d7ceed3d3b342d20d3f51
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88795309"
---
# <a name="what-is-enterprise-user-management"></a>엔터프라이즈 사용자 관리란?

이 문서에서는 Azure AD 관리자에게 그룹, 라이선스, 배포된 엔터프라이즈 앱, 라이선스 및 관리자 역할에 의한 사용자에 대한 [ID 관리](../fundamentals/active-directory-whatis.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) 작업 간의 관계를 소개합니다. 조직이 성장함에 따라 Azure AD 그룹 및 관리자 역할을 사용하여 다음을 수행할 수 있습니다.

* 개인 대신에 그룹에 라이선스 할당
* 사용 권한이 적은 역할에 Azure AD 관리 작업을 분산하는 권한 대리자
* 그룹에 엔터프라이즈 앱 액세스 할당

## <a name="assign-users-to-groups"></a>그룹에 사용자 할당

Azure AD의 그룹을 사용하여 다수의 사용자에게 라이선스를 할당하거나 배포된 엔터프라이즈 앱에 대한 사용자 액세스를 할당합니다. 그룹을 사용하여 Azure AD의 글로벌 관리자를 제외한 모든 관리자 역할을 할당하거나 SaaS 애플리케이션 또는 SharePoint 사이트와 같은 외부 리소스에 대한 액세스 권한을 부여할 수 있습니다.

유연성을 높이고 그룹 멤버 자격 관리 작업을 줄이기 위해 Azure AD의 [동적 그룹](groups-create-rule.md)을 사용하여 그룹 멤버 자격을 자동으로 확장 및 축소할 수 있습니다. 하나 이상의 동적 그룹의 멤버인 고유한 각 사용자에 대해 Azure AD Premium P1 라이선스가 필요합니다.

## <a name="assign-licenses-to-groups"></a>그룹에 라이선스 할당

라이선스를 개별적으로 사용자에게 할당하거나 사용자로부터 제거하면 시간과 주의가 필요할 수 있습니다. 대신에 [그룹에 라이선스를 할당하면](../fundamentals/license-users-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) 대규모 라이선스 관리를 더 쉽게 만들 수 있습니다.

Azure AD에서 사용자가 라이선스를 받은 그룹에 합류하면 해당 사용자에게 적절한 라이선스가 자동으로 할당됩니다. 사용자가 그룹을 떠나면 Azure AD는 해당 라이선스 할당을 제거합니다. Azure AD 그룹이 없었다면 조직에 합류하거나 조직을 떠나는 사용자에 대해 사용자 라이선스를 대량으로 추가하거나 제거하기 위해 PowerShell 스크립트를 작성하거나 Graph API를 사용해야 했을 것입니다.

사용 가능한 라이선스가 부족하거나 동시에 할당할 수 없는 서비스 계획 같은 문제가 발생하는 경우 Azure Portal에서 그룹에 대한 라이선싱의 상태를 확인할 수 있습니다.

>[!NOTE]
>그룹 기반 라이선싱 기능은 현재 공개 미리 보기에 있습니다. 미리 보기 중에 유료 Azure AD(Azure Active Directory) 라이선스 계획 또는 평가판에서 기능을 사용할 수 있습니다.

## <a name="delegate-administrator-roles"></a>관리자 역할 대리자

많은 대규모 조직은 사용자가 강력한 글로벌 관리자 역할(예: 애플리케이션을 등록해야 하는 사용자)을 할당하지 않고 업무 작업에 충분한 사용 권한을 얻는 옵션을 원합니다. 다음은 애플리케이션 관리 작업을 더 세밀하게 분배하는 데 도움이 되는 새 Azure AD 관리자 역할의 예제입니다.

 역할 이름 | 사용 권한 요약
 --------- | -------------------
 **애플리케이션 관리자** | 엔터프라이즈 애플리케이션 및 애플리케이션 등록을 추가 및 관리하고 프록시 애플리케이션 설정을 구성할 수 있습니다. 애플리케이션 관리자는 조건부 액세스 정책 및 디바이스를 볼 수 있지만 이를 관리할 수 없습니다.
 **클라우드 애플리케이션 관리자** | 엔터프라이즈 애플리케이션 및 엔터프라이즈 앱 등록을 추가 및 관리할 수 있습니다. 이 역할은 애플리케이션 프록시 설정을 관리할 수 없는 것을 제외하고 애플리케이션 관리자의 모든 사용 권한을 가집니다.
**애플리케이션 개발자** | 애플리케이션 등록을 추가 및 업데이트할 수 있지만 엔터프라이즈 애플리케이션을 관리하거나 애플리케이션 프록시를 구성할 수 없습니다.

새 Azure AD 관리자 역할이 추가되 고 있습니다. 현재 사용 가능한 역할은 Azure Portal 또는 [관리자 역할 사용 권한 참조](directory-assign-admin-roles.md)를 확인하십시오.

## <a name="assign-app-access"></a>앱 액세스 할당

Azure AD를 사용하여 [Azure AD 조직에 배포된 엔터프라이즈 앱](../manage-apps/assign-user-or-group-access-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)에 그룹 액세스를 할당할 수 있습니다. 동적 그룹을 앱에 대한 그룹 할당과 결합하면 조직이 성장함에 따라 사용자 앱 액세스 할당을 자동화할 수 있습니다. 엔터프라이즈 앱에 액세스를 할당하려면 Azure Active Directory Premium P1 또는 Premium P2 라이선스가 필요합니다.

또한 Azure AD에서는 액세스를 할당하는 앱과 그룹 사이에 흐르는 데이터를 세밀하게 제어할 수 있습니다. [엔터프라이즈 애플리케이션](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)에서 앱을 열고 **프로비전**을 선택합니다.

* 지원하는 앱에 대해 자동 프로비전 설정
* 앱의 사용자 관리 API에 연결하기 위한 자격 증명 제공
* 사용자 계정이 프로비전되거나 업데이트될 때 Azure AD와 앱 사이에 흐르는 사용자 특성을 제어하는 매핑 설정
* 앱에 대한 Azure AD 프로비전 서비스를 시작 및 중지하거나 프로비저닝 캐시를 지우거나 서비스를 다시 시작
* Azure AD와 앱 사이에 생성되고, 업데이트되고, 제거된 모든 사용자 및 그룹의 로그를 제공하는 **프로비저닝 활동 보고서**, 그리고 더 자세한 오류 메시지를 제공하는 **프로비저닝 오류 보고서** 확인

## <a name="next-steps"></a>다음 단계

시작하는 Azure AD 관리자인 경우 [Azure Active Directory 기본 사항](../fundamentals/index.yml)에서 기본 사항을 내립니다.

또는 [그룹 만들기](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context), [라이선스 할당](../fundamentals/license-users-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context), [앱 액세스 할당](../manage-apps/assign-user-or-group-access-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) 또는 [관리자 역할 할당](directory-assign-admin-roles.md)을 시작할 수 있습니다.