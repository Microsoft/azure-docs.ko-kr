---
title: Azure AD 프로 비전 작동 방법 이해 | Microsoft Docs
description: Azure AD 프로 비전 작동 방법 이해
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c3c0aea6ecaccc972702a8c87e4d127c71c75d6
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121358"
---
# <a name="how-provisioning-works"></a>프로비저닝 작동 방법

자동 프로 비전은 사용자가 액세스 해야 하는 클라우드 응용 프로그램에서 사용자 id 및 역할을 만드는 것을 말합니다. 자동 프로비저닝에는 사용자 ID를 생성하는 것 외에도 상태 또는 역할이 변경될 때 사용자 ID의 유지 관리 및 제거가 포함됩니다. 배포를 시작 하기 전에이 문서를 검토 하 여 Azure AD 프로 비전이 작동 하는 방법과 구성 권장 사항을 얻는 방법에 대해 알아보세요. 

**AZURE AD 프로 비전 서비스** 는 응용 프로그램 공급 업체에서 제공 하는 Scim (도메인 간 id 관리) 2.0 사용자 관리 API 끝점에 대 한 시스템에 연결 하 여 SaaS 앱 및 기타 시스템에 사용자를 프로 비전 합니다. 이 SCIM 끝점을 사용 하면 Azure AD에서 사용자를 프로그래밍 방식으로 만들고, 업데이트 하 고, 제거할 수 있습니다. 선택한 응용 프로그램의 경우 프로 비전 서비스는 그룹 및 역할과 같은 추가 id 관련 개체를 만들고 업데이트 하 고 제거할 수도 있습니다. Azure AD와 응용 프로그램 간의 프로 비전에 사용 되는 채널은 HTTPS SSL 암호화를 사용 하 여 암호화 됩니다.


Azure AD 프로 비전 서비스를 ![](./media/how-provisioning-works/provisioning0.PNG)
*그림 1: AZURE Ad 프로 비전 서비스*

![아웃 바운드 사용자 프로 비전 워크플로](./media/how-provisioning-works/provisioning1.PNG)
*그림 2: AZURE AD에서 인기 있는 SaaS 응용 프로그램에 대 한 "아웃 바운드" 사용자 프로 비전 워크플로*

