---
title: 셀프 서비스 애플리케이션 할당을 구성하는 방법 | Microsoft Docs
description: 셀프 서비스 애플리케이션 액세스를 활성화하여 사용자가 자신의 애플리케이션을 찾을 수 있도록 함
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/20/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa1016307cc6e838e2e1136f6bbacd82d2f8c82e
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640755"
---
# <a name="how-to-configure-self-service-application-assignment"></a>셀프 서비스 애플리케이션 할당을 구성하는 방법

사용자가 내 앱에서 응용 프로그램을 자체 검색할 수 있으려면 먼저 사용자가 직접 검색 하 고 액세스를 요청할 수 있도록 하려는 모든 응용 프로그램에 대 한 **셀프 서비스 응용 프로그램 액세스** 를 사용 하도록 설정 해야 합니다. 이 기능은 [AZURE AD 갤러리](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app)에서 추가 된 응용 프로그램, [azure AD 응용 프로그램 프록시](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) 또는 [사용자 또는 관리자 동의](https://docs.microsoft.com/azure/active-directory/develop/application-consent-experience)를 통해 추가 된 응용 프로그램에 사용할 수 있습니다. 

이 기능은 IT 그룹이 시간과 비용을 절감하는 유용한 방법이며, Azure Active Directory를 사용하는 최신 애플리케이션 배포의 일부로 사용하는 것이 좋습니다.

이 기능을 사용하면 다음을 수행할 수 있습니다.

-   사용자가 IT 그룹을 신경 하지 않고 [내 앱](https://myapps.microsoft.com/) 에서 응용 프로그램을 자동으로 검색할 수 있습니다.

-   액세스를 요청한 사용자가 누군지 알고, 액세스를 제거하고, 할당된 역할을 관리할 수 있도록 미리 구성된 그룹에 해당 사용자를 추가합니다.

-   필요에 따라 비즈니스 승인자가 애플리케이션 액세스 요청을 승인할 수 있도록 합니다. 그러면 IT 그룹에서 이러한 작업을 수행할 필요가 없습니다.

-   필요에 따라 이 애플리케이션에 대한 액세스를 승인할 수 있는 최대 10명의 개별 사용자를 구성합니다.

-   필요에 따라 비즈니스 승인자가 비즈니스 승인자의 [내 앱](https://myapps.microsoft.com/)에서 바로 응용 프로그램에 로그인 하는 데 사용할 수 있는 암호를 설정할 수 있습니다.

-   필요에 따라 자동으로 셀프 서비스 할당 사용자를 애플리케이션 역할에 직접 할당합니다.

> [!NOTE]
> 사용자가 셀프 서비스 앱 가입을 요청 하 고 소유자가 요청을 승인 또는 거부 하도록 요청 하려면 Azure Active Directory Premium (P1 또는 P2) 라이선스가 필요 합니다. Azure Active Directory Premium 라이선스가 없는 사용자는 셀프 서비스 앱을 추가할 수 없습니다.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>셀프 서비스 애플리케이션 액세스를 활성화하여 사용자가 자신의 애플리케이션을 찾을 수 있도록 함

셀프 서비스 응용 프로그램 액세스는 사용자가 응용 프로그램을 직접 검색할 수 있도록 하 고, 필요에 따라 비즈니스 그룹이 해당 응용 프로그램에 대 한 액세스를 승인할 수 있도록 하는 좋은 방법입니다. 암호 single sign-on 응용 프로그램의 경우 비즈니스 그룹이 자신의 내 앱 액세스 패널에서 해당 사용자에 게 할당 된 자격 증명을 관리 하도록 허용할 수도 있습니다.

애플리케이션에 대한 셀프 서비스 애플리케이션 액세스를 사용하려면 다음 단계를 수행합니다.

1. 전역 관리자 권한으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.

2. **Azure Active Directory**를 선택합니다. 왼쪽 탐색 메뉴에서 **엔터프라이즈 애플리케이션**을 선택합니다.

3. 목록에서 애플리케이션을 선택합니다. 애플리케이션이 표시되지 않으면 검색 상자에 해당 이름을 입력합니다. 또는 필터 컨트롤을 사용하여 애플리케이션 종류, 상태 또는 표시 유형을 선택한 다음, **적용**을 선택합니다.

4. 왼쪽 탐색 메뉴에서 **셀프 서비스**를 선택 합니다.

5. 이 애플리케이션에 대한 셀프 서비스 애플리케이션 액세스를 활성화하려면 **사용자가 이 애플리케이션에 대한 액세스를 요청하도록 허용하시겠습니까?** 토글을 **예**로 전환합니다.

6. 사용자를 **할당 해야 하는 그룹**옆에 있는 **그룹 선택**을 클릭 합니다. 그룹을 선택 하 고 **선택**을 클릭 합니다. 사용자의 요청이 승인 되 면이 그룹에 추가 됩니다. 이 그룹의 멤버 자격을 볼 때 셀프 서비스 액세스를 통해 응용 프로그램에 대 한 액세스 권한이 부여 된 사용자를 볼 수 있습니다.
  
    > [!NOTE]
    > 이 설정은 온-프레미스에서 동기화 된 그룹을 지원 하지 않습니다.

7. **선택 사항:** 사용자가 액세스를 허용 하기 전에 비즈니스 승인을 요구 하려면 **이 응용 프로그램에 대 한 액세스 권한을 부여 하기 전에 승인 필요** 를 설정 하 고 **예**로 전환 합니다.

8. **선택 사항: 암호 single sign-on을 사용 하는 응용 프로그램의 경우** 비즈니스 승인자가 승인 된 사용자에 대해이 응용 프로그램으로 전송 되는 암호를 지정할 수 있도록 허용 하려면 **승인자가이 응용 프로그램에 대 한 사용자의 암호를 설정 하도록 허용 하 시겠습니까?** 를 **예**로 설정 합니다.

9. **선택 사항:** 이 응용 프로그램에 대 한 액세스를 승인 하도록 허용 된 **사용자**에 게이 응용 프로그램에 대 한 액세스를 승인 하도록 허용 된 비즈니스 승인자를 지정 하려면 **승인자 선택**을 클릭 하 고 최대 10 개의 개별 비즈니스 승인자를 선택 합니다. 그런 다음 **선택**을 클릭합니다.

    >[!NOTE]
    >그룹은 지원되지 않습니다. 개별 비즈니스 승인자를 최대 10 개까지 선택할 수 있습니다. 여러 승인자를 지정 하는 경우 모든 단일 승인자는 액세스 요청을 승인할 수 있습니다.

10. **선택 사항:** **역할을 표시 하는 응용 프로그램의**경우 사용자에 게 **이 응용 프로그램에서 사용자에 게 할당 되는 역할에**대 한 셀프 서비스 승인 사용자를 할당 하려면 **역할 선택**을 클릭 하 고 해당 사용자를 할당할 역할을 선택 합니다. 그런 다음 **선택**을 클릭합니다.

11. 창 위쪽에서 **저장** 단추를 클릭하여 완료합니다.

셀프 서비스 응용 프로그램 구성을 완료 한 후에는 사용자가 [내 앱](https://myapps.microsoft.com/) 으로 이동 하 여 셀프 서비스 **앱 추가** 단추를 클릭 하 여 셀프 서비스 액세스를 사용 하도록 설정 된 앱을 찾을 수 있습니다. 비즈니스 승인자는 [내 앱](https://myapps.microsoft.com/)에서도 알림을 볼 수 있습니다. 사용자가 승인이 필요한 애플리케이션에 대한 액세스를 요청한 경우 비즈니스 승인자에게 알리는 이메일을 활성화할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[셀프 서비스 그룹 관리를 위한 Azure Active Directory 설정](../users-groups-roles/groups-self-service-management.md)
