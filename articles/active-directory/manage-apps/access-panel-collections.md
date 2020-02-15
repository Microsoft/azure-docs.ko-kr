---
title: Azure Active Directory에서 내 앱 포털에 대 한 컬렉션 만들기 | Microsoft Docs
description: 내 앱 컬렉션을 사용 하 여 최종 사용자에 게 더 간단한 내 앱 환경을 위한 내 앱 페이지를 사용자 지정할 수 있습니다. 별도의 탭을 사용 하 여 응용 프로그램을 그룹으로 구성 합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: c91b9ffc9e3487e492c91cb0f5825d0b725f9410
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120090"
---
# <a name="create-collections-on-the-my-apps-portal"></a>내 앱 포털에서 컬렉션 만들기

사용자는 My Apps 포털을 사용 하 여 액세스 권한이 있는 클라우드 기반 응용 프로그램을 보고 시작할 수 있습니다. 기본적으로 사용자가 액세스할 수 있는 모든 응용 프로그램은 단일 페이지에 함께 나열 됩니다. 사용자에 대해이 페이지를 더 잘 구성 하려면 P1 또는 P2 라이선스가 Azure AD Premium 경우 컬렉션을 설정할 수 있습니다. 컬렉션을 사용 하 여 관련 된 응용 프로그램 (예: 작업 역할, 작업 또는 프로젝트)을 함께 그룹화 하 고 별도의 탭에 표시할 수 있습니다. 컬렉션은 기본적으로 사용자가 액세스할 수 있는 응용 프로그램에 필터를 적용 하므로 사용자는 컬렉션에서 해당 응용 프로그램에 할당 된 응용 프로그램만 볼 수 있습니다.

> [!NOTE]
> 이 문서에서는 관리자가 컬렉션을 사용 하도록 설정 하 고 만드는 방법을 설명 합니다. 내 앱 포털 및 컬렉션을 사용 하는 방법에 대 한 최종 사용자 정보는 [컬렉션 액세스 및 사용](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces)을 참조 하세요.

## <a name="enable-the-latest-my-apps-features"></a>최신 내 앱 기능 사용

