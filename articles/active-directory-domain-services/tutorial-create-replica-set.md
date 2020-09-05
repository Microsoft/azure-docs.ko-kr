---
title: 자습서 - Azure AD Domain Services에서 복제본 세트 만들기 | Microsoft Docs
description: Azure AD Domain Services의 서비스 복원력을 높이기 위해 Azure Portal에서 복제본 세트를 만들고 사용하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: iainfou
ms.openlocfilehash: 6f166cdcb5f3764d7b264fdb4ebc082ece4c798b
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245097"
---
# <a name="tutorial-create-and-use-replica-sets-for-resiliency-or-geolocation-in-azure-active-directory-domain-services-preview"></a>자습서: 복제본 세트를 만들어서 Azure Active Directory Domain Services(미리 보기)의 복원력 또는 지리적 위치에 사용

Azure AD DS(Azure Active Directory Domain Services) 관리되는 도메인의 복원력을 높이기 위해 또는 애플리케이션과 가까운 지리적 위치에 추가로 배포하려면 *복제본 세트*를 사용하면 됩니다. 모든 Azure AD DS 관리되는 도메인 네임스페이스(예: *aaddscontoso.com*)에는 초기 복제본 세트가 하나 포함되어 있습니다. 다른 Azure 지역에 추가 복제본 세트를 만드는 기능은 관리되는 도메인에 지리적 복원력을 제공합니다.

Azure AD DS을 지원하는 Azure 지역의 모든 피어링된 가상 네트워크에 복제본 세트를 추가할 수 있습니다.

복제본 세트는 Azure AD Domain Services의 공개 미리 보기 기능입니다. 미리 보기로 제공되는 기능의 지원 차이점을 알고 있어야 합니다. 미리 보기에 대한 자세한 내용은 [Azure Active Directory 미리 보기 SLA](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)를 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 가상 네트워크 요구 사항 이해
> * 복제본 세트 만들기
> * 복제본 세트 삭제

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure Resource Manager 배포 모델을 사용하여 만들고 Azure AD 테넌트에 구성된 Azure Active Directory Domain Services 관리형 도메인입니다.
    * 필요한 경우 [Azure Active Directory Domain Services 관리형 도메인을 만들고 구성합니다][tutorial-create-instance].

    > [!IMPORTANT]
    > 클래식 배포 모델을 사용하여 만든 관리되는 도메인은 복제본 세트를 사용할 수 없습니다. 또한 관리되는 도메인에 대해 적어도 *Enterprise* SKU를 사용해야 합니다. 필요한 경우 [관리되는 도메인의 SKU를 변경][howto-change-sku]하세요.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