인바운드 사용자 프로 비전 워크플로](./media/how-provisioning-works/provisioning2.PNG)
*그림 3: 인기 있는 HCM (인간 자본 관리) 응용 프로그램에서 Azure Active Directory 및 Windows Server로의 "인바운드" 사용자 프로 비전 워크플로를 ![Active Directory*

## <a name="provisioning-using-scim-20"></a>SCIM 2.0을 사용한 프로 비전

Azure AD 프로 비전 서비스는 자동 프로 비전을 위해 [Scim 2.0 프로토콜](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards) 을 사용 합니다. 서비스는 응용 프로그램에 대 한 SCIM 끝점에 연결 하 고 SCIM 사용자 개체 스키마 및 REST Api를 사용 하 여 사용자 및 그룹의 프로 비전 및 프로 비전 해제를 자동화 합니다. SCIM 기반 프로 비전 커넥터는 Azure AD 갤러리에서 대부분의 응용 프로그램에 대해 제공 됩니다. Azure AD 용 앱을 빌드할 때 개발자는 SCIM 2.0 사용자 관리 API를 사용 하 여 프로 비전을 위해 Azure AD를 통합 하는 SCIM 끝점을 만들 수 있습니다. 자세한 내용은 [SCIM 끝점 빌드 및 사용자 프로 비전 구성](../app-provisioning/use-scim-to-provision-users-and-groups.md)을 참조 하세요.

현재 없는 앱에 대 한 자동 Azure AD 프로 비전 커넥터를 요청 하려면 [Azure Active Directory 응용 프로그램 요청](https://aka.ms/aadapprequest)을 입력 합니다.

## <a name="authorization"></a>권한 부여

자격 증명은 Azure AD에서 응용 프로그램의 사용자 관리 API에 연결 하는 데 필요 합니다. 응용 프로그램에 대 한 자동 사용자 프로비저닝을 구성 하는 동안 유효한 자격 증명을 입력 해야 합니다. 앱 자습서를 참조 하 여 응용 프로그램에 대 한 자격 증명 유형 및 요구 사항을 찾을 수 있습니다. Azure Portal에서 Azure AD가 제공 된 자격 증명을 사용 하 여 앱의 프로 비전 앱에 연결을 시도 하 여 자격 증명을 테스트할 수 있습니다.

응용 프로그램에 대 한 SAML 기반 Single Sign-On 구성 된 경우 Azure AD의 내부 응용 프로그램 당 저장소 제한은 1024 바이트입니다. 이 제한에는 응용 프로그램의 단일 인스턴스 (Azure AD의 서비스 주체 레코드 라고도 함)와 연결 된 모든 인증서, 비밀 토큰, 자격 증명 및 관련 구성 데이터가 포함 됩니다. SAML 기반 Single Sign-On 구성 되 면 SAML 토큰에 서명 하는 데 사용 되는 인증서가 공간의 50%% 이상 사용 되는 경우가 많습니다. 사용자 프로 비전을 설정 하는 동안 입력 한 추가 항목 (비밀 토큰, Uri, 알림 전자 메일 주소, 사용자 이름 및 암호)이 저장소 제한을 초과할 수 있습니다. 자세한 내용은 [사용자 프로 비전을 구성 하는 동안 관리자 자격 증명을 저장 하는 문제](../manage-apps/application-provisioning-config-problem-storage-limit.md)를 참조 하세요.

## <a name="mapping-attributes"></a>매핑 특성

타사 SaaS 응용 프로그램에 대 한 사용자 프로 비전을 사용 하도록 설정 하면 Azure Portal 특성 매핑을 통해 해당 특성 값을 제어 합니다. 매핑은 사용자 계정이 프로 비전 되거나 업데이트 될 때 Azure AD와 대상 응용 프로그램 간에 흐르는 사용자 특성을 결정 합니다.

Azure AD 사용자 개체와 각 SaaS 앱의 사용자 개체 사이에는 미리 구성 된 특성 및 특성 매핑 집합이 있습니다. 일부 앱은 그룹과 같은 다른 유형의 개체를 사용자와 함께 관리 합니다.

프로 비전을 설정할 때 Azure AD에서 응용 프로그램으로 전달 되는 사용자 (또는 그룹) 속성을 정의 하는 특성 매핑 및 워크플로를 검토 하 고 구성 하는 것이 중요 합니다. 두 시스템 간에 사용자/그룹을 고유 하 게 식별 하 고 일치 시키는 데 사용 되는 일치 하는 속성 (**이 특성을 사용 하 여 개체 일치**)을 검토 하 고 구성 합니다.

비즈니스 요구 사항에 따라 기본 특성 매핑을 사용자 지정할 수 있습니다. 따라서 기존 특성 매핑을 변경 또는 삭제 하거나 새 특성 매핑을 만들 수 있습니다. 자세한 내용은 [SaaS 응용 프로그램에 대 한 사용자 프로 비전 특성 매핑 사용자 지정](../manage-apps/customize-application-attributes.md)을 참조 하세요.

SaaS 애플리케이션에 프로비전을 구성하면 식 매핑은 지정할 수 있는 특성 매핑의 유형 중 하나입니다. 이러한 매핑의 경우 사용자 데이터를 SaaS 응용 프로그램에 보다 적합 한 형식으로 변환할 수 있는 스크립트와 유사한 식을 작성 해야 합니다. 자세한 내용은 [특성 매핑에 대 한 식 작성](functions-for-customizing-application-data.md)을 참조 하세요.

## <a name="scoping"></a>범위 지정 
### <a name="assignment-based-scoping"></a>할당 기반 범위 지정

Azure AD에서 SaaS 응용 프로그램으로 아웃 바운드 프로 비전을 위해 [사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md) 을 사용 하는 것은 프로 비전 범위에 있는 사용자를 결정 하는 가장 일반적인 방법입니다. 사용자 할당은 Single Sign-On를 사용 하도록 설정 하는 데도 사용 되므로 액세스 및 프로 비전을 관리 하는 데 동일한 방법을 사용할 수 있습니다. 할당 기반 범위 지정은 Workday 및 Successfactors와 같은 인바운드 프로 비전 시나리오에 적용 되지 않습니다.

* **그룹.** Azure AD Premium 라이선스 계획을 사용 하면 그룹을 사용 하 여 SaaS 응용 프로그램에 대 한 액세스 권한을 할당할 수 있습니다. 그런 다음 프로 비전 범위가 **할당 된 사용자 및 그룹만 동기화**하도록 설정 된 경우 Azure AD 프로 비전 서비스는 사용자가 응용 프로그램에 할당 된 그룹의 멤버 인지 여부에 따라 사용자를 프로 비전 하거나 프로 비전 해제 합니다. 그룹 개체 자체는 응용 프로그램이 그룹 개체를 지원 하지 않는 한 프로 비전 되지 않습니다.

* **동적 그룹.** Azure AD 사용자 프로 비전 서비스는 [동적 그룹](../users-groups-roles/groups-create-rule.md)에서 사용자를 읽고 프로 비전 할 수 있습니다. 이러한 주의 사항 및 권장 사항을 염두에 두십시오.

  * 동적 그룹은 Azure AD에서 SaaS 응용 프로그램으로 종단 간 프로 비전의 성능에 영향을 줄 수 있습니다.

  * 동적 그룹의 사용자가 SaaS 응용 프로그램에서 프로 비전 되거나 프로 비전 해제 되는 속도는 동적 그룹이 구성원 자격 변경 내용을 평가할 수 있는 속도에 따라 달라 집니다. 동적 그룹의 처리 상태를 확인 하는 방법에 대 한 자세한 내용은 [멤버 자격 규칙에 대 한 처리 상태 확인](../users-groups-roles/groups-create-rule.md)을 참조 하세요.

  * 사용자가 동적 그룹의 멤버 자격을 잃은 경우 프로 비전 해제 이벤트로 간주 됩니다. 동적 그룹에 대 한 규칙을 만들 때이 시나리오를 고려 합니다.

* **중첩 된 그룹.** Azure AD 사용자 프로 비전 서비스는 중첩 된 그룹의 사용자를 읽거나 프로 비전 할 수 없습니다. 서비스는 명시적으로 할당 된 그룹의 직접 구성원 인 사용자만 읽고 프로 비전 할 수 있습니다. "응용 프로그램에 대 한 그룹 기반 할당"의 이러한 제한은 Single Sign-On에도 영향을 줍니다. [그룹을 사용 하 여 SaaS 응용 프로그램에 대 한 액세스 관리를](../users-groups-roles/groups-saasapps.md)참조 하세요. 대신, 프로 비전 해야 하는 사용자가 포함 된 그룹에서 직접 할당 하거나 [범위](define-conditional-rules-for-provisioning-user-accounts.md) 를 지정 합니다.

### <a name="attribute-based-scoping"></a>특성 기반 범위 지정 

범위 지정 필터를 사용 하 여 응용 프로그램에 프로 비전 되는 사용자를 결정 하는 특성 기반 규칙을 정의할 수 있습니다. 이 메서드는 일반적으로 HCM 응용 프로그램에서 Azure AD로의 인바운드 프로 비전 및 Active Directory에 사용 됩니다. 범위 지정 필터는 각 Azure AD 사용자 프로비전 커넥터에 대해 특성 매핑의 일부로 구성됩니다. 특성 기반 범위 지정 필터를 구성 하는 방법에 대 한 자세한 내용은 [범위 지정 필터를 사용한 특성 기반 응용 프로그램 프로 비전](define-conditional-rules-for-provisioning-user-accounts.md)을 참조 하세요.

### <a name="b2b-guest-users"></a>B2B (게스트) 사용자

Azure AD 사용자 프로 비전 서비스를 사용 하 여 Azure AD의 B2B (또는 게스트) 사용자를 SaaS 응용 프로그램에 프로 비전 할 수 있습니다. 그러나 B2B 사용자가 Azure AD를 사용 하 여 SaaS 응용 프로그램에 로그인 하는 경우 SaaS 응용 프로그램에는 특정 방식으로 구성 된 SAML 기반 Single Sign-On 기능이 있어야 합니다. B2B 사용자의 로그인을 지원하도록 SaaS 애플리케이션을 구성하는 방법에 대한 자세한 내용은 [B2B 협업을 위한 SaaS 앱 구성](../b2b/configure-saas-apps.md)을 참조하세요.

게스트 사용자에 대 한 userPrincipalName "alias # EXT #@domain.com"로 저장 되는 경우가 많습니다. userPrincipalName이 특성 매핑에 원본 특성으로 포함 된 경우에는 #EXT #가 userPrincipalName에서 제거 됩니다. #EXT #을 (를) 표시 해야 하는 경우에는 userPrincipalName을 originalUserPrincipalName로 원본 특성으로 바꿉니다. 

## <a name="provisioning-cycles-initial-and-incremental"></a>프로 비전 주기: 초기 및 증분

Azure AD가 소스 시스템인 경우 프로비저닝 서비스는 [Azure AD Graph API의 차등 쿼리 기능](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)을 사용하여 사용자와 그룹을 모니터링합니다. 프로 비전 서비스는 원본 시스템 및 대상 시스템에 대해 초기 주기를 실행 한 다음 주기적 증분 주기를 실행 합니다.

### <a name="initial-cycle"></a>초기 주기

프로 비전 서비스가 시작 되 면 첫 번째 주기는 다음을 수행 합니다.

1. [특성 매핑](customize-application-attributes.md)에 정의된 모든 특성을 검색하여 소스 시스템에서 모든 사용자와 그룹을 쿼리합니다.

2. 구성된 [할당](../manage-apps/assign-user-or-group-access-portal.md) 또는 [특성 기반 범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)를 사용하여 반환된 사용자 및 그룹을 필터링합니다.

3. 사용자가 할당 되거나 프로 비전 범위에 있는 경우 서비스는 지정 된 [일치 특성](customize-application-attributes.md#understanding-attribute-mapping-properties)을 사용 하 여 대상 시스템에 일치 하는 사용자를 쿼리 합니다. 예: 소스 시스템의 userPrincipal 이름이 일치하는 특성이고 대상 시스템의 userName에 매핑되는 경우 프로비저닝 서비스는 대상 시스템에서 소스 시스템의 userPrincipal 이름 값과 일치하는 userNames를 쿼리합니다.

4. 대상 시스템에서 일치 하는 사용자를 찾을 수 없는 경우 소스 시스템에서 반환 된 특성을 사용 하 여 생성 됩니다. 사용자 계정을 만든 후 프로 비전 서비스는 새 사용자에 대 한 대상 시스템의 ID를 검색 하 고 캐시 합니다. 이 ID는 해당 사용자에 대해 앞으로 수행 되는 모든 작업을 실행 하는 데 사용 됩니다.

5. 일치 하는 사용자가 있는 경우 원본 시스템에서 제공 하는 특성을 사용 하 여 업데이트 됩니다. 사용자 계정이 일치 한 후 프로 비전 서비스는 새 사용자에 대 한 대상 시스템의 ID를 검색 하 고 캐시 합니다. 이 ID는 해당 사용자에 대해 앞으로 수행 되는 모든 작업을 실행 하는 데 사용 됩니다.

6. 특성 매핑에 "참조" 특성이 포함 되어 있으면 서비스는 대상 시스템에서 추가 업데이트를 수행 하 여 참조 된 개체를 만들고 연결 합니다. 예를 들어 대상 시스템에 있는 한 사용자의 “Manager” 특성이 대상 시스템에서 생성된 다른 사용자에 연결되어 있을 수 있습니다.

7. 이후 증분 주기에 대 한 시작 지점을 제공 하는 초기 주기 끝에 워터 마크를 유지 합니다.

ServiceNow, G Suite 및 Box와 같은 일부 응용 프로그램은 사용자를 프로 비전 할 뿐만 아니라 그룹과 해당 구성원도 프로 비전 할 수 있습니다. 이러한 경우 [매핑에서](customize-application-attributes.md)그룹 프로 비전이 사용 하도록 설정 된 경우 프로 비전 서비스는 사용자와 그룹을 동기화 한 다음 나중에 그룹 멤버 자격을 동기화 합니다.

### <a name="incremental-cycles"></a>증분 주기

초기 주기 후 다른 모든 주기는 다음을 수행 합니다.

1. 소스 시스템에서 마지막 워터마크가 저장된 이후 업데이트된 사용자 및 그룹을 쿼리합니다.

2. 구성된 [할당](../manage-apps/assign-user-or-group-access-portal.md) 또는 [특성 기반 범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)를 사용하여 반환된 사용자 및 그룹을 필터링합니다.

3. 사용자가 할당 되거나 프로 비전 범위에 있는 경우 서비스는 지정 된 [일치 특성](customize-application-attributes.md#understanding-attribute-mapping-properties)을 사용 하 여 대상 시스템에 일치 하는 사용자를 쿼리 합니다.

4. 대상 시스템에서 일치 하는 사용자를 찾을 수 없는 경우 소스 시스템에서 반환 된 특성을 사용 하 여 생성 됩니다. 사용자 계정을 만든 후 프로 비전 서비스는 새 사용자에 대 한 대상 시스템의 ID를 검색 하 고 캐시 합니다. 이 ID는 해당 사용자에 대해 앞으로 수행 되는 모든 작업을 실행 하는 데 사용 됩니다.

5. 일치 하는 사용자가 있는 경우 원본 시스템에서 제공 하는 특성을 사용 하 여 업데이트 됩니다. 새로 할당 된 계정인 경우 프로 비전 서비스는 새 사용자에 대 한 대상 시스템의 ID를 검색 하 고 캐시 합니다. 이 ID는 해당 사용자에 대해 앞으로 수행 되는 모든 작업을 실행 하는 데 사용 됩니다.

6. 특성 매핑에 "참조" 특성이 포함 되어 있으면 서비스는 대상 시스템에서 추가 업데이트를 수행 하 여 참조 된 개체를 만들고 연결 합니다. 예를 들어 대상 시스템에 있는 한 사용자의 “Manager” 특성이 대상 시스템에서 생성된 다른 사용자에 연결되어 있을 수 있습니다.

7. 이전에 프로 비전 범위에 있던 사용자가 할당 되지 않은 경우를 비롯 하 여 범위에서 제거 되는 경우 서비스는 업데이트를 통해 대상 시스템에서 사용자를 사용 하지 않도록 설정 합니다.

8. 이전에 프로비저닝 범위에 있던 사용자가 소스 시스템에서 해제 또는 일시 삭제되면 서비스는 업데이트를 통해 대상 시스템에서 사용자를 해제합니다.

9. 이전에 프로비저닝 범위에 있던 사용자가 소스 시스템에서 영구 삭제되면 서비스는 대상 시스템에서 사용자를 삭제합니다. Azure AD에서 사용자는 일시 삭제 된 후 30 일 후에 하드 삭제 됩니다.

10. 이후 증분 주기에 대 한 시작점을 제공 하는 증분 주기의 끝에 새 워터 마크를 유지 합니다.

> [!NOTE]
> 필요에 따라 [매핑](customize-application-attributes.md) 섹션에서 **대상 개체 작업** 확인란을 사용 하 여 **만들기**, **업데이트**또는 **삭제** 작업을 비활성화할 수 있습니다. 업데이트 중에 사용자를 해제하는 논리도 “accountEnabled”와 같은 필드의 특성 매핑을 통해 제어됩니다.

프로 비전 서비스는 [각 응용 프로그램에 특정 한 자습서](../saas-apps/tutorial-list.md)에 정의 된 간격으로 다시 백 엔드 증분 주기를 계속 실행 합니다. 증분 주기는 다음 이벤트 중 하나가 발생할 때까지 계속 됩니다.

- 서비스가 Azure Portal을 사용하여 수동으로 중지되거나 적절한 Graph API 명령을 사용하여 중지됩니다. 
- Azure Portal에서 **Clear state 및 restart** 옵션을 사용 하거나 적절 한 Graph API 명령을 사용 하 여 새로운 초기 순환이 트리거됩니다. 이 작업을 수행 하면 저장 된 워터 마크가 모두 지워지며 모든 소스 개체가 다시 평가 됩니다.
- 특성 매핑 또는 범위 지정 필터의 변경으로 인해 새 초기 순환이 트리거됩니다. 이 작업을 수행 하면 저장 된 워터 마크가 모두 지워지므로 모든 원본 개체가 다시 평가 됩니다.
- 높은 오류 비율로 인해 프로 비전 프로세스가 격리 (아래 참조) 되 고 4 주 넘게 격리 상태로 유지 됩니다. 이 이벤트가 발생하면 자동으로 서비스를 사용할 수 없게 됩니다.

### <a name="errors-and-retries"></a>오류 및 다시 시도

대상 시스템에 오류가 발생 하 여 대상 시스템에서 개별 사용자가 추가, 업데이트 또는 삭제 되지 않은 경우 다음 동기화 주기에서 작업을 다시 시도 합니다. 사용자가 계속 실패하면 감소된 빈도로 다시 시도되기 시작하여, 점차 하루에 한 번만 시도되는 빈도까지 축소됩니다. 오류를 해결 하려면 관리자가 [프로 비전 로그](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 를 확인 하 여 근본 원인을 확인 하 고 적절 한 조치를 취해야 합니다. 일반적인 오류는 다음과 같습니다.

- 대상 시스템에서 필요한 특성이 사용자의 소스 시스템에 채워져 있지 않음
- 대상 시스템에 unique 제약 조건이 있고 다른 사용자 레코드에 동일한 값이 있는 원본 시스템에 특성 값이 있는 사용자

원본 시스템에서 영향을 받는 사용자에 대 한 특성 값을 조정 하거나 충돌을 발생 시 키 지 않도록 특성 매핑을 조정 하 여 이러한 오류를 해결 합니다.

### <a name="quarantine"></a>격리

오류 (예: 잘못 된 관리자 자격 증명)로 인해 대상 시스템에 대해 수행 되는 대부분의 호출이 일관 되 게 실패 하는 경우 프로 비전 작업은 "격리" 상태가 됩니다. 이 상태는 Azure Portal에서 전자 메일 알림이 구성 된 경우 [프로 비전 요약 보고서](../manage-apps/check-status-user-account-provisioning.md) 와 전자 메일을 통해 표시 됩니다.

격리에서 증분 주기의 빈도는 하루에 한 번만 점차적으로 줄어듭니다.

모든 잘못 된 오류를 수정 하 고 다음 동기화 주기를 시작 하면 프로 비전 작업은 격리를 종료 합니다. 프로비저닝 작업이 4주 넘게 격리 상태로 유지되면 프로비저닝 작업을 사용할 수 없게 됩니다. [여기에서 격리 상태에](../manage-apps/application-provisioning-quarantine-status.md)대해 자세히 알아보세요.

### <a name="how-long-provisioning-takes"></a>프로비저닝 소요 시간

성능은 프로 비전 작업에서 초기 프로 비전 주기 또는 증분 주기를 실행 하 고 있는지 여부에 따라 달라 집니다. 프로 비전에 소요 되는 시간 및 프로 비전 서비스의 상태를 모니터링 하는 방법에 대 한 자세한 내용은 [사용자 프로 비전 상태 확인](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)을 참조 하세요.

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>사용자가 올바르게 프로 비전 되는지 확인 하는 방법

사용자 프로 비전 서비스에 의해 실행 되는 모든 작업은 Azure AD [프로 비전 로그 (미리 보기)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)에 기록 됩니다. 로그에는 원본 및 대상 시스템에 대 한 모든 읽기 및 쓰기 작업과 각 작업 중에 읽거나 쓴 사용자 데이터가 포함 됩니다. Azure Portal에서 프로 비전 로그를 읽는 방법에 대 한 자세한 내용은 [프로 비전 보고 가이드](../manage-apps/check-status-user-account-provisioning.md)를 참조 하세요.

## <a name="de-provisioning"></a>프로 비전 해제

Azure AD 프로 비전 서비스는 사용자가 더 이상 액세스 하지 않아야 하는 경우 프로 비전 취소 계정으로 원본 및 대상 시스템을 동기화 상태로 유지 합니다. 

Azure AD 프로 비전 서비스는 응용 프로그램이 suupports 일시 삭제 (활성 = false로 업데이트 요청) 되 고 다음 이벤트 중 하나가 발생할 때 응용 프로그램에서 사용자를 일시 삭제 합니다.

* Azure AD에서 사용자 계정이 삭제 됨
*   사용자는 응용 프로그램에서 할당 해제 됩니다.
*   사용자가 더 이상 범위 지정 필터를 충족 하지 않고 범위를 벗어났습니다.
    * 기본적으로 Azure AD 프로 비전 서비스는 범위를 벗어난 사용자를 일시적으로 삭제 하거나 사용 하지 않도록 설정 합니다. 이 기본 동작을 재정의 하려는 경우 [범위를 벗어난 삭제를 건너뛰도록](../app-provisioning/skip-out-of-scope-deletions.md)플래그를 설정할 수 있습니다.
*   AccountEnabled 속성은 False로 설정 됩니다.

위의 4 개 이벤트 중 하나가 발생 하 고 대상 응용 프로그램에서 일시 삭제를 지원 하지 않는 경우 프로 비전 서비스는 앱에서 사용자를 영구적으로 삭제 하는 삭제 요청을 보냅니다. 

사용자가 Azure AD에서 삭제 되 고 30 일 후에는 테 넌 트에서 영구적으로 삭제 됩니다. 이 시점에서 프로 비전 서비스는 응용 프로그램에서 사용자를 영구적으로 삭제 하는 삭제 요청을 보냅니다. 30 일 기간 동안 언제 든 지 [사용자를 영구적으로 삭제](../fundamentals/active-directory-users-restore.md)하 여 응용 프로그램에 삭제 요청을 보낼 수 있습니다.

특성 매핑에서 Issoft Deleted 특성이 표시 되는 경우 사용자의 상태를 확인 하는 데 사용 되 고 활성 = false로 업데이트 요청을 보낼지 여부를 지정 하 여 사용자를 일시 삭제 합니다. 

## <a name="next-steps"></a>다음 단계

[자동 사용자 프로 비전 배포 계획](../app-provisioning/plan-auto-user-provisioning.md)

[갤러리 앱에 대 한 프로 비전 구성](../manage-apps/configure-automatic-user-provisioning-portal.md)

[사용자 고유의 앱을 만들 때 SCIM 끝점을 빌드하고 프로 비전 구성](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[응용 프로그램에 사용자를 구성 및 프로 비전 하는 문제를 해결](../manage-apps/application-provisioning-config-problem.md)합니다.
