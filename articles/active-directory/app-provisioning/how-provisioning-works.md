---
title: Azure AD 프로비저닝 작동 방법의 이해 | Microsoft Docs
description: Azure AD 프로비저닝 작동 방법의 이해
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 05/20/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 69ea1964449143a25f447375f2aae15d9feeff10
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235726"
---
# <a name="how-provisioning-works"></a>프로비저닝 작동 방법

자동 프로비저닝은 사용자가 액세스해야 하는 클라우드 애플리케이션에서 사용자 ID와 역할을 만드는 것을 가리킵니다. 자동 프로비저닝에는 사용자 ID를 생성하는 것 외에도 상태 또는 역할이 변경될 때 사용자 ID의 유지 관리 및 제거가 포함됩니다. 배포를 시작하기 전에 이 문서를 참고하여 Azure AD 프로비저닝의 작동 방법을 살펴보고 구성 권장 사항을 확인하세요. 

**Azure AD 프로비저닝 서비스**는 애플리케이션 벤더에서 제공하는 SCIM(System for Cross-Domain Identity Management) 2.0 사용자 관리 API 엔드포인트에 연결하여 SaaS 앱과 기타 시스템으로 사용자를 프로비저닝합니다. 이러한 SCIM 엔드포인트를 사용하여 Azure AD에서 프로그래밍 방식으로 사용자를 만들고, 업데이트하고, 제거할 수 있습니다. 선택한 애플리케이션의 경우 프로비저닝 서비스는 그룹 및 역할과 같은 추가 ID 관련 개체를 만들고, 업데이트하고, 제거할 수 있습니다. Azure AD와 애플리케이션 사이에서 프로비저닝용으로 사용되는 채널은 HTTPS TLS 1.2 암호화를 사용하여 암호화됩니다.


![Azure AD 프로비저닝 서비스](./media/how-provisioning-works/provisioning0.PNG)
*그림 1: Azure AD Provisioning Service

![아웃바운드 사용자 프로비저닝 워크플로](./media/how-provisioning-works/provisioning1.PNG)
*그림 2: Azure AD에서 인기 있는 SaaS 애플리케이션으로의 “아웃바운드” 사용자 프로비저닝 워크플로

![인바운드 사용자 프로비저닝 워크플로](./media/how-provisioning-works/provisioning2.PNG)
*그림 3: 인기 있는 HCM(Human Capital Management) 애플리케이션에서 Azure Active Directory 및 Windows Server Active Directory로의 “인바운드”사용자 프로비저닝 워크플로

## <a name="provisioning-using-scim-20"></a>SCIM 2.0을 사용한 프로비저닝

Azure AD 프로비저닝 서비스는 자동 프로비저닝을 위해 [SCIM 2.0 프로토콜](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/bg-p/IdentityStandards)을 사용합니다. 이 서비스는 애플리케이션의 SCIM 엔드포인트에 연결하고 SCIM 사용자 개체 스키마 및 REST API를 사용하여 사용자와 그룹의 프로비저닝 및 프로비저닝 해제를 자동화합니다. Azure AD 갤러리에 있는 대부분의 애플리케이션에 대해 SCIM 기반 프로비저닝 커넥터가 제공됩니다. 개발자는 Azure AD용 앱을 빌드할 때 SCIM 2.0 사용자 관리 API를 사용하여, 프로비저닝을 위해 Azure AD와 통합되는 SCIM 엔드포인트를 빌드할 수 있습니다. 자세한 내용은 [SCIM 엔드포인트 빌드 및 사용자 프로비저닝 구성](../app-provisioning/use-scim-to-provision-users-and-groups.md)을 참조하세요.

