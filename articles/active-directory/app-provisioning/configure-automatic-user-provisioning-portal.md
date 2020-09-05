---
title: Azure AD에서 엔터프라이즈 앱에 대한 사용자 프로비저닝 관리
description: Azure Active Directory를 사용하여 엔터프라이즈 앱에 대한 사용자 계정 프로비전을 관리하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 11/25/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 9c42a83b4f7f3c6b5ff501525a04ebd96c2a692a
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88234842"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Azure Portal에서 엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리

이 문서에서는 자동 사용자 계정 프로비저닝 및 지원되는 애플리케이션에 대한 프로비저닝 해제를 관리하는 방법을 설명합니다. *사용자 계정 프로비전*은 애플리케이션의 로컬 사용자 프로필 저장소에 사용자 계정 레코드를 생성, 업데이트 및/또는 비활성화하는 작업입니다. 대부분의 클라우드 및 SaaS 애플리케이션은 사용자의 고유한 로컬 사용자 프로필 저장소에 사용자 역할 및 권한을 저장하고, 사용자 로컬 저장소의 그러한 사용자 레코드의 존재는 Single Sign-On 및 작업에 대한 액세스를 위해 *필요*합니다. 자동 사용자 계정 프로비저닝에 대한 자세한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션에 사용자 프로비저닝 및 프로비저닝 해제 자동화](user-provisioning.md)를 참조하세요.

> [!IMPORTANT]
> Azure AD(Azure Active Directory)에는 Azure AD를 사용한 자동 프로비저닝이 지원되는 수천 개의 사전 통합 애플리케이션이 들어 있는 갤러리가 있습니다. 먼저 [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)에서 사용 중인 애플리케이션과 관련된 프로비저닝 설정 자습서를 찾아야 합니다. 프로비저닝 연결을 만들도록 앱과 Azure AD를 구성하는 방법에 대한 단계별 지침을 찾을 수 있습니다.

## <a name="finding-your-apps-in-the-portal"></a>포털에서 앱 찾기

Azure Active Directory 포털을 사용하여 디렉터리에서 Single Sign-On용으로 구성된 모든 애플리케이션을 보고 관리할 수 있습니다. 엔터프라이즈 앱은 조직 내에서 배포 및 사용되는 앱입니다. 엔터프라이즈 애플리케이션을 확인하고 관리하려면 다음 단계를 따르세요.

