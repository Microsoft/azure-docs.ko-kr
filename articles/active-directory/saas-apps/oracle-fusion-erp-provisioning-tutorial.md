---
title: '자습서: Azure Active Directory을 사용 하 여 자동 사용자 프로 비전을 위해 Oracle Fusion ERP 구성 Microsoft Docs'
description: 사용자 계정을 Oracle Fusion ERP로 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure Active Directory를 구성 하는 방법에 대해 알아봅니다.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: Zhchia
ms.openlocfilehash: ad6b24c4bbfc2e117c010b99247cb8eb9925ff3e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543825"
---
# <a name="tutorial-configure-oracle-fusion-erp-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로 비전을 위해 Oracle Fusion ERP 구성

이 자습서에서는 사용자 및/또는 그룹을 Oracle Fusion ERP로 자동으로 프로 비전 및 프로 비전 해제 하도록 Azure AD를 구성 하기 위해 Oracle Fusion ERP 및 Azure Active Directory (Azure AD)에서 수행 하는 단계를 설명 합니다.

> [!NOTE]
>  이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../app-provisioning/user-provisioning.md)를 참조하세요.
>
> 이 커넥터는 현재 미리 보기로 제공됩니다. 미리 보기 기능에 대 한 일반 Microsoft Azure 사용 약관에 대 한 자세한 내용은 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 필수 구성 요소가 있다고 가정합니다.