현재 자동 Azure AD 프로비저닝 커넥터가 없는 앱에 대해 커넥터를 요청하려면 [Azure Active Directory 애플리케이션 요청](https://aka.ms/aadapprequest)을 작성합니다.

## <a name="authorization"></a>권한 부여

Azure AD가 애플리케이션의 사용자 관리 API에 연결하려면 자격 증명이 필요합니다. 애플리케이션에 대해 자동 사용자 프로비저닝을 구성할 때는 유효한 자격 증명을 입력해야 합니다. 앱 자습서에서 애플리케이션의 자격 증명 유형과 요구 사항을 확인할 수 있습니다. Azure Portal에서, 제공된 자격 증명을 사용하여 Azure AD에서 해당 앱의 프로비전닝 앱에 연결해 봄으로써 자격 증명을 테스트할 수 있습니다.

애플리케이션에 대해 SAML 기반 Single Sign-On이 구성된 경우 Azure AD의 내부 애플리케이션당 스토리지 용량 한도는 1024바이트입니다. 이 한도에는 애플리케이션의 단일 인스턴스와 연결된 모든 인증서, 비밀 토큰, 자격 증명 및 관련 구성 데이터(Azure AD의 서비스 주체 레코드라도고 함)를 포함합니다. SAML 기반 Single Sign-On을 구성할 때 SAML 토큰에 서명하는 데 사용되는 인증서가 종종 50%를 초과하는 공간을 사용합니다. 사용자 프로비저닝을 설정하는 동안 입력하는 기타 항목(비밀 토큰, URI, 알림 메일 주소, 사용자 이름 및 암호)으로 인해 스토리지 용량 한도가 초과될 수 있습니다. 자세한 내용은 [사용자 프로비저닝 갤러리 앱을 구성하는 동안 관리자 자격 증명을 저장하는 문제](./application-provisioning-config-problem-storage-limit.md)를 참조하세요.

## <a name="mapping-attributes"></a>매핑 특성

타사 SaaS 애플리케이션에 대해 사용자 프로비저닝을 사용하도록 설정할 경우 Azure Portal은 해당 특성 값을 특성 매핑으로 제어합니다. 매핑은 사용자 계정이 프로비저닝되거나 업데이트될 때 Azure AD와 대상 애플리케이션 간에 흐르는 사용자 특성을 결정합니다.

Azure AD 사용자 개체와 각 SaaS 앱의 사용자 개체 사이에는 미리 구성된 특성 및 특성 매핑 세트가 있습니다. 일부 앱은 사용자 외에도 그룹 같은 다른 유형의 개체를 관리합니다.

프로비저닝을 설정할 때는 Azure AD에서 애플리케이션으로 이동하는 사용자(또는 그룹) 속성을 정의하는 특성 매핑 및 워크플로를 검토하고 구성하는 것이 중요합니다. 두 시스템 간 사용자/그룹을 고유하게 식별하고 일치시키는 데 사용되는 일치하는 속성(**이 특성을 사용하여 개체 일치**)을 검토 및 구성하세요.

비즈니스 요구 사항에 따라 기본 특성 매핑을 사용자 지정할 수 있습니다. 따라서 기존의 특성 매핑을 변경 또는 삭제하거나 새 특성 매핑을 만들 수 있습니다. 자세한 내용은 [SaaS 애플리케이션에 대한 사용자 프로비저닝 특성 매핑 사용자 지정](./customize-application-attributes.md)을 참조하세요.

SaaS 애플리케이션에 프로비전을 구성하면 식 매핑은 지정할 수 있는 특성 매핑의 유형 중 하나입니다. 이러한 매핑의 경우, 사용자의 데이터를 SaaS 애플리케이션에 대해 사용하는 형식으로 변환할 수 있는 스크립트 방식의 식을 작성해야 합니다. 자세한 내용은 [특성 매핑에 대한 식 작성](functions-for-customizing-application-data.md)을 참조하세요.

## <a name="scoping"></a>범위 지정 
### <a name="assignment-based-scoping"></a>할당 기반 범위 지정

Azure AD에서 SaaS 애플리케이션으로의 아웃바운드 프로비저닝의 경우, [사용자 및 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)은 프로비저닝 범위에 포함될 사용자를 결정하는 가장 일반적인 방법입니다. 이러한 할당은 Single Sign-On 활성화에도 사용되므로 액세스와 프로비저닝을 관리하기 위해 동일한 방법을 사용할 수 있습니다. 할당 기반 범위 지정은 Workday 및 Successfactors와 같은 인바운드 프로비저닝이 시나리오에는 적용되지 않습니다.

* **그룹.** Azure AD Premium 라이선스 플랜에서는 그룹을 사용해 SaaS 애플리케이션에 대한 액세스 권한을 할당할 수 있습니다. 그런 다음 프로비저닝 범위를 **할당된 사용자 및 그룹만 동기화**로 설정하면 Azure AD 프로비저닝 서비스는 사용자가 애플리케이션에 할당된 그룹의 멤버인지 여부에 따라 사용자를 프로비저닝하거나 프로비저닝 해제합니다. 그룹 개체 자체는 애플리케이션이 그룹 개체를 지원하지 않는 한 프로비저닝되지 않습니다. 애플리케이션에 할당된 그룹에서 “SecurityEnabled” 속성이 “True”로 설정되어 있는지 확인합니다.

* **동적 그룹.** Azure AD 사용자 프로비저닝 서비스는 [동적 그룹](../users-groups-roles/groups-create-rule.md)에 있는 사용자를 읽고 프로비저닝할 수 있습니다. 다음과 같은 주의 사항 및 권장 사항에 유의하세요.

  * 동적 그룹은 Azure AD에서 SaaS 애플리케이션으로의 엔드투엔드 프로비저닝의 전체 성능에 영향을 줄 수 있습니다.

  * 동적 그룹의 사용자가 SaaS 애플리케이션에서 프로비저닝 또는 프로비저닝 해제되는 속도는 동적 그룹이 멤버 자격 변경을 평가하는 속도에 따라 좌우됩니다. 동적 그룹의 처리 상태를 확인하는 방법에 대한 내용은 [멤버 관리 규칙에 대한 처리 상태 확인](../users-groups-roles/groups-create-rule.md)을 참조하세요.

  * 사용자가 동적 그룹의 멤버 자격을 잃은 경우 이는 프로비저닝 해제 이벤트로 간주됩니다. 동적 그룹에 대한 규칙을 만들 때 이 시나리오를 고려하세요.

* **중첩 그룹.** Azure AD 사용자 프로비저닝 서비스는 중첩된 그룹에 있는 사용자를 읽거나 프로비저닝할 수 없습니다. 서비스는 명시적으로 할당된 그룹의 직계 멤버인 사용자만 읽고 프로비저닝할 수 있습니다. 이것은 Single Sign-On에도 영향을 미치는 “애플리케이션에 대한 그룹 기반 할당”의 제한 사항입니다([그룹을 사용하여 SaaS 애플리케이션에 대한 액세스 관리](../users-groups-roles/groups-saasapps.md) 참조). 대신 프로비저닝해야 하는 사용자를 포함하는 그룹을 직접 할당하거나 그 밖의 방식으로 [범위 지정](define-conditional-rules-for-provisioning-user-accounts.md)해야 합니다.

### <a name="attribute-based-scoping"></a>특성 기반 범위 지정 

범위 지정 필터를 사용하여 어떤 사용자를 애플리케이션에 프로비저닝할지 결정하는 특성 기반 규칙을 정의할 수 있습니다. 이 방법은 일반적으로 HCM 애플리케이션에서 Azure AD 및 Active Directory로의 인바운드 프로비저닝에 사용됩니다. 범위 지정 필터는 각 Azure AD 사용자 프로비전 커넥터에 대해 특성 매핑의 일부로 구성됩니다. 특성 기반 범위 지정 필터를 구성하는 방법에 대한 자세한 내용은 [범위 지정 필터를 사용한 특성 기반 애플리케이션 프로비저닝](define-conditional-rules-for-provisioning-user-accounts.md)을 참조하세요.

### <a name="b2b-guest-users"></a>B2B(게스트) 사용자

Azure AD 사용자 프로비저닝 서비스를 사용하여 Azure AD의 B2B(또는 게스트) 사용자를 SaaS 애플리케이션으로 프로비저닝할 수 있습니다. 그러나 B2B 사용자가 Azure AD를 사용하여 SaaS 애플리케이션에 로그인하려면 SaaS 애플리케이션의 SAML 기반 Single Sign-On 기능을 특정 방식으로 구성해야 합니다. B2B 사용자의 로그인을 지원하도록 SaaS 애플리케이션을 구성하는 방법에 대한 자세한 내용은 [B2B 협업을 위한 SaaS 앱 구성](../external-identities/configure-saas-apps.md)을 참조하세요.

게스트 사용자의 userPrincipalName은 보통 “alias#EXT#@domain.com”으로 저장되는 경우가 많습니다. userPrincipalName이 특성 매핑에 원본 특성으로 포함된 경우 #EXT#이 userPrincipalName에서 제거됩니다. #EXT#을 포함해야 하는 경우에는 userPrincipalName을 원본 특성과 동일한 originalUserPrincipalName으로 바꾸세요. 

## <a name="provisioning-cycles-initial-and-incremental"></a>프로비저닝 주기: 초기 및 증분

Azure AD가 원본 시스템일 경우 프로비저닝 서비스는 [델타 쿼리를 사용하여 Microsoft Graph 데이터의 변경 내용 추적](/graph/delta-query-overview)으로 사용자 및 그룹을 모니터링합니다. 프로비저닝 서비스는 원본 시스템 및 대상 시스템에 대해 초기 주기를 실행한 다음, 주기적 증분 주기를 실행합니다.

### <a name="initial-cycle"></a>초기 주기

프로비저닝 서비스가 시작되면 첫 번째 주기는 다음을 수행합니다.

1. [특성 매핑](customize-application-attributes.md)에 정의된 모든 특성을 검색하여 소스 시스템에서 모든 사용자와 그룹을 쿼리합니다.

2. 구성된 [할당](../manage-apps/assign-user-or-group-access-portal.md) 또는 [특성 기반 범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)를 사용하여 반환된 사용자 및 그룹을 필터링합니다.

3. 사용자가 할당되었거나 프로비저닝 범위에 있는 경우 서비스는 지정된 [일치 특성](customize-application-attributes.md#understanding-attribute-mapping-properties)을 사용하여 대상 시스템에서 일치하는 사용자를 쿼리합니다. 예제: 원본 시스템의 userPrincipal 이름이 일치하는 특성이고 대상 시스템의 userName에 매핑되는 경우, 프로비전 서비스는 대상 시스템에서 원본 시스템의 userPrincipal 이름 값과 일치하는 userNames를 쿼리합니다.

4. 대상 시스템에 일치하는 사용자가 없으면 원본 시스템에서 반환된 특성을 사용하여 생성됩니다. 사용자 계정이 생성되면 프로비저닝 서비스는 새 사용자의 대상 시스템 ID를 검색 및 캐시합니다. 이 ID는 해당 사용자에 대한 모든 후속 작업을 수행하는 데 사용됩니다.

5. 일치하는 사용자가 있으면 원본 시스템에서 제공하는 특성을 사용하여 업데이트됩니다. 사용자 계정이 일치되면 프로비저닝 서비스는 새 사용자의 대상 시스템 ID를 검색 및 캐시합니다. 이 ID는 해당 사용자에 대한 모든 후속 작업을 수행하는 데 사용됩니다.

6. 특성 매핑에 “참조” 특성이 포함되어 있을 경우 서비스는 대상 시스템에서 추가 업데이트를 수행하여 참조된 개체를 만들고 연결합니다. 예를 들어 대상 시스템에 있는 한 사용자의 “Manager” 특성이 대상 시스템에서 생성된 다른 사용자에 연결되어 있을 수 있습니다.

7. 초기 주기가 끝나면 워터마크를 저장합니다. 이 워터마크는 후속 증분 주기의 시작점을 제공합니다.

ServiceNow, G Suite, Box 등의 일부 애플리케이션은 사용자 프로비저닝뿐 아니라 그룹 및 해당 멤버 프로비저닝도 지원합니다. 이 경우 [매핑](customize-application-attributes.md)에서 그룹 프로비저닝을 사용하도록 설정하면 프로비저닝 서비스에서 사용자와 그룹을 동기화한 다음, 그룹 멤버 자격을 동기화합니다.

### <a name="incremental-cycles"></a>증분 주기

다른 모든 주기도 초기 주기와 마찬가지로 다음을 수행합니다.

1. 소스 시스템에서 마지막 워터마크가 저장된 이후 업데이트된 사용자 및 그룹을 쿼리합니다.

2. 구성된 [할당](../manage-apps/assign-user-or-group-access-portal.md) 또는 [특성 기반 범위 지정 필터](define-conditional-rules-for-provisioning-user-accounts.md)를 사용하여 반환된 사용자 및 그룹을 필터링합니다.

3. 사용자가 할당되었거나 프로비저닝 범위에 있는 경우 서비스는 지정된 [일치 특성](customize-application-attributes.md#understanding-attribute-mapping-properties)을 사용하여 대상 시스템에서 일치하는 사용자를 쿼리합니다.

4. 대상 시스템에 일치하는 사용자가 없으면 원본 시스템에서 반환된 특성을 사용하여 생성됩니다. 사용자 계정이 생성되면 프로비저닝 서비스는 새 사용자의 대상 시스템 ID를 검색 및 캐시합니다. 이 ID는 해당 사용자에 대한 모든 후속 작업을 수행하는 데 사용됩니다.

5. 일치하는 사용자가 있으면 원본 시스템에서 제공하는 특성을 사용하여 업데이트됩니다. 일치하는 새로 할당된 계정인 경우, 프로비저닝 서비스는 새 사용자의 대상 시스템 ID를 검색 및 캐시합니다. 이 ID는 해당 사용자에 대한 모든 후속 작업을 수행하는 데 사용됩니다.

6. 특성 매핑에 “참조” 특성이 포함되어 있을 경우 서비스는 대상 시스템에서 추가 업데이트를 수행하여 참조된 개체를 만들고 연결합니다. 예를 들어 대상 시스템에 있는 한 사용자의 “Manager” 특성이 대상 시스템에서 생성된 다른 사용자에 연결되어 있을 수 있습니다.

7. 이전에 프로비저닝 범위에 있던 사용자가 범위에서 제거되면(할당 취소되는 경우 포함), 서비스는 업데이트를 통해 대상 시스템에서 사용자를 해제합니다.

8. 이전에 프로비저닝 범위에 있던 사용자가 소스 시스템에서 해제 또는 일시 삭제되면 서비스는 업데이트를 통해 대상 시스템에서 사용자를 해제합니다.

9. 이전에 프로비저닝 범위에 있던 사용자가 소스 시스템에서 영구 삭제되면 서비스는 대상 시스템에서 사용자를 삭제합니다. Azure AD에서 사용자는 일시 삭제된 날부터 30일 후에 영구 삭제됩니다.

10. 증분 주기가 끝나면 새 워터마크를 저장합니다. 이 워터마크는 후속 증분 주기의 시작점을 제공합니다.

> [!NOTE]
> 필요에 따라 [매핑](customize-application-attributes.md) 섹션의 **대상 개체 작업** 확인란을 사용하여 **만들기**, **업데이트** 또는 **삭제** 작업을 사용하지 않도록 설정할 수 있습니다. 업데이트 중에 사용자를 해제하는 논리도 “accountEnabled”와 같은 필드의 특성 매핑을 통해 제어됩니다.

프로비저닝 서비스는 [각 애플리케이션과 관련된 자습서](../saas-apps/tutorial-list.md)에 정의된 간격마다 연속 증분 주기를 무기한 실행합니다. 증분 주기는 다음과 같은 이벤트 중 하나가 발생할 때까지 실행됩니다.

- 서비스가 Azure Portal을 사용하여 수동으로 중지되거나 적절한 Microsoft Graph API 명령을 사용하여 중지됩니다.
- Azure Portal의 **상태 지우기 및 다시 시작** 옵션을 사용하거나 적절한 Microsoft Graph API 명령을 사용하여 새 초기 주기가 트리거됩니다. 이 작업은 저장된 워터마크를 모두 지우고 모든 원본 개체를 다시 평가합니다.
- 특성 매핑 또는 범위 지정 필터가 변경되어 새 초기 주기가 트리거됩니다. 이 작업도 저장된 워터마크를 모두 지우고 모든 원본 개체를 다시 평가합니다.
- 높은 오류 비율로 인해 프로비저닝 프로세스가 격리(아래 참조) 상태로 전환되고 4주 넘게 격리 상태로 유지됩니다. 이 이벤트가 발생하면 자동으로 서비스를 사용할 수 없게 됩니다.

### <a name="errors-and-retries"></a>오류 및 다시 시도

대상 시스템의 오류로 인해 대상 시스템에서 개별 사용자를 추가, 업데이트 또는 삭제할 수 없는 경우 다음 동기화 주기에서 작업이 다시 시도됩니다. 사용자가 계속 실패하면 감소된 빈도로 다시 시도되기 시작하여, 점차 하루에 한 번만 시도되는 빈도까지 축소됩니다. 오류를 해결하려면 관리자가 [프로비저닝 로그](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)를 확인하여 근본 원인을 파악하고 적절한 조치를 수행해야 합니다. 일반적인 오류는 다음과 같습니다.

- 대상 시스템에서 필요한 특성이 사용자의 소스 시스템에 채워져 있지 않음
- 대상 시스템에서 UNIQUE 제약 조건을 가진 특성 값이 사용자의 원본 시스템에 있고 다른 사용자 레코드에 동일한 값이 있음

이러한 오류는 원본 시스템에서 영향을 받는 사용자의 특성 값을 조정하거나 충돌이 발생하지 않도록 특성 매핑을 조정하여 해결할 수 있습니다.

### <a name="quarantine"></a>격리

대상 시스템에 대한 호출이 오류(예: 잘못된 관리자 자격 증명)로 인해 대부분 또는 모두 일관되게 실패하는 경우 프로비저닝 작업이 “격리” 상태로 전환됩니다. 이 상태는 [프로비저닝 요약 보고서](./check-status-user-account-provisioning.md) 및 Azure Portal에서 메일 알림이 구성된 경우 메일을 통해 알 수 있습니다.

격리 중에 증분 주기 빈도는 하루에 한 번까지 점차 감소됩니다.

위반 오류를 모두 수정하고 다음 동기화 주기가 시작되면 프로비저닝 작업이 격리에서 제거됩니다. 프로비저닝 작업이 4주 넘게 격리 상태로 유지되면 프로비저닝 작업을 사용할 수 없게 됩니다. 격리 상태에 대한 자세한 내용은 [여기](./application-provisioning-quarantine-status.md)를 참조하세요.

### <a name="how-long-provisioning-takes"></a>프로비저닝 소요 시간

성능은 대로 프로비저닝 작업이 초기 프로비저닝 주기와 증분 주기 중 어느 것을 실행하는지에 따라 달라집니다. 프로비저닝 소요 시간 및 프로비저닝 서비스 상태를 모니터링하는 방법은 [사용자 프로비저닝 상태 확인](application-provisioning-when-will-provisioning-finish-specific-user.md)을 참조하세요.

### <a name="how-to-tell-if-users-are-being-provisioned-properly"></a>사용자가 올바르게 프로비저닝되었는지 확인하는 방법

사용자 프로비저닝 서비스에서 실행하는 모든 작업은 Azure AD [프로비저닝 로그(미리 보기)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)에 기록됩니다. 로그에는 원본 및 대상 시스템에 만들어진 모든 읽기 및 쓰기 작업과 각 작업 동안 읽거나 쓴 사용자 데이터가 포함됩니다. Azure Portal에서 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [프로비저닝 보고 가이드](./check-status-user-account-provisioning.md)를 참조하세요.

## <a name="de-provisioning"></a>프로비저닝 해제

Azure AD 프로비저닝 서비스는 사용자가 더 이상 액세스 권한을 갖지 않아야 할 경우 계정을 프로비저닝 해제함으로써 원본 및 대상 시스템을 동기화합니다. 

Azure AD 프로 비전 서비스는 응용 프로그램에서 일시 삭제 (활성 = false로 업데이트 요청)를 지원할 때 응용 프로그램에서 사용자를 일시 삭제 하며 다음 이벤트 중 하나가 발생 합니다.

* Azure AD에서 사용자 계정이 삭제됨
*   애플리케이션에서 사용자가 할당 취소됨
*   사용자가 더 이상 범위 지정 필터를 충족하지 않고 범위를 벗어남
    * 기본적으로 Azure AD 프로비저닝 서비스는 범위를 벗어나는 사용자를 일시 삭제하거나 비활성화합니다. 이 기본 동작을 재정의하려면 [범위 외 삭제 건너뛰기](../app-provisioning/skip-out-of-scope-deletions.md) 플래그를 설정할 수 있습니다.
*   AccountEnabled 속성이 False로 설정된 경우

위의 4개 이벤트 중 하나가 발생하고 대상 애플리케이션에서 일시 삭제를 지원하지 않는 경우 프로비저닝 서비스는 DELETE 요청을 보내서 앱에서 사용자를 영구 삭제합니다. 

사용자가 Azure AD에서 삭제되고 30일이 지나면 테넌트에서 영구 삭제됩니다. 이 시점에서 프로비저닝 서비스는 DELETE 요청을 보내서 애플리케이션에서 사용자를 영구 삭제합니다. 30일 기간 중 언제든지 [사용자를 수동으로 삭제](../fundamentals/active-directory-users-restore.md)할 수 있으며, 이렇게 하면 애플리케이션으로 삭제 요청이 전송됩니다.

특성 매핑에 IsSoftDeleted 특성이 있는 경우, 이 특성은 사용자의 상태를 확인하고 active = false 업데이트 요청을 보내서 사용자를 일시 삭제할지 여부를 결정하는 데 사용됩니다. 

## <a name="next-steps"></a>다음 단계

[자동 사용자 프로비저닝 배포 계획](../app-provisioning/plan-auto-user-provisioning.md)

[갤러리 앱 프로비저닝 구성](./configure-automatic-user-provisioning-portal.md)

[사용자 고유의 앱을 만들 때 SCIM 엔드포인트 빌드 및 프로비저닝 구성](../app-provisioning/use-scim-to-provision-users-and-groups.md)

[애플리케이션에 사용자를 구성 및 프로비저닝하는 문제 해결](./application-provisioning-config-problem.md)