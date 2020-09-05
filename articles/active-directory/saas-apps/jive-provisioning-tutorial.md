---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 Jive 구성 | Microsoft Docs'
description: Azure Active Directory와 Jive 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: fec959ee34f9491b6510390d4b390e652690897e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547089"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>자습서: 자동 사용자 프로비전을 위한 Jive 구성

이 자습서의 목적은 사용자 계정을 Azure AD에서 Jive로 자동으로 프로비전 및 프로비전 해제하도록 Jive 및 Azure AD에서 수행해야 하는 단계를 설명하는 것입니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

*   Azure Active Directory 테넌트.
*   Jive Single Sign-On이 설정된 구독
*   팀 관리자 권한이 있는 Jive의 사용자 계정

## <a name="assigning-users-to-jive"></a>Jive에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 애플리케이션에 “할당된” 사용자 및 그룹만 동기화됩니다.

프로비전 서비스를 구성하고 사용하도록 설정하기 전에 Jive 앱에 액세스해야 하는 사용자를 나타내는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 결정했으면 다음 지침에 따라 이러한 사용자를 Jive 앱에 할당할 수 있습니다.

[엔터프라이즈 앱에 사용자 또는 그룹 할당](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Jive에 사용자를 할당하기 위한 주요 팁

*   단일 Azure AD 사용자를 Jive에 할당하여 프로비전 구성을 테스트하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.

*   Jive에 사용자를 할당할 때 유효한 사용자 역할을 선택해야 합니다. "기본 액세스" 역할은 프로비전에 작동하지 않습니다.

## <a name="enable-user-provisioning"></a>사용자 프로비저닝 사용

이 섹션에서는 사용자의 Azure AD를 Jive의 사용자 계정 프로비전 API에 연결하고, Azure AD의 사용자 및 그룹 할당을 기반으로 Jive에서 할당된 사용자 계정을 만들고 업데이트하고 비활성화하도록 프로비전 서비스를 구성하는 방법을 안내합니다.

> [!TIP]
> [Azure Portal](https://portal.azure.com)에 제공된 지침에 따라 Jive에 대해 SAML 기반 Single Sign-On을 사용하도록 설정할 수도 있습니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="to-configure-user-account-provisioning"></a>사용자 계정 프로비전을 구성하려면

이 섹션에서는 Jive에 Active Directory 사용자 계정을 프로비저닝할 수 있도록 설정하는 방법을 간략하게 설명합니다.
이 절차의 일부로 Jive.com에 요청해야 하는 사용자 보안 토큰을 입력해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory > Enterprise Apps > 모든 응용 프로그램** 섹션으로 이동 합니다.

1. 이미 Jive에 Single Sign-On을 구성한 경우 검색 필드를 사용하여 Jive의 인스턴스를 검색합니다. 그러지 않은 경우 **추가**를 선택하고 애플리케이션 갤러리에서 **Jive**를 검색합니다. 검색 결과에서 Jive를 선택하고 애플리케이션 목록에 추가합니다.

1. Jive의 인스턴스를 선택한 다음 **프로비전** 탭을 선택합니다.

1. **프로비전 모드**를 **자동**으로 설정합니다. 

    ![프로비전](./media/jive-provisioning-tutorial/provisioning.png)

1. **관리자 자격 증명** 섹션에서 다음 구성 설정을 제공합니다.
   
    a. **Jive 관리자 사용자 이름** 텍스트 상자에 Jive.com에서 **시스템 관리자** 프로필이 할당된 Jive 계정 이름을 입력합니다.
   
    b. **Jive 관리자 암호** 텍스트 상자에 해당 계정의 암호를 입력합니다.
   
    다. **Jive 테넌트 URL** 텍스트 상자에 Jive 테넌트 URL을 입력합니다.
      
      > [!NOTE]
      > Jive 테넌트 URL은 조직에서 Jive에 로그인할 때 사용하는 URL입니다.  
      > 일반적으로 URL의 형식은 다음과 같습니다. **www. \<organization\> . jive.com**.          

1. Azure Portal에서 **연결 테스트**를 클릭하여 Azure AD가 Jive 앱에 연결할 수 있는지 확인합니다.

1. 프로비전 오류 알림을 받을 개인 또는 그룹의 전자 메일 주소를 **알림 전자 메일** 필드에 입력하고 아래 확인란을 선택합니다.

1. **저장**을 클릭합니다.

1. [매핑] 섹션에서 **Synchronize Azure Active Directory Users to Jive**(Azure Active Directory 사용자를 Jive에 동기화)를 선택합니다.

1. **특성 매핑** 섹션에서 Azure AD에서 Jive로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 Jive의 사용자 계정을 일치시키는 데 사용됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.

1. Jive에 대한 Azure AD 프로비전 서비스를 사용하도록 설정하려면 [설정] 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

1. **저장**을 클릭합니다.

[사용자 및 그룹] 섹션에서 Jive에 할당된 모든 사용자 및/또는 그룹의 초기 동기화가 시작됩니다. 초기 동기화는 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고 링크를 클릭하여 Jive 앱의 프로비저닝 서비스에서 수행한 모든 작업을 설명하는 프로비저닝 활동 로그를 확인할 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../app-provisioning/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
* [Single Sign-on 구성](jive-tutorial.md)