* Azure AD 테넌트
* [Oracle FUSION ERP 테 넌 트](https://www.oracle.com/applications/erp/)입니다.
* 관리 권한이 있는 Oracle Fusion ERP의 사용자 계정

## <a name="assign-users-to-oracle-fusion-erp"></a>Oracle Fusion ERP에 사용자 할당 
Azure Active Directory는 할당이라는 개념을 사용하여 어떤 사용자가 선택된 앱에 대한 액세스 권한을 부여받아야 하는지 판단합니다. 자동 사용자 프로 비전의 컨텍스트에서는 Azure AD의 응용 프로그램에 할당 된 사용자 및/또는 그룹만 동기화 됩니다.

자동 사용자 프로비저닝을 구성 하 고 사용 하도록 설정 하기 전에 Azure AD의 사용자 및/또는 그룹에 Oracle Fusion ERP에 대 한 액세스가 필요한 지 여부를 결정 해야 합니다. 일단 결정 되 면 다음 지침에 따라 이러한 사용자 및/또는 그룹을 Oracle Fusion ERP에 할당할 수 있습니다.
 
* [엔터프라이즈 앱에 사용자 또는 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-oracle-fusion-erp"></a>Oracle Fusion ERP에 사용자를 할당 하기 위한 주요 팁 

 * 자동 사용자 프로 비전 구성을 테스트 하기 위해 단일 Azure AD 사용자를 Oracle Fusion ERP에 할당 하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

* Oracle Fusion ERP에 사용자를 할당할 때 [할당] 대화 상자에서 유효한 응용 프로그램별 역할 (사용 가능한 경우)을 선택 해야 합니다. 기본 액세스 역할이 있는 사용자는 프로비전에서 제외됩니다.

## <a name="set-up-oracle-fusion-erp-for-provisioning"></a>프로 비전을 위해 Oracle Fusion ERP 설정

Azure AD를 사용 하 여 자동 사용자 프로 비전을 위해 Oracle Fusion ERP를 구성 하기 전에 Oracle Fusion ERP에서 SCIM 프로 비전을 사용 하도록 설정 해야 합니다.

1. [Oracle FUSION ERP 관리 콘솔](https://cloud.oracle.com/sign-in) 에 로그인 합니다.

2. 왼쪽 위 모서리에서 탐색기를 클릭 합니다. **도구**아래에서 **보안 콘솔**을 선택 합니다.

    ![Oracle Fusion ERP 추가 SCIM](media/oracle-fusion-erp-provisioning-tutorial/login.png)

3. **사용자**로 이동 합니다.
    
    ![Oracle Fusion ERP 추가 SCIM](media/oracle-fusion-erp-provisioning-tutorial/user.png)

4. Oracle Fusion ERP 관리 콘솔에 로그인 하는 데 사용할 관리 사용자 계정의 사용자 이름 및 암호를 저장 합니다. 이러한 값은 Azure Portal Oracle Fusion ERP 응용 프로그램의 프로 비전 탭에 있는 **관리자 사용자 이름** 및 **암호** 필드에 입력 해야 합니다.

## <a name="add-oracle-fusion-erp-from-the-gallery"></a>갤러리에서 Oracle Fusion ERP 추가

Azure AD를 사용 하 여 자동 사용자 프로 비전을 위해 Oracle Fusion ERP를 구성 하려면 Azure AD 응용 프로그램 갤러리의 Oracle Fusion ERP를 관리 되는 SaaS 응용 프로그램 목록에 추가 해야 합니다.

**Azure AD 응용 프로그램 갤러리에서 Oracle Fusion ERP를 추가 하려면 다음 단계를 수행 합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 패널에서 **Azure Active Directory**를 선택 합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 응용 프로그램을 추가 하려면 창의 위쪽에 있는 **새 응용 프로그램** 단추를 선택 합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Oracle FUSION erp**를 입력 하 고 결과 패널에서 **oracle fusion erp** 를 선택 합니다.

    ![결과 목록의 Oracle Fusion ERP](common/search-new-app.png)

 ## <a name="configure-automatic-user-provisioning-to-oracle-fusion-erp"></a>Oracle Fusion ERP에 자동 사용자 프로 비전 구성 

이 섹션에서는 azure ad의 사용자 및/또는 그룹 할당을 기반으로 Oracle Fusion ERP에서 사용자 및/또는 그룹을 만들고, 업데이트 하 고, 비활성화 하도록 Azure AD 프로 비전 서비스를 구성 하는 단계를 안내 합니다.

> [!TIP]
> Oracle fusion erp [single sign-on 자습서](oracle-fusion-erp-tutorial.md)에 제공 된 지침에 따라 ORACLE fusion erp에 대해 SAML 기반 Single Sign-On를 사용 하도록 선택할 수도 있습니다. Single sign-on은 자동 사용자 프로 비전과 독립적으로 구성할 수 있습니다. 단,이 두 가지 기능은 서로 보완적입니다.

> [!NOTE]
> Oracle Fusion ERP의 SCIM 끝점에 대 한 자세한 내용은 [Oracle 응용 프로그램 클라우드의 일반적인 기능에 대 한 REST API](https://docs.oracle.com/en/cloud/saas/applications-common/18b/farca/index.html)를 참조 하세요.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Azure AD에서 Fuze에 대 한 자동 사용자 프로 비전을 구성 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **엔터프라이즈 애플리케이션**, **모든 애플리케이션**을 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Oracle Fusion ERP**를 선택합니다.

    ![애플리케이션 목록의 Oracle Fusion ERP 링크](common/all-applications.png)

3. **프로비전** 탭을 선택합니다.

    ![프로비저닝 탭](common/provisioning.png)

4. **프로비전 모드**를 **자동**으로 설정합니다.

    ![프로비저닝 탭](common/provisioning-automatic.png)

5. **관리자 자격 증명** 섹션 아래에 `https://ejlv.fa.em2.oraclecloud.com/hcmRestApi/scim/` 있는 **테 넌 트 URL**에 입력 합니다. 이전에 **관리자** 사용자 이름 및 **암호** 필드에 검색 한 관리자 사용자 이름 및 암호를 입력 합니다. Azure AD와 Oracle Fusion ERP 간의 **연결 테스트** 를 클릭 합니다. 

    ![Oracle Fusion ERP 추가 SCIM](media/oracle-fusion-erp-provisioning-tutorial/admin.png)

6. **알림 메일** 필드에 프로비저닝 오류 알림을 받을 개인 또는 그룹의 메일 주소를 입력하고, **오류가 발생할 경우, 메일 알림 보내기** 확인란을 선택합니다.

    ![알림 이메일](common/provisioning-notification-email.png)

7. **저장**을 클릭합니다.

8. **매핑** 섹션에서 **사용자 AZURE ACTIVE DIRECTORY Oracle Fusion ERP에 동기화**를 선택 합니다.

    ![Oracle Fusion ERP 추가 SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-mapping.png)

9. **특성 매핑** 섹션에서 Azure AD에서 ORACLE Fusion ERP로 동기화 되는 사용자 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 ORACLE Fusion ERP의 사용자 계정을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![Oracle Fusion ERP 추가 SCIM](media/oracle-fusion-erp-provisioning-tutorial/user-attribute.png)

10. **매핑** 섹션에서 **Oracle Fusion ERP에 Azure Active Directory 그룹 동기화를**선택 합니다.

    ![Oracle Fusion ERP 그룹 매핑](media/oracle-fusion-erp-provisioning-tutorial/groupmappings.png)

11. **특성 매핑** 섹션에서 Azure AD에서 ORACLE Fusion ERP로 동기화 되는 그룹 특성을 검토 합니다. **일치** 속성으로 선택한 특성은 업데이트 작업을 위해 ORACLE Fusion ERP의 그룹을 일치 시키는 데 사용 됩니다. **저장** 단추를 선택하여 변경 내용을 커밋합니다.

    ![Oracle Fusion ERP 그룹 특성](media/oracle-fusion-erp-provisioning-tutorial/groupattributes.png)

12. 범위 지정 필터를 구성하려면 [범위 지정 필터 자습서](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)에서 제공하는 다음 지침을 참조합니다.

13. Oracle Fusion ERP에 대해 Azure AD 프로 비전 서비스를 사용 하도록 **설정 하려면 설정** 섹션에서 **프로 비전 상태** 를 **켜기** 로 변경 합니다.

    ![프로비전 상태 켜기로 전환](common/provisioning-toggle-on.png)

14. **설정** 섹션의 **범위** 에서 원하는 값을 선택 하 여 Oracle Fusion ERP에 프로 비전 하려는 사용자 및/또는 그룹을 정의 합니다.

    ![프로비전 범위](common/provisioning-scope.png)

15. 프로비전할 준비가 되면 **저장**을 클릭합니다.

    ![프로비전 구성 저장](common/provisioning-configuration-save.png)

    이 작업은 **설정**의 **범위** 섹션에 정의된 모든 사용자 및/또는 그룹의 초기 동기화를 시작합니다. 초기 동기화는 Azure AD 프로비전 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용 하 여 진행률을 모니터링 하 고 프로 비전 활동 보고서에 대 한 링크를 따라 ORACLE Fusion ERP에서 Azure AD 프로 비전 서비스가 수행 하는 모든 작업을 설명 합니다.

    Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="connector-limitations"></a>커넥터 제한 사항

* Oracle Fusion ERP는 SCIM 끝점에 대 한 기본 인증만 지원 합니다.
* Oracle Fusion ERP는 그룹 프로 비전을 지원 하지 않습니다.
* Oracle Fusion ERP의 역할은 Azure AD의 그룹에 매핑됩니다. Azure AD에서 Oracle Fusion ERP의 사용자에 게 역할을 할당 하려면 Oracle Fusion ERP에서 역할 다음에 명명 된 원하는 Azure AD 그룹에 사용자를 할당 해야 합니다.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>다음 단계

* [프로비저닝 작업에 대한 로그를 검토하고 보고서를 받아보는 방법을 알아봅니다](../app-provisioning/check-status-user-account-provisioning.md).
