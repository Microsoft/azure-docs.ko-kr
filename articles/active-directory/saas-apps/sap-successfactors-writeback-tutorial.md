---
title: '자습서: Azure Active Directory에서 SuccessFactors 쓰기 저장 구성 Microsoft Docs'
description: Azure AD에서 SuccessFactors에 대 한 특성 쓰기 저장을 구성 하는 방법을 알아봅니다.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: ad255bd4-9e50-43a1-a92b-359215868b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 2de0cdd32428884170f549afacdbd52c3a10c93f
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060051"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>자습서: Azure AD에서 SAP SuccessFactors로 특성 쓰기 저장 구성 (미리 보기)
이 자습서는 Azure AD에서 SuccessFactors Employee Central으로 쓰기 저장 특성을 수행 하는 데 필요한 단계를 보여 주기 위한 것입니다. 현재 쓰기 저장에 대해 지원 되는 유일한 특성은 전자 메일 특성입니다. 

## <a name="overview"></a>개요

[SuccessFactors에서 온-프레미스 ad](sap-successfactors-inbound-provisioning-tutorial.md) 프로 비전 앱 또는 [SUCCESSFACTORS에서 Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) 프로 비전 앱으로 인바운드 프로 비전 통합을 설정한 후 필요에 따라 SuccessFactors 쓰기 저장 앱을 구성 하 여 전자 메일 주소를 SuccessFactors에 다시 쓸 수 있습니다. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>이 사용자 프로비전 솔루션에 가장 적합한 사용자

이 SuccessFactors 쓰기 저장 (Writeback) 사용자 프로 비전 솔루션은 다음과 같은 경우에 가장 적합 합니다.