1. [Azure Active Directory 포털](https://aad.portal.azure.com)을 엽니다.
1. 왼쪽 창에서 **엔터프라이즈 애플리케이션**을 선택합니다. 갤러리에서 추가된 앱을 포함하여 구성된 모든 앱 목록이 표시됩니다.
1. 앱을 선택하여 보고서를 보고 앱 설정을 관리할 수 있는 보고서 창을 로드합니다.
1. **프로비저닝**을 선택하여 선택한 앱의 사용자 계정 프로비저닝 설정을 관리합니다.

   ![사용자 계정 프로비저닝 설정을 관리하기 위한 프로비저닝 화면](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>프로비전 모드

**프로비전** 창은 엔터프라이즈 애플리케이션을 위해 지원되는 프로비저닝 모드를 표시하는 **모드** 메뉴로 시작하고 해당 모드를 구성할 수 있도록 합니다. 사용 가능한 옵션은 다음과 같습니다.

* **자동** - Azure AD가 이 애플리케이션에 대해 사용자 계정의 자동 API 기반의 프로비저닝 또는 프로비저닝 해제를 지원하는 경우 이 옵션이 나타납니다. 관리자에게 도움이 되는 인터페이스를 표시하려면 이 모드를 선택합니다.

  * 애플리케이션의 사용자 관리 API에 연결하도록 Azure AD 구성
  * Azure AD와 앱 간에 사용자 계정 데이터가 흐르는 방식을 정의하는 계정 매핑 및 워크플로 만들기
  * Azure AD 프로비저닝 서비스 관리

* **수동** - Azure AD가 이 애플리케이션에 대한 사용자 계정의 자동 프로비저닝을 지원하지 않을 경우 이 옵션이 표시됩니다. 이 경우 애플리케이션에 저장된 사용자 계정 레코드를 해당 애플리케이션에서 제공한 사용자 관리 및 프로비저닝 기능(SAML Just-In-Time 프로비저닝 포함 가능)에 따라 외부 프로세스를 사용하여 관리해야 합니다.

## <a name="configuring-automatic-user-account-provisioning"></a>자동 사용자 계정 프로비전 구성

**자동** 옵션을 선택하여 관리자 자격 증명, 매핑, 시작/중지 및 동기화에 대한 설정을 지정합니다.

### <a name="admin-credentials"></a>관리자 자격 증명

**관리자 자격 증명**을 확장하여 Azure AD가 애플리케이션의 사용자 관리 API에 연결하는 데 필요한 자격 증명을 입력합니다. 필요한 입력은 애플리케이션에 따라 다릅니다. 특정 애플리케이션에 대한 자격 증명 형식 및 요구 사항에 대해 알아보려면 [특정 애플리케이션에 대한 구성 자습서](user-provisioning.md)를 참조하세요.

**연결 테스트**를 선택하면 제공된 자격 증명을 사용하여 Azure AD에서 해당 앱의 프로비저닝 앱에 연결해 보면서 자격 증명을 테스트할 수 있습니다.

### <a name="mappings"></a>매핑

**매핑**을 확장하여 사용자 계정이 프로비저닝되거나 업데이트될 때 Azure AD와 대상 애플리케이션 간에 흐르는 사용자 특성을 보고 편집할 수 있습니다.

Azure AD 사용자 개체와 각 SaaS 앱의 사용자 개체 간의 미리 구성된 매핑 세트가 있습니다. 일부 앱은 그룹이나 연락처 같은 다른 유형의 개체를 관리합니다. 테이블에서 매핑을 선택하여 오른쪽에 있는 매핑 편집기를 엽니다. 이 편집기에서 매핑을 보고 사용자 지정할 수 있습니다.

![특성 매핑 화면 표시](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

지원되는 사용자 지정은 다음과 같습니다.

* Azure AD 사용자 개체를 SaaS 앱의 사용자 개체로 매핑하는 것과 같이 특정 개체에 대한 매핑을 사용하거나 사용하지 않도록 설정합니다.
* Azure AD 사용자 개체에서 앱의 사용자 개체로 흐르는 특성을 편집합니다. 특성 매핑에 대한 자세한 내용은 [특성 매핑 유형 이해하기](customize-application-attributes.md#understanding-attribute-mapping-types)를 참조하세요.
* Azure AD는 대상 애플리케이션에서 실행하는 프로비저닝 작업을 필터링합니다. Azure AD가 개체를 완전히 동기화하도록 하지 않고 실행되는 작업을 제한할 수 있습니다.

  예를 들어 **업데이트**만 선택하면 Azure AD가 애플리케이션에서 기존 사용자 계정을 업데이트만 하며 새 계정을 만들지는 않습니다. **만들기**만 선택하면 Azure가 새 사용자 계정을 만들기만 하고 기존 계정을 업데이트하지는 않습니다. 이 기능을 통해 관리자는 계정을 만들기 위해 여러 매핑을 만들고 워크플로를 업데이트할 수 있습니다.

* 새 특성 매핑을 추가합니다. **특성 매핑** 창의 맨 아래에 있는 **새 매핑 추가**를 선택합니다. **특성 편집** 양식을 채우고 **확인**을 선택하여 목록에 새 매핑을 추가합니다.

### <a name="settings"></a>설정

**프로비저닝** 화면의 **설정** 영역에서 선택한 애플리케이션에 대한 Azure AD 프로비저닝 서비스를 시작 및 중지할 수 있습니다. 프로비저닝 캐시를 지우고 서비스를 다시 시작하도록 선택할 수도 있습니다.

프로비전을 애플리케이션에 대해 처음으로 사용하는 경우 **프로비전 상태**를 **켜기**로 변경하여 서비스를 활성화합니다. 이렇게 변경하면 Azure AD 프로비저닝 서비스가 초기 주기를 실행합니다. **사용자 및 그룹** 섹션에 할당 된 사용자를 읽고, 대상 애플리케이션을 쿼리한 다음, Azure AD **매핑** 섹션에 정의된 프로비저닝 작업을 실행합니다. 이 과정에서 프로비저닝 서비스가 관리하는 사용자 계정에 대해 캐시된 데이터를 저장하므로 할당 범위에 들지 않는 대상 애플리케이션 내의 관리되지 않는 계정은 프로비저닝 해제 작업의 영향을 받지 않습니다. 초기 주기 후 프로비전 서비스는 40분 간격으로 사용자 및 그룹 개체를 자동으로 동기화합니다.

**프로비저닝 상태**를 **끄기**로 변경하여 프로비저닝 서비스를 일시 중지할 수 있습니다. 이 상태에서는 Azure가 앱의 어떤 사용자 또는 그룹 개체도 만들거나 업데이트하거나 제거하지 않습니다. 상태를 다시 **켜기**로 변경하면 서비스는 중단되었던 위치에서 진행됩니다.

**현재 상태 지우기 및 동기화 다시 시작**은 초기 주기를 트리거합니다. 그러면 서비스는 원본 시스템의 모든 사용자를 다시 평가하고 프로비저닝 범위에 있는지 확인합니다. 이 서비스는 애플리케이션이 현재 격리 중이거나 특성 매핑을 변경해야 하는 경우에 유용할 수 있습니다. 계산해야 하는 개체의 수 때문에 초기 주기는 일반적인 증분 주기보다 완료하는 데 더 오래 걸립니다. [여기](application-provisioning-when-will-provisioning-finish-specific-user.md)에서 초기 및 증분 주기의 성능에 대해 자세히 알아볼 수 있습니다.