1. [**Azure Portal**](https://portal.azure.com/) 를 열고 사용자 관리자 또는 전역 관리자 권한으로 로그인 합니다.

2. **Azure Active Directory** > **사용자 설정**으로 이동 합니다.

3. **사용자 기능**미리 보기에서 **사용자 기능 미리 보기 설정 관리**를 선택 합니다.

4. **사용자가 내 앱의 미리 보기 기능을 사용할 수 있음**에서 다음 옵션 중 하나를 선택 합니다.
   * **Selected** -특정 그룹에 대해 기능을 사용 하도록 설정 합니다. **그룹 선택** 옵션을 사용 하 여 기능을 사용 하도록 설정할 그룹을 선택할 수 있습니다.  
   * **모두** -모든 사용자에 대해 기능을 사용 하도록 설정 합니다.

> [!NOTE]
> 사용자는 내 앱 포털을 열기 위해 `https://myapps.microsoft.com/contoso.com`와 같은 조직에 대 한 사용자 지정 링크 또는 링크 `https://myapps.microsoft.com` 사용할 수 있습니다. 새 내 앱 환경을 사용 하도록 설정 하 고 나면 **업데이트 된 내 응용 프로그램 환경을 사용할 수** 있습니다. 배너는 내 앱 페이지의 맨 위에 표시 되 고 사용자는 **체험을 선택 하** 여 새 환경을 볼 수 있습니다. 새 환경 사용을 중지 하기 위해 사용자는 페이지 맨 위에 있는 **새 환경 그대로** 배너에서 **예** 를 선택할 수 있습니다.

## <a name="create-a-collection"></a>컬렉션 만들기

컬렉션을 만들려면 Azure AD Premium P1 또는 P2 라이선스가 있어야 합니다.

1. [**Azure Portal**](https://portal.azure.com/) 를 열고 Azure AD Premium P1 또는 P2 라이선스를 사용 하 여 관리자로 로그인 합니다.

2. **Azure Active Directory** > **Enterprise 응용 프로그램**으로 이동 합니다.

3. **관리**아래에서 **컬렉션**을 선택 합니다.

4. **새 컬렉션**을 선택 합니다. **새 컬렉션** 페이지에서 컬렉션의 **이름을** 입력 합니다. 이름에 "collection"을 사용 하지 않는 것이 좋습니다. 그런 다음 **설명을**입력 합니다.

   ![새 컬렉션 페이지](media/acces-panel-collections/new-collection.png)

5. **응용** 프로그램 탭을 선택 합니다. **+ 응용 프로그램**추가를 선택 하 고 **응용 프로그램 추가** 페이지에서 컬렉션에 추가 하려는 모든 응용 프로그램을 선택 하거나 **검색** 상자를 사용 하 여 응용 프로그램을 찾습니다.

   ![컬렉션에 응용 프로그램 추가](media/acces-panel-collections/add-applications.png)

6. 응용 프로그램 추가가 완료 되 면 **추가**를 선택 합니다. 선택한 응용 프로그램 목록이 표시 됩니다. 위쪽 화살표를 사용 하 여 목록에서 응용 프로그램의 순서를 변경할 수 있습니다. 응용 프로그램을 아래로 이동 하거나 컬렉션에서 삭제 하려면 **추가** 메뉴 ( **...** )를 선택 합니다.

7. **소유자** 탭을 선택 합니다. **+ 사용자 및 그룹 추가**를 선택 하 고 **사용자 및 그룹 추가** 페이지에서 소유권을 할당 하려는 사용자 또는 그룹을 선택 합니다. 사용자 및 그룹 선택을 완료 한 후 **선택**을 선택 합니다.

9. **사용자 및 그룹** 탭을 선택 합니다. **+ 사용자 및 그룹 추가**를 선택 하 고 **사용자 및 그룹 추가** 페이지에서 컬렉션을 할당 하려는 사용자 또는 그룹을 선택 합니다. 또는 **검색** 상자를 사용 하 여 사용자 또는 그룹을 찾을 수 있습니다. 사용자 및 그룹 선택을 완료 한 후 **선택**을 선택 합니다.

   ![사용자 및 그룹 추가](media/acces-panel-collections/add-users-and-groups.png)

11. **검토 + 만들기**를 선택합니다. 새 컬렉션의 속성이 표시 됩니다.


## <a name="view-audit-logs"></a>감사 로그 보기

감사 로그는 컬렉션 생성 최종 사용자 동작을 포함 하 여 내 앱 컬렉션 작업을 기록 합니다. 내 앱에서 생성 되는 이벤트는 다음과 같습니다.

* 컬렉션 만들기
* 컬렉션 편집
* 컬렉션 삭제
* 응용 프로그램 시작 (최종 사용자)
* 셀프 서비스 응용 프로그램 추가 (최종 사용자)
* 셀프 서비스 응용 프로그램 삭제 (최종 사용자)

작업 섹션에서 **Azure Active Directory** > **엔터프라이즈 응용 프로그램** > **감사 로그** 를 선택 하 여 [Azure Portal](https://portal.azure.com) 에서 감사 로그에 액세스할 수 있습니다. **서비스**의 경우 **내 앱**을 선택 합니다.

## <a name="get-support-for-my-account-pages"></a>내 계정 페이지에 대 한 지원 받기

내 앱 페이지에서 사용자 **는 내 계정을 선택 하** > **내 계정을 확인** 하 여 계정 설정을 열 수 있습니다. Azure AD **내 계정** 페이지에서 사용자는 보안 정보, 장치, 암호 등을 관리할 수 있습니다. 또한 회사 계정 설정에 액세스할 수 있습니다.

Azure AD 계정 페이지 또는 Office 계정 페이지에서 문제에 대 한 지원 요청을 제출 해야 하는 경우 다음 단계에 따라 요청이 적절히 라우팅됩니다. 

* **AZURE AD "내 계정"** 페이지의 문제에 대 한 자세한 내용은 Azure Portal 내에서 지원 요청을 여세요. **Azure Portal** > **Azure Active Directory** **새 지원 요청** > 로 이동 합니다.

* **Office "내 계정"** 페이지의 문제를 보려면 Microsoft 365 관리 센터 내에서 지원 요청을 여세요. **Microsoft 365 관리 센터** > **지원**으로 이동 합니다. 

## <a name="next-steps"></a>다음 단계
[Azure Active Directory의 응용 프로그램에 대 한 최종 사용자 환경](end-user-experiences.md)