* 회사에서 관리 하는 신뢰할 수 있는 특성 (예: 전자 메일 주소)을 SuccessFactors로 다시 쓰기 저장 하고자 하는 Office 365를 사용 하는 조직

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
  권한 역할 ![관리](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* 권한 역할 목록에서 **새로 만들기**를 클릭 합니다.
  > [!div class="mx-imgBorder"]
  > 새 권한 역할을 만들 ![](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* 새 권한 역할에 대 한 **역할 이름** 및 **설명을** 추가 합니다. 이름 및 설명은 API 사용 권한에 대 한 역할 임을 나타내야 합니다.
  > [!div class="mx-imgBorder"]
  > ![권한 역할 세부 정보](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* 권한 설정에서 사용 권한 **...** 을 클릭 한 다음 사용 권한 목록을 아래로 스크롤하고 **통합 도구 관리**를 클릭 합니다. **기본 인증을 통해 관리자가 ODATA API에 액세스할 수 있도록 허용**확인란을 선택 합니다.
  > [!div class="mx-imgBorder"]
  > 통합 도구](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png) ![관리
* 동일한 상자에서 아래로 스크롤하고 **Employee CENTRAL API**를 선택 합니다. ODATA API를 사용 하 여 읽고 ODATA API를 사용 하 여 편집 하려면 아래와 같이 사용 권한을 추가 합니다. SuccessFactors에 쓰기 저장 시나리오에 동일한 계정을 사용 하려는 경우 편집 옵션을 선택 합니다. 
  > [!div class="mx-imgBorder"]
  > 읽기 쓰기 권한을 ![](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* **완료**를 클릭합니다. **변경 내용 저장**을 클릭합니다.

### <a name="create-a-permission-group-for-the-api-user"></a>API 사용자에 대 한 권한 그룹 만들기

* SuccessFactors 관리 센터에서 *관리 권한 그룹*을 검색 한 다음 검색 결과에서 **권한 그룹 관리** 를 선택 합니다.
  > [!div class="mx-imgBorder"]
  > 권한 그룹을 관리 ![](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* 권한 그룹 관리 창에서 **새로 만들기**를 클릭 합니다.
  > [!div class="mx-imgBorder"]
  > 새 그룹을 추가 ![](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
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
  > 권한 그룹 추가 ![](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* 권한 그룹에 부여 된 권한 역할을 검토 합니다. 
  > [!div class="mx-imgBorder"]
  > ![권한 역할 및 그룹 세부 정보](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* **변경 내용 저장**을 클릭합니다.

## <a name="configuring-successfactors-writeback"></a>SuccessFactors 쓰기 저장 구성

이 섹션에서는 다음에 대 한 단계를 제공 합니다. 

* [프로 비전 커넥터 앱을 추가 하 고 SuccessFactors에 대 한 연결 구성](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [특성 매핑 구성](#part-2-configure-attribute-mappings)
* [사용자 프로비저닝 사용 및 시작](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>1 부: 프로 비전 커넥터 앱을 추가 하 고 SuccessFactors에 대 한 연결 구성

**SuccessFactors 쓰기 저장을 구성 하려면:**

1. [https://editor.swagger.io](<https://portal.azure.com>) 으로 이동합니다.

2. 왼쪽 탐색 모음에서 **Azure Active Directory**를 선택합니다.

3. **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다.

4. **애플리케이션 추가**를 선택하고 **모두** 범주를 선택합니다.

5. **SuccessFactors 쓰기 저장**을 검색 하 고 갤러리에서 해당 앱을 추가 합니다.

6. 앱이 추가되고 앱 세부 정보 화면이 표시되면 **프로비전**을 선택합니다.

7. **프로 비전** **모드** 를 **자동** 으로 변경

8. 다음과 같이 **관리자 자격 증명** 섹션을 완료합니다.

   * **관리자 사용자 이름** – 회사 ID가 추가 된 SuccessFactors API 사용자 계정의 사용자 이름을 입력 합니다. **사용자 이름\@companyID** 형식입니다.

   * **관리자 암호 –** SuccessFactors API 사용자 계정의 암호를 입력 합니다. 

   * **테넌트 URL –** SuccessFactors OData API 서비스 엔드포인트의 이름을 입력 합니다. Http 또는 https를 사용 하지 않고 서버의 호스트 이름만 입력 합니다. 이 값은 **api-server-name.successfactors.com**와 같습니다.

   * **알림 메일 –** 메일 주소를 입력하고 “오류가 발생하면 메일 보내기” 확인란을 선택합니다.
    > [!NOTE]
    > Azure AD 프로비전 서비스는 프로비전 작업이 [격리](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) 상태가 되면 이메일 알림을 보냅니다.

   * **연결 테스트** 단추를 클릭합니다. 연결 테스트가 성공하면 맨 위에서 **저장** 단추를 클릭합니다. 실패 하면 SuccessFactors 자격 증명 및 URL이 올바른지 다시 한 번 확인 합니다.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * 자격 증명이 성공적으로 저장 되 면 **매핑** 섹션에 **Azure Active Directory 사용자를 SuccessFactors에 동기화 하** 는 기본 매핑이 표시 됩니다.

### <a name="part-2-configure-attribute-mappings"></a>2부: 특성 매핑 구성

이 섹션에서는 사용자 데이터가 SuccessFactors에서 Active Directory로 흐르는 방식을 구성 합니다.

1. **매핑**아래의 프로 비전 탭에서 **SuccessFactors에 사용자 Azure Active Directory 동기화를**클릭 합니다.

1. **원본 개체 범위** 필드에서 특성 기반 필터 집합을 정의 하 여 쓰기 저장에 대해 고려할 Azure AD의 사용자 집합을 선택할 수 있습니다. 기본 범위는 "Azure AD의 모든 사용자"입니다. 
   > [!TIP]
   > 프로비전 앱을 처음 구성하는 경우 특성 매핑 및 식을 테스트하고 확인하여 원하는 결과를 제공하는지 확인해야 합니다. **원본 개체 범위** 에서 범위 지정 필터를 사용 하 여 Azure AD에서 소수의 테스트 사용자로 매핑을 테스트 하는 것이 좋습니다. 매핑이 작동하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

1. **대상 개체 작업** 필드는 **업데이트** 작업만 지원 합니다.

1. **특성 매핑** 섹션에서는 SuccessFactors 사용자 프로필을 azure ad 사용자와 연결 하는 데 사용 되는 일치 하는 ID와 azure ad에서 전자 메일의 원본으로 사용 되는 특성을 변경할 수 있습니다. 
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >SuccessFactors 쓰기 저장은 전자 메일 특성만 지원 합니다. 새 특성을 추가 하려면 **새 매핑 추가** 를 사용 하지 마세요. 

1. 매핑을 저장하려면 특성 매핑 섹션 맨 위에서 **저장**을 클릭합니다.

특성 매핑 구성이 완료되면 이제 [사용자 프로비저닝 서비스를 사용하도록 설정하고 시작](#enable-and-launch-user-provisioning)할 수 있습니다.

## <a name="enable-and-launch-user-provisioning"></a>사용자 프로비저닝 사용 및 시작

SuccessFactors 프로 비전 앱 구성이 완료 되 면 Azure Portal에서 프로 비전 서비스를 켤 수 있습니다.

> [!TIP]
> 기본적으로 프로비전 서비스를 켜면 범위 내 모든 사용자의 프로비전 작업이 시작됩니다. 매핑 오류 또는 Workday 데이터 문제가 있는 경우 프로비전 작업이 실패하고 격리 상태로 전환될 수 있습니다. 이를 방지하기 위해 **원본 개체 범위** 필터를 구성하고 모든 사용자의 전체 동기화를 시작하기 전에 몇몇 테스트 사용자로 특성 매핑을 테스트하는 것이 좋습니다. 매핑이 작동하고 원하는 결과를 제공하는지 확인한 후에는 필터를 제거하거나 점진적으로 더 많은 사용자를 포함하도록 해당 필터를 점진적으로 확장할 수 있습니다.

1. **프로비전** 탭에서 **프로비전 상태**를 **켜기**로 설정합니다.

2. **저장**을 클릭합니다.

3. 이 작업을 수행 하면 초기 동기화가 시작 되며,이는 SuccessFactors 테넌트에 있는 사용자 수에 따라 시간이 많이 걸릴 수 있습니다. 진행률 표시줄을 확인 하 여 동기화 주기의 진행 상황을 추적할 수 있습니다. 

4. 언제든지 Azure Portal에서 **감사 로그** 탭을 확인하여 프로비전 서비스에서 수행한 작업을 확인합니다. 감사 로그는 Workday에서 어떤 사용자를 읽은 다음, Active Directory에 추가 또는 업데이트되는지와 같은 프로비저닝 서비스에서 수행한 모든 개별 동기화 이벤트를 나열합니다. 

5. 초기 동기화가 완료되면 아래와 같이 **프로비전** 탭에 감사 요약 보고서가 작성됩니다.

   > [!div class="mx-imgBorder"]
   > 프로 비전 진행률 표시줄을 ![](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
* [SuccessFactors와 Azure Active Directory 간에 Single Sign-On를 구성 하는 방법에 대해 알아봅니다.](successfactors-tutorial.md)
* [Azure Active Directory와 다른 SaaS 애플리케이션을 통합하는 방법을 알아봅니다.](tutorial-list.md)
* [프로 비전 구성을 내보내고 가져오는 방법에 대해 알아봅니다.](../app-provisioning/export-import-provisioning-configuration.md)