이 자습서에서는 Azure Portal을 사용하여 복제본 세트를 만들고 관리합니다. 시작하려면 먼저 [Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="networking-considerations"></a>네트워킹 고려 사항

복제본 세트를 호스트하는 가상 네트워크가 서로 통신할 수 있어야 합니다. Azure AD DS를 사용하는 애플리케이션과 서비스 역시 복제본 세트를 호스팅하는 가상 네트워크에 대한 네트워크 연결이 필요합니다. 완전한 메시형 네트워크를 만들도록 모든 가상 네트워크 사이에 Azure 가상 네트워크 피어링을 구성해야 합니다. 이렇게 피어링하면 복제본 세트 간에 사이트 간 복제를 효과적으로 수행할 수 있습니다.

Azure AD DS에서 복제본 세트를 사용하려면 다음 Azure 가상 네트워크 요구 사항을 검토하세요.

* 가상 네트워크 피어링 및 라우팅이 가능하도록 IP 주소 공간이 겹치지 않게 하세요.
* 시나리오를 지원하는 데 충분한 IP 주소를 사용하는 서브넷을 만듭니다.
* Azure AD DS에 자체 서브넷이 있어야 합니다. 이 가상 네트워크 서브넷을 애플리케이션 VM 및 서비스와 공유하지 마세요.
* 피어링된 가상 네트워크는 전이적이지 않습니다.

> [!TIP]
> Azure Portal에서 복제본 세트를 만들 때 가상 네트워크 간에 네트워크 피어링이 생성됩니다.
>
> 필요한 경우 Azure Portal에서 복제본 세트를 추가할 때 가상 네트워크 및 서브넷을 만들 수 있습니다. 또는 복제본 세트의 대상 지역에서 기존 가상 네트워크 리소스를 선택하고, 피어링이 아직 없는 경우 자동으로 생성되도록 할 수 있습니다.

## <a name="create-a-replica-set"></a>복제본 세트 만들기

*aaddscontoso.com* 같은 관리되는 도메인을 만들 때 초기 복제본 세트가 만들어집니다. 추가 복제본 세트는 동일한 네임스페이스와 구성을 공유합니다. 구성, 사용자 ID 및 자격 증명, 그룹, 그룹 정책 개체, 컴퓨터 개체를 비롯한 Azure AD DS 변경 내용과 기타 변경 내용은 AD DS 복제를 사용하는 관리되는 도메인의 모든 복제본 세트에 적용됩니다.

이 자습서에서는 Azure 지역에 초기 Azure AD DS 복제본 세트와 다른 추가 복제본 세트를 만듭니다.

추가 복제본 세트를 만들려면 다음 단계를 완료합니다.

1. Azure Portal에서 **Azure AD Domain Services**를 검색하여 선택합니다.
1. 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. 왼쪽에서 **복제본 세트(미리 보기)** 를 선택합니다. 각 관리되는 도메인에는 다음 예제 스크린샷처럼 선택한 지역에 하나의 초기 복제본 세트가 포함됩니다.

    ![Azure Portal에서 복제본 세트를 보고 추가하는 예제 스크린샷](./media/tutorial-create-replica-set/replica-set-list.png)

    추가 복제본 세트를 만들려면 **+ 추가**를 선택합니다.

1. *복제본 세트 추가* 창에서 *미국 동부*와 같은 대상 지역을 선택합니다.

    대상 지역에서 *vnet-eastus*와 같은 가상 네트워크를 선택한 다음, *aadds-subnet*과 같은 서브넷을 선택합니다. 필요한 경우 **새로 만들기**를 선택하여 대상 지역에서 가상 네트워크를 추가한 다음, **관리**를 선택하여 Azure AD DS에 대한 서브넷을 만듭니다.

    기존 관리되는 도메인의 가상 네트워크와 대상 가상 네트워크 사이에 Azure 가상 네트워크 피어링이 아직 없는 경우 자동으로 만들어집니다.

    다음 예제 스크린샷에서는 *미국 동부*에 새 복제본 세트를 만드는 프로세스를 보여줍니다.

    ![Azure Portal에서 복제본 세트를 만드는 예제 스크린샷](./media/tutorial-create-replica-set/create-replica-set.png)

1. 준비되면 **저장**을 선택합니다.

리소스가 대상 지역에 생성되므로 복제본 세트를 만드는 프로세스가 완료될 때까지 약간의 시간이 걸립니다. 그 후 AD DS 복제를 사용하여 관리되는 도메인 자체가 복제됩니다.

다음 예제 스크린샷처럼 배포가 계속 진행되고 복제본 세트는 *프로비저닝 중* 상태를 보고합니다. 완료되면 복제본 세트는 *실행 중*으로 표시됩니다.

![Azure Portal의 복제본 세트 배포 상태를 보여주는 예제 스크린샷](./media/tutorial-create-replica-set/replica-set-provisioning.png)

## <a name="delete-a-replica-set"></a>복제본 세트 삭제

관리되는 도메인은 현재 초기 복제본 세트와 추가 복제본 세트 3개, 총 4개의 복제본으로 제한됩니다. 복제본 세트가 더 이상 필요 없거나 다른 지역에 복제본 세트를 만들려는 경우 필요 없는 복제본 세트를 삭제할 수 있습니다.

> [!IMPORTANT]
> 관리되는 도메인의 마지막 복제본 세트는 삭제할 수 없습니다.

복제본 세트를 삭제하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **Azure AD Domain Services**를 검색하여 선택합니다.
1. 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. 왼쪽에서 **복제본 세트(미리 보기)** 를 선택합니다. 복제본 세트 목록에서 삭제하려는 복제본 세트 옆에 있는 **...** 바로 가기 메뉴를 선택합니다.
1. 바로 가기 메뉴에서 **삭제**를 선택한 다음, 복제본 세트 삭제를 확인합니다.

> [!NOTE]
> 복제본 세트를 삭제하는 작업은 시간이 오래 걸릴 수 있습니다.

복제본 세트에서 사용하는 가상 네트워크 또는 피어링이 더 이상 필요 없는 경우 해당 리소스를 삭제할 수도 있습니다. 다른 지역의 다른 애플리케이션 리소스에 네트워크 연결이 필요한지 확인한 후 삭제해야 합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 가상 네트워크 피어링 구성
> * 다른 지리적 지역에 복제본 세트 만들기
> * 복제본 세트 삭제

개념에 대해 자세히 알아보려면 Azure AD DS에서 복제본 세트가 작동하는 방식을 알아보세요.

> [!div class="nextstepaction"]
> [복제본 세트 개념 및 기능][concepts-replica-sets]

<!-- INTERNAL LINKS -->
[replica-sets]: concepts-replica-sets.md
[tutorial-create-instance]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[howto-change-sku]: change-sku.md
[concepts-replica-sets]: concepts-replica-sets.md
