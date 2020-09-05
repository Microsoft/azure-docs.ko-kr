---
title: '자습서: Azure Active Directory에서 SuccessFactors 인바운드 프로 비전 구성 Microsoft Docs'
description: SuccessFactors에서 Azure AD로 인바운드 프로 비전을 구성 하는 방법에 대해 알아봅니다.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.openlocfilehash: 9c94d2ab2e0c49fe38142e42cf934c440746da75
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546681"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning"></a>자습서: Azure AD 사용자 프로 비전에 대 한 SAP SuccessFactors 구성
이 자습서에서는 SuccessFactors Employee Central에서 Azure Active Directory로 작업자 데이터를 프로 비전 하기 위해 수행 해야 하는 단계를 설명 하 고, 전자 메일 주소를 SuccessFactors에 선택적으로 쓸 수 있습니다. 

>[!NOTE]
>SuccessFactors에서 프로 비전 하려는 사용자가 온-프레미스 AD 계정이 필요 하지 않은 클라우드 전용 사용자 인 경우이 자습서를 사용 합니다. 사용자에 게 온-프레미스 AD 계정 또는 AD와 Azure AD 계정이 모두 필요한 경우 [SAP SuccessFactors 구성](sap-successfactors-inbound-provisioning-tutorial.md#overview) 에 대 한 자습서를 참조 하 여 사용자 프로 비전을 Active Directory 하세요. 

## <a name="overview"></a>개요

[Azure Active Directory 사용자 프로 비전 서비스](../app-provisioning/user-provisioning.md) 는 사용자의 id 수명 주기를 관리 하기 위해 [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) 과 통합 됩니다. 

Azure AD 사용자 프로 비전 서비스에서 지 원하는 SuccessFactors 사용자 프로 비전 워크플로는 다음과 같은 인적 자원 및 id 수명 주기 관리 시나리오를 자동화할 수 있도록 합니다.

* **신규 직원 고용** -SuccessFactors에 새 직원을 추가 하면 사용자 계정이 자동으로 Azure Active Directory 생성 되 고, 선택적으로 [Azure AD에서 지 원하는 Office 365 및 기타 SaaS 응용 프로그램이](../app-provisioning/user-provisioning.md)SuccessFactors에 전자 메일 주소를 다시 쓸 수 있습니다.

* **직원 특성 및 프로필 업데이트** -SuccessFactors에서 직원 레코드를 업데이트 하는 경우 (예: 이름, 제목 또는 관리자) 해당 사용자 계정이 자동으로 Azure Active Directory 업데이트 되 고, 선택적으로 Office 365 및 [Azure AD에서 지 원하는 기타 SaaS 응용 프로그램이](../app-provisioning/user-provisioning.md)자동으로 업데이트 됩니다.

* **직원 종료** -SuccessFactors에서 직원이 종료 되 면 해당 사용자 계정은 Azure Active Directory에서 자동으로 사용 하지 않도록 설정 되며, 선택적으로 Office 365 및 [Azure AD에서 지 원하는 기타 SaaS 응용 프로그램](../app-provisioning/user-provisioning.md)입니다.

* **직원 재할당** -SuccessFactors에서 직원이 다시 고용 될 때 이전 계정은 자동으로 다시 활성화 되거나 다시 프로 비전 될 수 있습니다 (기본 설정에 따라) Azure Active Directory 및 [Azure AD에서 지 원하는 기타 Office 365 및 기타 SaaS 응용 프로그램](../app-provisioning/user-provisioning.md)입니다.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>이 사용자 프로비전 솔루션에 가장 적합한 사용자

이 SuccessFactors Azure Active Directory 사용자 프로비저닝 솔루션은 다음과 같은 경우에 가장 적합 합니다.

* SuccessFactors 사용자 프로 비전을 위해 미리 작성 된 클라우드 기반 솔루션을 원하는 조직

* SuccessFactors에 직접 사용자를 프로 비전 해야 하는 조직 Azure Active Directory

* [EC (SuccessFactors Employee Central)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) 에서 얻은 데이터를 사용 하 여 사용자를 프로 비전 해야 하는 조직

* 이메일에 Office 365를 사용하는 조직

## <a name="solution-architecture"></a>솔루션 아키텍처

이 섹션에서는 클라우드 전용 사용자를 위한 종단 간 사용자 프로비저닝 솔루션 아키텍처에 대해 설명 합니다. 두 가지의 관련된 흐름이 있습니다.

* **권한 있는 HR 데이터 흐름 – SuccessFactors에서 Azure Active Directory로:** 이 흐름에서 작업자 이벤트 (예: 새 채용, 전송, 종료)는 먼저 cloud SuccessFactors Employee Central에서 발생 하 고 이벤트 데이터는 Azure Active Directory로 흐릅니다. 이벤트에 따라 Azure AD에서 생성/업데이트/사용/사용 안 함 작업이 발생할 수 있습니다.
* **전자 메일 쓰기 저장 흐름 – 온-프레미스 Active Directory에서 SuccessFactors로:** Azure Active Directory에서 계정 만들기가 완료 되 면 Azure AD에서 생성 된 전자 메일 특성 값 또는 UPN을 SuccessFactors에 다시 쓸 수 있습니다.

  ![개요](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>엔드투엔드 사용자 데이터 흐름

1. HR 팀은 SuccessFactors Employee Central에서 작업자 트랜잭션 (Joiners/운송업/Leavers 또는 새 채용/전송/종료)을 수행 합니다.
2. Azure AD 프로 비전 서비스는 SuccessFactors EC의 예약 된 id 동기화를 실행 하 고 온-프레미스 Active Directory와 동기화 하기 위해 처리 해야 하는 변경 내용을 식별 합니다.
3. Azure AD 프로 비전 서비스는 변경 내용을 확인 하 고 Azure AD에서 사용자에 대 한 만들기/업데이트/사용/사용 안 함 작업을 호출 합니다.
4. [SuccessFactors 쓰기 저장 앱](sap-successfactors-writeback-tutorial.md) 이 구성 되 면 Azure AD에서 사용자의 전자 메일 주소가 검색 됩니다. 
5. Azure AD 프로 비전 서비스는 사용 된 일치 특성에 따라 SuccessFactors에 전자 메일 특성을 다시 씁니다.

## <a name="planning-your-deployment"></a>배포 계획

SuccessFactors에서 Azure AD로 클라우드 HR 구동 사용자 프로 비전을 구성 하려면 다음과 같은 다양 한 측면을 포함 하는 상당한 계획이 필요 합니다.

* 일치 하는 ID 확인 
* 특성 매핑
* 특성 변환 
* 범위 지정 필터

이러한 항목에 대 한 포괄적인 지침은 [CLOUD HR 배포 계획](../app-provisioning/plan-cloud-hr-provision.md) 을 참조 하세요. 지원 되는 엔터티, 처리 세부 정보 및 다른 HR 시나리오에 대 한 통합을 사용자 지정 하는 방법에 대 한 자세한 내용은 [SAP SuccessFactors integration 참조](../app-provisioning/sap-successfactors-integration-reference.md) 를 참조 하세요. 

## <a name="configuring-successfactors-for-the-integration"></a>통합을 위한 SuccessFactors 구성

모든 SuccessFactors 프로 비전 커넥터의 일반적인 요구 사항은 SuccessFactors OData Api를 호출 하기 위한 올바른 권한이 있는 SuccessFactors 계정의 자격 증명을 요구 하는 것입니다. 이 섹션에서는 SuccessFactors에서 서비스 계정을 만들고 적절 한 권한을 부여 하는 단계에 대해 설명 합니다. 

* [SuccessFactors에서 API 사용자 계정 만들기/식별](#createidentify-api-user-account-in-successfactors)
* [API 권한 역할 만들기](#create-an-api-permissions-role)
* [API 사용자에 대 한 권한 그룹 만들기](#create-a-permission-group-for-the-api-user)
* [권한 그룹에 권한 역할을 부여 합니다.](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>SuccessFactors에서 API 사용자 계정 만들기/식별
SuccessFactors admin 팀 또는 구현 파트너와 협력 하 여 OData Api를 호출 하는 데 사용할 사용자 계정을 SuccessFactors에서 만들거나 식별 합니다. 이 계정의 사용자 이름 및 암호 자격 증명은 Azure AD에서 프로 비전 앱을 구성할 때 필요 합니다. 

### <a name="create-an-api-permissions-role"></a>API 권한 역할 만들기

* 관리 센터에 대 한 액세스 권한이 있는 사용자 계정을 사용 하 여 SAP SuccessFactors에 로그인 합니다.
* *권한 역할 관리*를 검색 한 다음 검색 결과에서 **사용 권한 역할 관리** 를 선택 합니다.
  ![권한 역할 관리](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* 권한 역할 목록에서 **새로 만들기**를 클릭 합니다.
  > [!div class="mx-imgBorder"]
  > ![새 권한 역할 만들기](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* 새 권한 역할에 대 한 **역할 이름** 및 **설명을** 추가 합니다. 이름 및 설명은 API 사용 권한에 대 한 역할 임을 나타내야 합니다.
  > [!div class="mx-imgBorder"]
  > ![권한 역할 정보](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* 권한 설정에서 사용 권한 **...** 을 클릭 한 다음 사용 권한 목록을 아래로 스크롤하고 **통합 도구 관리**를 클릭 합니다. **기본 인증을 통해 관리자가 ODATA API에 액세스할 수 있도록 허용**확인란을 선택 합니다.
  > [!div class="mx-imgBorder"]
  > ![통합 도구 관리](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* 동일한 상자에서 아래로 스크롤하고 **Employee CENTRAL API**를 선택 합니다. ODATA API를 사용 하 여 읽고 ODATA API를 사용 하 여 편집 하려면 아래와 같이 사용 권한을 추가 합니다. SuccessFactors에 쓰기 저장 시나리오에 동일한 계정을 사용 하려는 경우 편집 옵션을 선택 합니다. 
  > [!div class="mx-imgBorder"]
  > ![쓰기 권한 읽기](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* **완료**를 클릭합니다. **변경 내용 저장**을 클릭합니다.

### <a name="create-a-permission-group-for-the-api-user"></a>API 사용자에 대 한 권한 그룹 만들기

* SuccessFactors 관리 센터에서 *관리 권한 그룹*을 검색 한 다음 검색 결과에서 **권한 그룹 관리** 를 선택 합니다.
  > [!div class="mx-imgBorder"]
  > ![권한 그룹 관리](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* 권한 그룹 관리 창에서 **새로 만들기**를 클릭 합니다.
  > [!div class="mx-imgBorder"]
  > ![새 그룹 추가](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* 새 그룹의 그룹 이름을 추가 합니다. 그룹 이름은 API 사용자에 대 한 그룹 임을 나타내야 합니다.
  > [!div class="mx-imgBorder"]
  > ![권한 그룹 이름](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* 그룹에 멤버를 추가 합니다. 예를 들어 사용자 풀 드롭다운 메뉴에서 **사용자 이름** 을 선택한 다음 통합에 사용할 API 계정의 사용자 이름을 입력할 수 있습니다. 
  > [!div class="mx-imgBorder"]
  > ![그룹 멤버 추가](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* **완료** 를 클릭 하 여 권한 그룹 만들기를 완료 합니다.

### <a name="grant-permission-role-to-the-permission-group"></a>권한 그룹에 권한 역할을 부여 합니다.

* SuccessFactors 관리 센터에서 *관리 권한 역할*을 검색 한 다음 검색 결과에서 **권한 역할 관리** 를 선택 합니다.
* **권한 역할 목록**에서 API 사용 권한에 대해 만든 역할을 선택 합니다.
* **다음에이 역할 부여**...에서 **추가 ...** 단추를 클릭 합니다.
* 드롭다운 메뉴에서 **사용 권한 그룹 ...** 을 선택 하 고 **선택 ...** 을 클릭 하 여 그룹 창을 열고 위에서 만든 그룹을 검색 하 여 선택 합니다. 
  > [!div class="mx-imgBorder"]
  > ![권한 그룹 추가](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* 권한 그룹에 부여 된 권한 역할을 검토 합니다. 
  > [!div class="mx-imgBorder"]
  > ![권한 역할 및 그룹 정보](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* **변경 내용 저장**을 클릭합니다.

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>SuccessFactors에서 Azure AD로 사용자 프로 비전 구성

이 섹션에서는 SuccessFactors에서 Azure AD로의 사용자 계정 프로 비전에 대 한 단계를 제공 합니다.

* [프로 비전 커넥터 앱을 추가 하 고 SuccessFactors에 대 한 연결 구성](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [특성 매핑 구성](#part-2-configure-attribute-mappings)
* [사용자 프로비저닝 사용 및 시작](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>1 부: 프로 비전 커넥터 앱을 추가 하 고 SuccessFactors에 대 한 연결 구성

**Azure AD 프로 비전에 SuccessFactors를 구성 하려면:**

1. [https://editor.swagger.io](<https://portal.azure.com> ) 으로 이동합니다.

2. 왼쪽 탐색 모음에서 **Azure Active Directory**를 선택합니다.

3. **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다.

4. **애플리케이션 추가**를 선택하고 **모두** 범주를 선택합니다.

5. **SuccessFactors를 검색 하 여 사용자 프로 비전을 Azure Active Directory**하 고 갤러리에서 해당 앱을 추가 합니다.

6. 앱이 추가 되 고 앱 세부 정보 화면이 표시 되 면 **프로 비전** 을 선택 합니다.

7. **프로 비전** **모드** 를 **자동** 으로 변경

8. 다음과 같이 **관리자 자격 증명** 섹션을 완료합니다.

   * **관리자 사용자 이름** – 회사 ID가 추가 된 SuccessFactors API 사용자 계정의 사용자 이름을 입력 합니다. 형식: **username \@ companyID**

   * **관리자 암호 –** SuccessFactors API 사용자 계정의 암호를 입력 합니다. 

   * **테 넌 트 URL –** SuccessFactors OData API 서비스 끝점의 이름을 입력 합니다. Http 또는 https를 사용 하지 않고 서버의 호스트 이름만 입력 합니다. 이 값은 **api-server-name.successfactors.com**와 같습니다.

   * **알림 메일 –** 이메일 주소를 입력하고 "오류가 발생하면 이메일 보내기" 확인란을 선택합니다.
    > [!NOTE]
    > Azure AD 프로비전 서비스는 프로비전 작업이 [격리](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) 상태가 되면 이메일 알림을 보냅니다.

   * **연결 테스트** 단추를 클릭합니다. 연결 테스트가 성공하면 맨 위에서 **저장** 단추를 클릭합니다. 실패 하면 SuccessFactors 자격 증명 및 URL이 올바른지 다시 한 번 확인 합니다.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * 자격 증명이 성공적으로 저장 되 면 **매핑** 섹션에 **SuccessFactors 사용자를 동기화 하** 는 기본 매핑이 표시 됩니다 Azure Active Directory

### <a name="part-2-configure-attribute-mappings"></a>2부: 특성 매핑 구성

이 섹션에서는 사용자 데이터가 SuccessFactors에서 Active Directory로 흐르는 방식을 구성 합니다.

1. **매핑**아래의 프로 비전 탭에서 **SuccessFactors 사용자를 Azure Active Directory 동기화를**클릭 합니다.

1. **원본 개체 범위** 필드에서 특성 기반 필터 집합을 정의 하 여 SuccessFactors의 사용자 집합을 Azure AD로 프로 비전 하는 범위 내에 있어야 하는 사용자 집합을 선택할 수 있습니다. 기본 범위는 "SuccessFactors의 모든 사용자"입니다. 예제 필터:

   * 예: personIdExternal를 사용 하는 사용자에 대 한 범위 100만에서 200만 (200만 제외)

      * 특성: personIdExternal

      * 연산자: REGEX 일치

      * 값: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * 예제: 정규직 직원만 포함하고 비정규직 직원은 포함하지 않음

      * 특성: EmployeeID

      * 연산자: NULL이 아님

   > [!TIP]
   > 프로비전 앱을 처음 구성하는 경우 특성 매핑 및 식을 테스트하고 확인하여 원하는 결과를 제공하는지 확인해야 합니다. **원본 개체 범위** 에서 범위 지정 필터를 사용 하 여 SuccessFactors에서 소수의 테스트 사용자로 매핑을 테스트 하는 것이 좋습니다. 매핑이 작동하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

   > [!CAUTION] 
   > 프로비저닝 엔진의 기본 동작은 범위를 벗어나는 사용자를 사용하지 않도록 설정/삭제하는 것입니다. SuccessFactors Azure AD 통합에는 바람직하지 않을 수 있습니다. 이 기본 동작을 재정의하려면 범위를 [벗어난 사용자 계정 삭제 건너뛰기](../app-provisioning/skip-out-of-scope-deletions.md) 문서를 참조하세요.
  
1. **대상 개체 작업** 필드에서 Active Directory에서 수행할 작업을 전역적으로 필터링할 수 있습니다. **만들기** 및 **업데이트**가 가장 일반적입니다.

1. **특성 매핑** 섹션에서 개별 SuccessFactors 특성을 Active Directory 특성에 매핑하는 방법을 정의할 수 있습니다.

  >[!NOTE]
  >응용 프로그램에서 지 원하는 SuccessFactors 특성의 전체 목록은 [SuccessFactors 특성 참조](../app-provisioning/sap-successfactors-attribute-reference.md) 를 참조 하세요.


1. 기존 특성 매핑을 클릭하여 업데이트하거나 화면 맨 아래에서 **새 매핑 추가**를 클릭하여 새 매핑을 추가합니다. 개별 특성 매핑은 다음 속성을 지원합니다.

      * **매핑 유형**

         * **Direct** – SuccessFactors 특성의 값을 변경 하지 않고 AD 특성에 씁니다.

         * **상수** - AD 특성에 고정적인 상수 문자열 값을 씁니다.

         * **식** – 하나 이상의 SuccessFactors 특성을 기반으로 AD 특성에 사용자 지정 값을 쓸 수 있습니다. [자세한 내용은 식에 대한 이 문서를 참조하세요](../app-provisioning/functions-for-customizing-application-data.md).

      * **원본 특성** -SuccessFactors의 사용자 특성

      * **기본값** – 선택 사항입니다. 원본 특성의 값이 비어 있는 경우 매핑에서 이 값을 대신 씁니다.
            이 값을 비워두는 것이 가장 일반적인 구성입니다.

      * **대상 특성** – Active Directory의 사용자 특성입니다.

      * **이 특성을 사용 하 여 개체 일치** – SuccessFactors와 Active Directory 간에 사용자를 고유 하 게 식별 하는 데이 매핑을 사용할지 여부를 지정 합니다. 일반적으로이 값은 SuccessFactors의 작업자 ID 필드에 설정 됩니다 .이 필드는 일반적으로 Active Directory의 직원 ID 특성 중 하나에 매핑됩니다.

      * **일치 우선 순위** – 여러 일치 특성을 설정할 수 있습니다. 일치 특성이 여러 개 있으면 이 필드에 정의된 순서대로 평가됩니다. 일치 항목이 발견되는 즉시 더 이상 일치 특성을 평가하지 않습니다.

      * **이 매핑 적용**

         * **항상** – 사용자 만들기 및 업데이트 작업 시 이 매핑을 적용합니다.

         * **만들기 작업 시에만** - 사용자 만들기 작업 시에만 이 매핑을 적용합니다.

1. 매핑을 저장하려면 특성 매핑 섹션 맨 위에서 **저장**을 클릭합니다.

특성 매핑 구성이 완료되면 이제 [사용자 프로비저닝 서비스를 사용하도록 설정하고 시작](#enable-and-launch-user-provisioning)할 수 있습니다.

## <a name="enable-and-launch-user-provisioning"></a>사용자 프로비저닝 사용 및 시작

SuccessFactors 프로 비전 앱 구성이 완료 되 면 Azure Portal에서 프로 비전 서비스를 켤 수 있습니다.

> [!TIP]
> 기본적으로 프로비전 서비스를 켜면 범위 내 모든 사용자의 프로비전 작업이 시작됩니다. 매핑 오류 또는 Workday 데이터 문제가 있는 경우 프로비전 작업이 실패하고 격리 상태로 전환될 수 있습니다. 이를 방지하기 위해 **원본 개체 범위** 필터를 구성하고 모든 사용자의 전체 동기화를 시작하기 전에 몇몇 테스트 사용자로 특성 매핑을 테스트하는 것이 좋습니다. 매핑이 작동하고 원하는 결과를 제공하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

1. **프로비전** 탭에서 **프로비전 상태**를 **켜기**로 설정합니다.

2. **저장**을 클릭합니다.

3. 이 작업을 수행 하면 초기 동기화가 시작 되며,이는 SuccessFactors 테 넌 트에 있는 사용자 수에 따라 시간이 많이 걸릴 수 있습니다. 진행률 표시줄을 확인 하 여 동기화 주기의 진행 상황을 추적할 수 있습니다. 

4. 언제든지 Azure Portal에서 **감사 로그** 탭을 확인하여 프로비전 서비스에서 수행한 작업을 확인합니다. 감사 로그는 Workday에서 어떤 사용자를 읽은 다음, Active Directory에 추가 또는 업데이트되는지와 같은 프로비저닝 서비스에서 수행한 모든 개별 동기화 이벤트를 나열합니다. 

5. 초기 동기화가 완료되면 아래와 같이 **프로비전** 탭에 감사 요약 보고서가 작성됩니다.

   > [!div class="mx-imgBorder"]
   > ![프로 비전 진행률 표시줄](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>다음 단계

* [인바운드 프로 비전에 대해 지원 되는 SuccessFactors 특성에 대 한 자세한 정보](../app-provisioning/sap-successfactors-attribute-reference.md)
* [SuccessFactors에 전자 메일 쓰기 저장을 구성 하는 방법 알아보기](sap-successfactors-writeback-tutorial.md)
* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
* [SuccessFactors와 Azure Active Directory 간에 Single Sign-On를 구성 하는 방법에 대해 알아봅니다.](successfactors-tutorial.md)
* [Azure Active Directory와 다른 SaaS 애플리케이션을 통합하는 방법을 알아봅니다.](tutorial-list.md)
* [프로 비전 구성을 내보내고 가져오는 방법에 대해 알아봅니다.](../app-provisioning/export-import-provisioning-configuration.md)


