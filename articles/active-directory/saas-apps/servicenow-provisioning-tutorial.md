---
title: '자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 ServiceNow 구성 | Microsoft Docs'
description: 사용자 계정을 Azure AD에서 ServiceNow로 자동으로 프로비전 및 프로비전 해제하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19b3e4cc5ba4bc0173721947bd1e1a680ca7b3a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60869845"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning-with-azure-active-directory"></a>자습서: Azure Active Directory로 자동 사용자 프로비전을 위한 ServiceNow 구성

이 자습서에서는 사용자 계정을 Azure AD에서 ServiceNow로 자동으로 프로비전 및 프로비전 해제하도록 ServiceNow 및 Azure AD에서 수행해야 하는 단계를 설명합니다.

> [!NOTE]
> 이 자습서에서는 Azure AD 사용자 프로비저닝 서비스에 기반하여 구축된 커넥터에 대해 설명합니다. 이 서비스의 기능, 작동 방법 및 질문과 대답에 대한 중요한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비저닝 및 프로비저닝 해제](../manage-apps/user-provisioning.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

ServiceNow와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- ServiceNow의 경우 ServiceNow의 인스턴스 또는 테넌트, Calgary 버전 이상
- ServiceNow Express의 경우 ServiceNow Express의 인스턴스, Helsinki 버전 이상

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [1개월 평가판을 얻을](https://azure.microsoft.com/pricing/free-trial/) 수 있습니다.


## <a name="assigning-users-to-servicenow"></a>ServiceNow에 사용자 할당

Azure Active Directory는 "할당"이라는 개념을 사용하여 어떤 사용자가 선택한 앱에 대한 액세스를 받아야 하는지를 판단합니다. 자동 사용자 계정 프로비전의 컨텍스트에서는 Azure AD의 애플리케이션에 “할당된” 사용자 및 그룹만 동기화됩니다.

프로비전 서비스를 구성하고 사용하도록 설정하기 전에 ServiceNow 앱 액세스 권한이 필요한 사용자를 나타내는 Azure AD의 사용자 및/또는 그룹을 결정해야 합니다. 결정했으면 다음 지침에 따라 이러한 사용자를 ServiceNow 앱에 할당할 수 있습니다. [엔터프라이즈 앱에 사용자 또는 그룹 할당](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)


> [!IMPORTANT]
>*   단일 Azure AD 사용자를 ServiceNow에 할당하여 프로비저닝 구성을 테스트하는 것이 좋습니다. 추가 사용자 및/또는 그룹은 나중에 할당할 수도 있습니다.
>*   ServiceNow에 사용자를 할당할 때는 유효한 사용자 역할을 선택해야 합니다. "기본 액세스" 역할은 프로비전에 작동하지 않습니다.

## <a name="enable-automated-user-provisioning"></a>자동 사용자 프로비전 사용

이 섹션에서는 Azure AD를 ServiceNow의 사용자 계정 프로비전 API에 연결하고, Azure AD의 사용자 및 그룹 할당을 기반으로 ServiceNow에서 할당된 사용자 계정을 만들고 업데이트하고 비활성화하도록 프로비전 서비스를 구성하는 방법을 안내합니다.

> [!TIP]
>[Azure Portal](https://portal.azure.com)에 제공된 지침에 따라 ServiceNow에 SAML 기반 Single Sign-On을 사용하도록 설정할 수도 있습니다. Single Sign-On은 자동 프로비전과 별개로 구성할 수 있습니다. 하지만 이 두 가지 기능은 서로 보완적입니다.

### <a name="configure-automatic-user-account-provisioning"></a>자동 사용자 계정 프로비전 구성

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory &gt; 엔터프라이즈 앱 &gt; 모든 애플리케이션** 섹션으로 이동합니다.

1. 이미 ServiceNow에 Single Sign-On을 구성한 경우 검색 필드를 사용하여 ServiceNow의 인스턴스를 검색합니다. 그렇지 않으면 **추가**를 선택하고 애플리케이션 갤러리에서 **ServiceNow**를 검색합니다. 검색 결과에서 ServiceNow를 선택하고 애플리케이션 목록에 추가합니다.

1. ServiceNow의 인스턴스를 선택한 다음 **프로비전** 탭을 선택합니다.

1. **프로비전 모드**를 **자동**으로 설정합니다. 

    ![프로비전](./media/servicenow-provisioning-tutorial/provisioning.png)

1. 관리자 자격 증명 섹션에서 다음 단계를 수행합니다.
   
    a. **ServiceNow 인스턴스 이름** 텍스트 상자에 ServiceNow 인스턴스 이름을 입력합니다.

    b. **ServiceNow 관리자 사용자 이름** 텍스트 상자에 관리자의 사용자 이름을 입력합니다.

    다. **ServiceNow 관리자 암호** 텍스트 상자에 관리자의 암호를 입력합니다.

1. Azure Portal에서 **연결 테스트**를 클릭하여 Azure AD가 ServiceNow 앱에 연결할 수 있는지 확인합니다. 연결에 실패하면 ServiceNow 계정에 팀 관리자 권한이 있는지 확인하고 **"관리자 자격 증명"** 단계를 다시 시도합니다.

1. 프로비전 오류 알림을 받을 개인 또는 그룹의 이메일 주소를 **알림 메일** 필드에 입력하고 확인란을 선택합니다.

1. **저장**을 클릭합니다.

1. 매핑 섹션에서 **Azure Active Directory 사용자를 ServiceNow에 동기화**를 선택합니다.

1. **특성 매핑** 섹션에서 Azure AD에서 ServiceNow로 동기화되는 사용자 특성을 검토합니다. **일치** 속성으로 선택한 특성은 업데이트 작업 시 ServiceNow의 사용자 계정을 일치시키는 데 사용됩니다. 저장 단추를 선택하여 변경 내용을 커밋합니다.

1. ServiceNow에 대한 Azure AD 프로비전 서비스를 사용하도록 설정하려면 설정 섹션에서 **프로비전 상태**를 **켜기**로 변경합니다.

1. **저장**을 클릭합니다.

사용자 및 그룹 섹션에서 Provisioning Status에 할당된 모든 사용자 및/또는 그룹의 초기 동기화가 시작됩니다. 초기 동기화는 서비스가 실행되는 동안 약 40분마다 발생하는 후속 동기화보다 더 많은 시간이 걸립니다. **동기화 세부 정보** 섹션을 사용하여 진행 상태를 모니터링하고 링크를 클릭하여 ServiceNow 앱의 프로비저닝 서비스에서 수행한 모든 작업을 설명하는 프로비저닝 활동 로그를 확인할 수 있습니다.

Azure AD 프로비저닝 로그를 읽는 방법에 대한 자세한 내용은 [자동 사용자 계정 프로비저닝에 대한 보고](../manage-apps/check-status-user-account-provisioning.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리](tutorial-list.md)
* [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)
* [Single Sign-On 구성](servicenow-tutorial.md)


