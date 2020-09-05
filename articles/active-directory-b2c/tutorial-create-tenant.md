---
title: '자습서: Azure Active Directory B2C 테넌트 만들기'
description: 이 자습서에 따라 Azure Portal을 사용하여 Azure Active Directory B2C 테넌트를 만들어 애플리케이션 등록을 준비하는 방법을 알아봅니다.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fbccbcf1ac85b63c5610b9904a84e5e6e3fb6c63
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922197"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>자습서: Azure Active Directory B2C 테넌트 만들기

애플리케이션이 Azure AD B2C(Azure Active Directory B2C)와 상호 작용하려면 먼저 사용자가 관리하는 테넌트에 등록되어야 합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트 만들기
> * 구독에 테넌트 연결
> * Azure AD B2C 테넌트를 포함하는 디렉터리로 전환합니다.
> * Azure AD B2C 리소스를 Azure Portal의 **즐겨찾기**로 추가합니다.

애플리케이션을 등록하는 방법은 다음 자습서에서 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-an-azure-ad-b2c-tenant"></a>Azure AD B2C 테넌트 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다. 구독 내에서 적어도 [기여자](../role-based-access-control/built-in-roles.md) 역할 또는 구독 내의 리소스 그룹에 할당된 Azure 계정으로 로그인합니다.

1. 구독이 포함된 디렉터리를 선택합니다.

    Azure Portal 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, 구독이 포함된 디렉터리를 선택합니다. 이 디렉터리는 Azure AD B2C 테넌트가 포함될 디렉터리와는 다릅니다.

    ![구독 테넌트, 구독 테넌트가 선택된 디렉터리 + 구독 필터](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. Azure Portal 메뉴 또는 **홈**페이지에서 **리소스 만들기**를 선택합니다.
1. **Active Directory B2C**를 검색한 다음, **만들기**를 선택합니다.
1. **새 Azure AD B2C 테넌트 만들기**를 선택합니다.

    ![Azure Portal에서 선택한 새 Azure AD B2C 테넌트 만들기](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. **디렉터리 만들기** 페이지에서 다음 정보를 입력합니다.

   - **조직 이름** - Azure AD B2C 테넌트의 이름을 입력합니다.
   - **초기 도메인 이름** - Azure AD B2C 테넌트의 도메인 이름을 입력합니다.
   - **국가 또는 지역** - 목록에서 국가 또는 지역을 선택합니다. 이 이름은 나중에 변경할 수 없습니다.
   - **구독** - 목록에서 구독을 선택합니다.
   - **리소스 그룹** - 테넌트를 포함하는 리소스 그룹을 선택합니다. 또는 **새로 만들기**에서 리소스 그룹에 사용할 **이름**을 입력하고 **리소스 그룹 위치**를 선택한 다음, **확인**을 선택합니다.

    ![Azure Portal에서 예제 값을 사용하여 테넌트 양식 만들기](media/tutorial-create-tenant/review-and-create-tenant.png)

1. **검토 + 만들기**를 선택합니다.
1. 디렉터리 설정을 검토합니다. 그런 다음 **만들기**를 선택합니다.

청구 목적으로 여러 Azure AD B2C 테넌트를 단일 Azure 구독에 연결할 수 있습니다. 테넌트를 연결하려면 Azure AD B2C 테넌트의 관리자여야 하고 Azure 구독 내에서 적어도 기여자 역할을 할당받아야 합니다. [구독에 Azure AD B2C 테넌트 연결](billing.md#link-an-azure-ad-b2c-tenant-to-a-subscription)을 참조하세요.

## <a name="select-your-b2c-tenant-directory"></a>B2C 테넌트 디렉터리 선택

새 Azure AD B2C 테넌트 사용을 시작하려면 테넌트가 포함된 디렉터리로 전환해야 합니다.

Azure Portal의 맨 위 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.

처음에 새 Azure B2C 테넌트가 목록에 표시되지 않으면 브라우저 창을 새로 고친 다음, 맨 위 메뉴에서 **디렉터리 + 구독** 필터를 다시 선택합니다.

![B2C 테넌트 - Azure Portal에서 선택한 디렉터리를 포함합니다.](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Azure AD B2C를 즐겨찾기로 추가(선택 사항)

이 선택적 단계를 사용하면 다음 및 모든 후속 자습서에서 Azure AD B2C 테넌트를 더 쉽게 선택할 수 있습니다.

테넌트로 작업할 때마다 **모든 서비스**에서 *Azure AD B2C*를 검색하는 대신 리소스를 즐겨찾기에 추가할 수 있습니다. 그런 다음, 포털 메뉴의 **즐겨찾기** 섹션에서 이를 선택하여 Azure AD B2C 테넌트를 빠르게 찾아볼 수 있습니다.

이 작업은 한 번만 수행하면 됩니다. 이러한 단계를 수행하기 전에 이전 섹션인 [B2C 테넌트 디렉터리 선택](#select-your-b2c-tenant-directory)에서 설명한 대로 Azure AD B2C 테넌트를 포함하는 디렉터리로 전환했는지 확인합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Portal 메뉴에서 **모든 서비스**를 선택합니다.
1. **모든 서비스** 검색 상자에서 **Azure AD B2C**를 검색하고 검색 결과 위로 마우스를 이동한 다음, 도구 설명의 별표 아이콘을 선택합니다. 이제 **Azure AD B2C**가 **즐겨찾기** 아래의 Azure Portal에 표시됩니다.
1. 새 즐겨찾기의 위치를 변경하려면 Azure Portal 메뉴로 이동하여 **Azure AD B2C**를 선택한 다음, 원하는 위치로 끌어 놓습니다.

    ![Azure AD B2C, 즐겨찾기 메뉴, Microsoft Azure Portal](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * Azure AD B2C 테넌트 만들기
> * 구독에 테넌트 연결
> * Azure AD B2C 테넌트를 포함하는 디렉터리로 전환합니다.
> * Azure AD B2C 리소스를 Azure Portal의 **즐겨찾기**로 추가합니다.

다음으로, 새 테넌트에 웹 애플리케이션을 등록하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [애플리케이션 등록 >](tutorial-register-applications.md)
