---
title: '자습서: Azure Active Directory에서 SAP SuccessFactors 쓰기 저장 구성 Microsoft Docs'
description: Azure AD에서 SAP SuccessFactors에 특성 쓰기 다시 쓰기를 구성 하는 방법에 대해 알아봅니다.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: article
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.openlocfilehash: 4b048053a553176f73b5bd199bcb6e28bc74cc6c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88533999"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>자습서: Azure AD에서 SAP SuccessFactors로 특성 쓰기 다시 구성
이 자습서에서는 Azure AD에서 SAP SuccessFactors Employee Central으로 특성을 다시 작성 하는 단계를 보여 줍니다. 

## <a name="overview"></a>개요

Azure Active Directory의 특정 특성을 SAP SuccessFactors Employee Central에 쓰도록 SAP SuccessFactors 쓰기 저장 (Writeback) 앱을 구성할 수 있습니다. SuccessFactors 쓰기 저장 (writeback) 프로 비전 앱은 다음 직원 중앙 특성에 값을 할당 하도록 지원 합니다.

* 회사 전자 메일
* 사용자 이름
* 회사 전화 번호 (국가 코드, 지역 번호 및 내선 번호 포함)
* 회사 전화 번호 기본 플래그
* 휴대폰 번호 (국가 코드, 지역 번호 등)
* 휴대폰 기본 플래그 
* 사용자 custom01-custom15 특성
* loginMethod 특성

