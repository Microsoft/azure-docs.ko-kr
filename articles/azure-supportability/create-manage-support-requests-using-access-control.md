---
title: Azure 역할 기반 Access Control(RBAC)로 지원 요청을 만들고 관리하기 위한 액세스 권한 제어 | Microsoft Docs
description: Azure 역할 기반 Access Control(RBAC)로 지원 요청을 만들고 관리하기 위한 액세스 권한 제어
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: azure
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: d98d0637c6d520193b11f4267c59016772ef063a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809928"
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Azure 역할 기반 Access Control(RBAC)로 지원 요청을 만들고 관리하기 위한 액세스 권한 제어

[RBAC(역할 기반 Access Control)](https://docs.microsoft.com/azure/role-based-access-control/overview)를 통해 Azure에 대한 세밀한 액세스 관리가 가능합니다.
Azure Portal([portal.azure.com](https://portal.azure.com))에서 지원 요청 생성은 Azure의 RBAC 모델을 사용하여 지원 요청을 만들고 관리할 수 잇는 사용자를 정의합니다.
특정 범위(구독, 리소스 그룹 또는 리소스일 수 있음)에서 사용자, 그룹 및 애플리케이션에 적절한 RBAC 역할을 할당하여 액세스 권한을 부여합니다.

예를 들어를 보겠습니다. 리소스 그룹 소유자는 구독 범위에서 읽기 권한이 있는 웹 사이트, virtual machines 및 서브넷과 같은 리소스 그룹 아래에 있는 모든 리소스를 관리할 수 있습니다.
하지만 가상 머신 리소스에 대해 지원 요청을 생성하려고 하는 경우 다음 오류가 발생합니다.

![구독 오류](./media/create-manage-support-requests-using-access-control/subscription-error.png)

지원 요청 관리에서는 구독 범위에서 지원 요청을 만들고 관리할 수 있도록 지원 작업 Microsoft.Support/*를 포함하는 역할 또는 쓰기 권한이 필요합니다.

다음 문서에서는 Azure의 RBAC(역할 기반 Access Control)를 사용하여 Azure Portal에서 지원 요청을 만들고 관리하는 방법을 설명합니다.

## <a name="getting-started"></a>시작하기

위의 예를 사용하여 구독 소유자에 의해 구독에 사용자 지정 RBAC 역할이 할당된 경우 리소스에 대한 지원 요청을 만들 수 있습니다.
Azure PowerShell, Azure 명령줄 인터페이스(CLI) 및 REST API를 사용하여 [사용자 지정 RBAC 역할](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/)을 만들 수 있습니다.

사용자 지정 역할의 작업 속성은 해당 역할에 액세스 권한이 부여되는 Azure 작업을 지정합니다.
지원 요청 관리를 위한 사용자 지정 역할을 만들려면 역할에 Microsoft.Support/* 작업이 있어야 합니다.

지원 요청을 만들고 관리하는 데 사용할 수 있는 사용자 지정 역할 예는 다음과 같습니다.
이 역할을 "지원 요청 참여자"로 명명하고 이 문서에서 사용자 지정 역할을 이 용어로 나타냅니다.

``` Json
{
    "Name":  "Support Request Contributor",
    "Id":  "1f2aad59-39b0-41da-b052-2fb070bd7942",
    "IsCustom":  true,
    "Description":  "Lets you create and manage support tickets.",
    "Actions":  [
                    "Microsoft.Support/*"
                ],
    "NotActions":  [
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

[이 비디오](https://www.youtube.com/watch?v=-PaBaDmfwKI)에 간략히 설명된 단계에 따라 구독에 대해 사용자 지정 역할을 만드는 방법을 알아봅니다.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Azure Portal에서 지원 요청 만들기 및 관리

예를 들어 보겠습니다. "Visual Studio MSDN 구독"의 소유자라고 합시다.
Joe는 이 구독에서 일부 리소스 그룹에 대한 리소스 소유자인 여러분의 동료이며 구독에 대해 읽기 권한을 보유합니다.
동료인 Joe가 이 구독 아래 리소스에 대한 지원 티켓을 만들고 관리할 수 있도록 액세스 권한을 부여하고 싶습니다.

1. 먼저 구독으로 이동하고 "설정" 아래에서 사용자 목록을 확인합니다. 구독에 대해 읽기 권한이 있는 Joe를 클릭하고 새 사용자 지정 역할을 할당해 보겠습니다.

    ![역할 추가](./media/create-manage-support-requests-using-access-control/add-role.png)

2. "사용자" 블레이드에서 [추가]를 클릭합니다. 역할 목록에서 사용자 지정 역할 "지원 요청 참여자"를 선택합니다.

    ![지원 참여자 역할 추가](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. 역할 이름을 선택한 후 "사용자 추가"를 클릭하고 Joe의 전자 메일 자격 증명을 입력합니다. [선택]을 클릭합니다.

    ![사용자 추가](./media/create-manage-support-requests-using-access-control/add-users.png)

4. [확인]을 클릭하여 계속합니다.

    ![액세스 권한 추가](./media/create-manage-support-requests-using-access-control/add-access.png)

5. 이제 자신의 소유인 구독 아래에 사용자 지정 역할 "지원 요청 참여자"가 새로 추가된 사용자를 볼 수 있습니다.

    ![추가된 사용자](./media/create-manage-support-requests-using-access-control/user-added.png)

    Joe가 포털에 로그인하면 자신이 추가된 구독을 볼 수 있습니다.

7. Joe는 "도움말 및 지원" 블레이드에서 "새 지원 요청"을 클릭하고 "Visual Studio Ultimate with MSDN"에 대한 지원 요청을 만들 수 있습니다.

    ![새 지원 요청](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Joe가 "모든 지원 요청"을 클릭하면 이 구독에 대해 생성된 모든 지원 요청을 확인할 수 있습니다. ![사례 정보 보기](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Azure Portal에서 지원 요청 액세스 제거

사용자에게 지원 요청을 만들고 관리하기 위한 액세스 권한을 부여할 수 있었던 것처럼 사용자에 대한 액세스 권한을 제거할 수도 있습니다.
지원 요청을 만들고 관리할 수 있는 기능을 제거하려면 구독으로 이동하고 "설정"을 클릭하고 사용자(이 경우, Joe)를 클릭합니다.
역할 이름, "지원 요청 참여자"를 마우스 오른쪽 단추로 클릭하고 "제거"를 클릭합니다.

![지원 요청 액세스 제거](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

Joe가 포털에 로그인하고 지원 요청을 만들려고 하면 다음 오류가 발생합니다.

![구독 오류-2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Joe가 "모든 지원 요청"을 클릭할 때 지원 요청을 볼 수 없습니다.

![사례 정보 보기-2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