> [!NOTE]
> 이 앱은 SuccessFactors 인바운드 사용자 프로 비전 통합 앱에 대 한 종속성이 없습니다. [온-프레미스 AD](sap-successfactors-inbound-provisioning-tutorial.md) 프로 비전 앱에 대 한 SuccessFactors 또는 [Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 프로 비전 앱에 SuccessFactors와 독립적으로 구성할 수 있습니다.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>이 사용자 프로비전 솔루션에 가장 적합한 사용자

이 SuccessFactors 쓰기 저장 (Writeback) 사용자 프로 비전 솔루션은 다음과 같은 경우에 가장 적합 합니다.

* Office 365를 사용 하는 조직에서는이를 통해 관리 되는 신뢰할 수 있는 특성 (예: 전자 메일 주소, 전화, 사용자 이름)을 다시 SuccessFactors Employee Central으로 다시 쓰기를 원합니다.

## <a name="configuring-successfactors-for-the-integration"></a>통합을 위한 SuccessFactors 구성

모든 SuccessFactors 프로 비전 커넥터에는 직원 중앙 OData Api를 호출 하기 위한 올바른 권한이 있는 SuccessFactors 계정의 자격 증명이 필요 합니다. 이 섹션에서는 SuccessFactors에서 서비스 계정을 만들고 적절 한 권한을 부여 하는 단계에 대해 설명 합니다. 

* [SuccessFactors에서 API 사용자 계정 만들기/식별](#createidentify-api-user-account-in-successfactors)
* [API 권한 역할 만들기](#create-an-api-permissions-role)
* [API 사용자에 대 한 권한 그룹 만들기](#create-a-permission-group-for-the-api-user)
* [권한 그룹에 권한 역할을 부여 합니다.](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>SuccessFactors에서 API 사용자 계정 만들기/식별
SuccessFactors admin 팀 또는 구현 파트너와 협력 하 여 OData Api를 호출 하는 데 사용할 사용자 계정을 SuccessFactors에서 만들거나 식별 합니다. 이 계정의 사용자 이름 및 암호 자격 증명은 Azure AD에서 프로 비전 앱을 구성할 때 필요 합니다. 

### <a name="create-an-api-permissions-role"></a>API 권한 역할 만들기

1. 관리 센터에 대 한 액세스 권한이 있는 사용자 계정을 사용 하 여 SAP SuccessFactors에 로그인 합니다.
1. *권한 역할 관리*를 검색 한 다음 검색 결과에서 **사용 권한 역할 관리** 를 선택 합니다.

   ![권한 역할 관리](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. 권한 역할 목록에서 **새로 만들기**를 클릭 합니다.

   > [!div class="mx-imgBorder"]
   > ![새 권한 역할 만들기](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. 새 권한 역할에 대 한 **역할 이름** 및 **설명을** 추가 합니다. 이름 및 설명은 API 사용 권한에 대 한 역할 임을 나타내야 합니다.

   > [!div class="mx-imgBorder"]
   > ![권한 역할 정보](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. 권한 설정에서 사용 권한 **...** 을 클릭 한 다음 사용 권한 목록을 아래로 스크롤하고 **통합 도구 관리**를 클릭 합니다. **기본 인증을 통해 관리자가 ODATA API에 액세스할 수 있도록 허용**확인란을 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![통합 도구 관리](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. 동일한 상자에서 아래로 스크롤하고 **Employee CENTRAL API**를 선택 합니다. ODATA API를 사용 하 여 읽고 ODATA API를 사용 하 여 편집 하려면 아래와 같이 사용 권한을 추가 합니다. SuccessFactors에 쓰기 다시 쓰기 시나리오에 동일한 계정을 사용 하려는 경우 편집 옵션을 선택 합니다. 

   > [!div class="mx-imgBorder"]
   > ![쓰기 권한 읽기](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. **완료**를 클릭합니다. **변경 내용 저장**을 클릭합니다.

### <a name="create-a-permission-group-for-the-api-user"></a>API 사용자에 대 한 권한 그룹 만들기

1. SuccessFactors 관리 센터에서 *관리 권한 그룹*을 검색 한 다음 검색 결과에서 **권한 그룹 관리** 를 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![권한 그룹 관리](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. 권한 그룹 관리 창에서 **새로 만들기**를 클릭 합니다.

   > [!div class="mx-imgBorder"]
   > ![새 그룹 추가](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. 새 그룹의 그룹 이름을 추가 합니다. 그룹 이름은 API 사용자에 대 한 그룹 임을 나타내야 합니다.

   > [!div class="mx-imgBorder"]
   > ![권한 그룹 이름](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. 그룹에 멤버를 추가 합니다. 예를 들어 사용자 풀 드롭다운 메뉴에서 **사용자 이름** 을 선택한 다음 통합에 사용할 API 계정의 사용자 이름을 입력할 수 있습니다. 

   > [!div class="mx-imgBorder"]
   > ![그룹 멤버 추가](./media/sap-successfactors-inbound-provisioning/add-group-members.png)

1. **완료** 를 클릭 하 여 권한 그룹 만들기를 완료 합니다.

### <a name="grant-permission-role-to-the-permission-group"></a>권한 그룹에 권한 역할을 부여 합니다.

1. SuccessFactors 관리 센터에서 *관리 권한 역할*을 검색 한 다음 검색 결과에서 **권한 역할 관리** 를 선택 합니다.
1. **권한 역할 목록**에서 API 사용 권한에 대해 만든 역할을 선택 합니다.
1. **다음에이 역할 부여**...에서 **추가 ...** 단추를 클릭 합니다.
1. 드롭다운 메뉴에서 **사용 권한 그룹 ...** 을 선택 하 고 **선택 ...** 을 클릭 하 여 그룹 창을 열고 위에서 만든 그룹을 검색 하 여 선택 합니다. 

   > [!div class="mx-imgBorder"]
   > ![권한 그룹 추가](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. 권한 그룹에 부여 된 권한 역할을 검토 합니다. 
   > [!div class="mx-imgBorder"]
   > ![권한 역할 및 그룹 정보](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. **변경 내용 저장**을 클릭합니다.

## <a name="preparing-for-successfactors-writeback"></a>SuccessFactors 쓰기 저장 준비

SuccessFactors 쓰기 저장 (Writeback) 프로 비전 앱은 특정 *코드* 값을 사용 하 여 Employee Central에서 전자 메일 및 전화 번호를 설정 합니다. 이러한 *코드* 값은 특성 매핑 테이블에서 상수 값으로 설정 되며 각 SuccessFactors 인스턴스에 대해 다릅니다. 이 섹션에서는 [Postman](https://www.postman.com/downloads/) 을 사용 하 여 코드 값을 가져옵니다. [말아](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler) 또는 기타 유사한 도구를 사용 하 여 HTTP 요청을 보낼 수 있습니다. 

### <a name="download-and-configure-postman-with-your-successfactors-tenant"></a>SuccessFactors 테 넌 트를 사용 하 여 Postman 다운로드 및 구성

1. [Postman](https://www.postman.com/downloads/) 다운로드
1. Postman 앱에서 "새 컬렉션"을 만듭니다. "SuccessFactors" 라고 합니다. 

   > [!div class="mx-imgBorder"]
   > ![새 Postman 컬렉션](./media/sap-successfactors-inbound-provisioning/new-postman-collection.png)

1. "권한 부여" 탭에서 이전 섹션에서 구성한 API 사용자의 자격 증명을 입력 합니다. 유형을 "기본 인증"으로 구성 합니다. 

   > [!div class="mx-imgBorder"]
   > ![Postman 권한 부여](./media/sap-successfactors-inbound-provisioning/postman-authorization.png)

1. 구성을 저장합니다. 

### <a name="retrieve-constant-value-for-emailtype"></a>EmailType의 상수 값을 검색 합니다.

1. Postman에서 SuccessFactors 컬렉션과 연결 된 줄임표 (...)를 클릭 하 고 아래와 같이 "새 요청"을 "전자 메일 유형 가져오기" 라고 추가 합니다. 

   > [!div class="mx-imgBorder"]
   > ![Postman 전자 메일 요청 ](./media/sap-successfactors-inbound-provisioning/postman-email-request.png)

1. "전자 메일 유형 가져오기" 요청 패널을 엽니다. 
1. URL 가져오기에서 다음 URL을 추가 합니다 `successFactorsAPITenantName` .를 SuccessFactors 인스턴스의 API 테 넌 트로 바꿉니다. 
   `https://<successfactorsAPITenantName>/odata/v2/Picklist('ecEmailType')?$expand=picklistOptions&$select=picklistOptions/id,picklistOptions/externalCode&$format=json`

   > [!div class="mx-imgBorder"]
   > ![Postman 이메일 유형 가져오기](./media/sap-successfactors-inbound-provisioning/postman-get-email-type.png)

1. "권한 부여" 탭은 컬렉션에 대해 구성 된 인증을 상속 합니다. 
1. "송신"을 클릭 하 여 API 호출을 호출 합니다. 
1. 응답 본문에서 JSON 결과 집합을 확인 하 고에 해당 하는 ID를 찾습니다 `externalCode = B` . 

   > [!div class="mx-imgBorder"]
   > ![Postman 전자 메일 유형 응답](./media/sap-successfactors-inbound-provisioning/postman-email-type-response.png)

1. 특성 매핑 테이블에서 *Emailtype* 에 사용할 상수로이 값을 적어 둡니다.

### <a name="retrieve-constant-value-for-phonetype"></a>PhoneType에 대 한 상수 값 검색

1. Postman에서 SuccessFactors 컬렉션과 연결 된 줄임표 (...)를 클릭 하 고 아래와 같이 "새 요청"을 "휴대폰 유형 가져오기" 라고 추가 합니다. 

   > [!div class="mx-imgBorder"]
   > ![Postman 휴대폰 요청](./media/sap-successfactors-inbound-provisioning/postman-phone-request.png)

1. "휴대폰 유형 가져오기" 요청 패널을 엽니다. 
1. URL 가져오기에서 다음 URL을 추가 합니다 `successFactorsAPITenantName` .를 SuccessFactors 인스턴스의 API 테 넌 트로 바꿉니다. 
   `https://<successfactorsAPITenantName>/odata/v2/Picklist('ecPhoneType')?$expand=picklistOptions&$select=picklistOptions/id,picklistOptions/externalCode&$format=json`

   > [!div class="mx-imgBorder"]
   > ![Postman 휴대폰 유형 가져오기](./media/sap-successfactors-inbound-provisioning/postman-get-phone-type.png)

1. "권한 부여" 탭은 컬렉션에 대해 구성 된 인증을 상속 합니다. 
1. "송신"을 클릭 하 여 API 호출을 호출 합니다. 
1. 응답 본문에서 JSON 결과 집합을 보고 및에 해당 하는 *id* 를 찾습니다 `externalCode = B` `externalCode = C` . 

   > [!div class="mx-imgBorder"]
   > ![Postman-휴대폰](./media/sap-successfactors-inbound-provisioning/postman-phone-type-response.png)

1. 특성 매핑 테이블에서 *businessPhoneType* 및 *cellPhoneType* 와 함께 사용할 상수로 이러한 값을 적어 둡니다.

## <a name="configuring-successfactors-writeback-app"></a>SuccessFactors 쓰기 저장 앱 구성

이 섹션에서는 다음에 대 한 단계를 제공 합니다. 

* [프로 비전 커넥터 앱을 추가 하 고 SuccessFactors에 대 한 연결 구성](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [특성 매핑 구성](#part-2-configure-attribute-mappings)
* [사용자 프로비저닝 사용 및 시작](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>1 부: 프로 비전 커넥터 앱을 추가 하 고 SuccessFactors에 대 한 연결 구성

**SuccessFactors 쓰기 저장을 구성 하려면:**

1. [https://editor.swagger.io](<https://portal.azure.com> ) 으로 이동합니다.

2. 왼쪽 탐색 모음에서 **Azure Active Directory**를 선택합니다.

3. **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다.

4. **애플리케이션 추가**를 선택하고 **모두** 범주를 선택합니다.

5. **SuccessFactors 쓰기 저장**을 검색 하 고 갤러리에서 해당 앱을 추가 합니다.

6. 앱이 추가 되 고 앱 세부 정보 화면이 표시 되 면 **프로 비전** 을 선택 합니다.

7. **프로 비전** **모드** 를 **자동** 으로 변경

8. 다음과 같이 **관리자 자격 증명** 섹션을 완료합니다.

   * **관리자 사용자 이름** – 회사 ID가 추가 된 SuccessFactors API 사용자 계정의 사용자 이름을 입력 합니다. 형식: **username \@ companyID**

   * **관리자 암호 –** SuccessFactors API 사용자 계정의 암호를 입력 합니다. 

   * **테 넌 트 URL –** SuccessFactors OData API 서비스 끝점의 이름을 입력 합니다. Http 또는 https를 사용 하지 않고 서버의 호스트 이름만 입력 합니다. 이 값은와 같아야 `api4.successfactors.com` 합니다.

   * **알림 메일 –** 이메일 주소를 입력하고 "오류가 발생하면 이메일 보내기" 확인란을 선택합니다.
    > [!NOTE]
    > Azure AD 프로비전 서비스는 프로비전 작업이 [격리](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) 상태가 되면 이메일 알림을 보냅니다.

   * **연결 테스트** 단추를 클릭합니다. 연결 테스트가 성공하면 맨 위에서 **저장** 단추를 클릭합니다. 실패 하면 SuccessFactors 자격 증명 및 URL이 올바른지 다시 한 번 확인 합니다.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * 자격 증명이 성공적으로 저장 되 면 **매핑** 섹션에 기본 매핑이 표시 됩니다. 특성 매핑이 표시 되지 않는 경우 페이지를 새로 고칩니다.  

### <a name="part-2-configure-attribute-mappings"></a>2부: 특성 매핑 구성

이 섹션에서는 사용자 데이터가 SuccessFactors에서 Active Directory로 흐르는 방식을 구성 합니다.

1. **매핑**아래의 프로 비전 탭에서 **사용자 Azure Active Directory 프로 비전**을 클릭 합니다.

1. **원본 개체 범위** 필드에서 특성 기반 필터 집합을 정의 하 여 쓰기 복구에 대해 고려할 Azure AD의 사용자 집합을 선택할 수 있습니다. 기본 범위는 "Azure AD의 모든 사용자"입니다. 
   > [!TIP]
   > 프로비전 앱을 처음 구성하는 경우 특성 매핑 및 식을 테스트하고 확인하여 원하는 결과를 제공하는지 확인해야 합니다. **원본 개체 범위** 에서 범위 지정 필터를 사용 하 여 Azure AD에서 소수의 테스트 사용자로 매핑을 테스트 하는 것이 좋습니다. 매핑이 작동하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

1. **대상 개체 작업** 필드는 **업데이트** 작업만 지원 합니다.

1. **특성 매핑** 섹션 아래의 매핑 테이블에서 다음 Azure Active Directory 특성을 SuccessFactors에 매핑할 수 있습니다. 다음 표에서는 나중 쓰기 특성을 매핑하는 방법에 대 한 지침을 제공 합니다. 

   | \# | Azure AD 특성 | SuccessFactors 특성 | 설명 |
   |--|--|--|--|
   | 1 | employeeId | personIdExternal | 기본적으로이 특성은 일치 하는 식별자입니다. EmployeeId 대신 SuccessFactors에 personIdExternal와 같은 값을 저장할 수 있는 다른 Azure AD 특성을 사용할 수 있습니다.    |
   | 2 | mail | 이메일 | 전자 메일 특성 소스를 매핑합니다. 테스트를 위해 userPrincipalName를 전자 메일에 매핑할 수 있습니다. |
   | 3 | 8448 | emailType | 이 상수 값은 비즈니스 전자 메일과 연결 된 SuccessFactors ID 값입니다. SuccessFactors 환경과 일치 하도록이 값을 업데이트 합니다. 이 값을 설정 하는 단계는 [emailType의 상수 값 검색](#retrieve-constant-value-for-emailtype) 섹션을 참조 하세요. |
   | 4 | true | emailIsPrimary | 이 특성을 사용 하 여 SuccessFactors에서 비즈니스 전자 메일을 기본으로 설정 합니다. 비즈니스 전자 메일이 기본이 아니면이 플래그를 false로 설정 합니다. |
   | 5 | userPrincipalName | [custom01 – custom15] | **새 매핑 추가**를 사용 하 여 필요에 따라 SuccessFactors User 개체에서 사용 가능한 사용자 지정 특성에 UserPrincipalName 또는 Azure AD 특성을 쓸 수 있습니다.  |
   | 6 | 온-프레미스-samAccountName | username | **새 매핑 추가**를 사용 하 여 필요에 따라 온-프레미스 SamAccountName을 SuccessFactors username 특성에 매핑할 수 있습니다. |
   | 7 | SSO | loginMethod | SuccessFactors 테 넌 트가 [부분 SSO](https://apps.support.sap.com/sap/support/knowledge/en/2320766)에 대해 설정 된 경우 새 매핑 추가를 사용 하 여 필요에 따라 loginMethod를 "SSO" 또는 "PWD"의 상수 값으로 설정할 수 있습니다. |
   | 8 | telephoneNumber | businessPhoneNumber | 이 매핑을 사용 하 여 Azure AD에서 SuccessFactors business/직장 전화 번호로 *telephoneNumber* 를 전달 합니다. |
   | 9 | 10605 | businessPhoneType | 이 상수 값은 회사 전화와 연결 된 SuccessFactors ID 값입니다. SuccessFactors 환경과 일치 하도록이 값을 업데이트 합니다. 이 값을 설정 하는 단계는 [phoneType 상수 값 검색](#retrieve-constant-value-for-phonetype) 섹션을 참조 하세요. |
   | 10 | true | businessPhoneIsPrimary | 회사 전화 번호에 대 한 기본 플래그를 설정 하려면이 특성을 사용 합니다. 유효한 값은 true 또는 false입니다. |
   | 11 | mobile | cellPhoneNumber | 이 매핑을 사용 하 여 Azure AD에서 SuccessFactors business/직장 전화 번호로 *telephoneNumber* 를 전달 합니다. |
   | 12 | 10606 | cellPhoneType | 이 상수 값은 휴대폰에 연결 된 SuccessFactors ID 값입니다. SuccessFactors 환경과 일치 하도록이 값을 업데이트 합니다. 이 값을 설정 하는 단계는 [phoneType 상수 값 검색](#retrieve-constant-value-for-phonetype) 섹션을 참조 하세요. |
   | 13 | false | cellPhoneIsPrimary | 이 특성을 사용 하 여 휴대폰 번호에 대 한 기본 플래그를 설정 합니다. 유효한 값은 true 또는 false입니다. |
 
1. 특성 매핑 유효성을 검사 하 고 검토 합니다. 
 
    >[!div class="mx-imgBorder"]
    >![쓰기 저장 특성 매핑](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. **저장** 을 클릭 하 여 매핑을 저장 합니다. 다음으로 SuccessFactors 인스턴스의 phoneType 코드를 사용 하도록 JSON 경로 API 식을 업데이트 합니다. 
1. **고급 옵션 표시**를 선택합니다. 

    >[!div class="mx-imgBorder"]
    >![고급 옵션 표시](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. **SuccessFactors에 대 한 특성 목록 편집**을 클릭 합니다. 

   > [!NOTE] 
   > **SuccessFactors에 대 한 특성 목록 편집** 옵션이 Azure Portal에 표시 되지 않으면 URL을 사용 하 여 *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* 페이지에 액세스 합니다. 

1. 이 보기의 **API 식** 열에는 커넥터에서 사용 하는 JSON 경로 식이 표시 됩니다. 
1. 회사 전화 및 휴대폰에 대 한 JSON 경로 식을 업데이트 하 여 사용자 환경에 해당 하는 ID 값 (*businessPhoneType* 및 *cellPhoneType*)을 사용 합니다. 

    >[!div class="mx-imgBorder"]
    >![Phone JSON 경로 변경](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. **저장** 을 클릭 하 여 매핑을 저장 합니다.

## <a name="enable-and-launch-user-provisioning"></a>사용자 프로비저닝 사용 및 시작

SuccessFactors 프로 비전 앱 구성이 완료 되 면 Azure Portal에서 프로 비전 서비스를 켤 수 있습니다.

> [!TIP]
> 기본적으로 프로비전 서비스를 켜면 범위 내 모든 사용자의 프로비전 작업이 시작됩니다. 매핑 또는 데이터 문제에 오류가 있는 경우 프로 비전 작업이 실패 하 고 격리 상태로 전환 될 수 있습니다. 이를 방지하기 위해 **원본 개체 범위** 필터를 구성하고 모든 사용자의 전체 동기화를 시작하기 전에 몇몇 테스트 사용자로 특성 매핑을 테스트하는 것이 좋습니다. 매핑이 작동하고 원하는 결과를 제공하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

1. **프로비전** 탭에서 **프로비전 상태**를 **켜기**로 설정합니다.

2. **저장**을 클릭합니다.

3. 이 작업을 수행 하면 초기 동기화가 시작 되며,이는 SuccessFactors 테 넌 트에 있는 사용자 수에 따라 시간이 많이 걸릴 수 있습니다. 진행률 표시줄을 확인 하 여 동기화 주기의 진행 상황을 추적할 수 있습니다. 

4. 언제든지 Azure Portal에서 **감사 로그** 탭을 확인하여 프로비전 서비스에서 수행한 작업을 확인합니다. 감사 로그에는 프로 비전 서비스에서 수행 하는 모든 개별 동기화 이벤트가 나열 됩니다. 예를 들어 Employee Central에서 읽고 이후에 Active Directory에 추가 하거나 업데이트 하는 사용자가 나열 됩니다. 

5. 초기 동기화가 완료되면 아래와 같이 **프로비전** 탭에 감사 요약 보고서가 작성됩니다.

   > [!div class="mx-imgBorder"]
   > ![프로 비전 진행률 표시줄](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>지원 되는 시나리오, 알려진 문제 및 제한 사항

SAP SuccessFactors 통합 참조 가이드의 [쓰기 저장 시나리오 섹션](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios) 을 참조 하세요. 

## <a name="next-steps"></a>다음 단계

* [Azure AD 및 SAP SuccessFactors 통합 참조 심층 살펴보기](../app-provisioning/sap-successfactors-integration-reference.md)
* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
* [SuccessFactors와 Azure Active Directory 간에 Single Sign-On를 구성 하는 방법에 대해 알아봅니다.](successfactors-tutorial.md)
* [Azure Active Directory와 다른 SaaS 애플리케이션을 통합하는 방법을 알아봅니다.](tutorial-list.md)
* [프로 비전 구성을 내보내고 가져오는 방법에 대해 알아봅니다.](../app-provisioning/export-import-provisioning-configuration.md)

