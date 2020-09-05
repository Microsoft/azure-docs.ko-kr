---
title: Azure AD Domain Services에 대 한 범위 동기화 Microsoft Docs
description: Azure Portal를 사용 하 여 Azure AD에서 관리 되는 Azure Active Directory Domain Services 도메인으로 범위가 지정 된 동기화를 구성 하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/24/2020
ms.author: iainfou
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe373b9aa08d6999a9c57dd0008a207071e1dddd
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068901"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure AD에서 Azure Active Directory Domain Services로 범위 동기화 구성

인증 서비스를 제공 하기 위해 Azure Active Directory Domain Services (Azure AD DS)는 Azure AD에서 사용자 및 그룹을 동기화 합니다. 하이브리드 환경에서 온-프레미스 Active Directory Domain Services (AD DS) 환경의 사용자와 그룹은 먼저 Azure AD Connect를 사용 하 여 Azure AD에 동기화 한 후 Azure AD DS 관리 되는 도메인에 동기화 할 수 있습니다.

기본적으로 Azure AD 디렉터리의 모든 사용자 및 그룹은 관리 되는 도메인에 동기화 됩니다. 특정 요구 사항이 있는 경우에는 정의 된 사용자 집합만 동기화 하도록 선택할 수 있습니다.

이 문서에서는 범위 지정 동기화를 구성 하 고 Azure Portal를 사용 하 여 범위 지정 된 사용자 집합을 변경 하거나 해제 하는 방법을 보여 줍니다. [PowerShell을 사용 하 여 이러한 단계를 완료할][scoped-sync-powershell]수도 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 자습서를 완료 하 여 [관리 되는 Azure Active Directory Domain Services 도메인을 만들고 구성][tutorial-create-instance]합니다.
* Azure AD DS 동기화 범위를 변경 하려면 Azure AD 테 넌 트에서 *전역 관리자* 권한이 필요 합니다.

## <a name="scoped-synchronization-overview"></a>범위 동기화 개요

기본적으로 Azure AD 디렉터리의 모든 사용자 및 그룹은 관리 되는 도메인에 동기화 됩니다. 사용자가 관리 되는 도메인에만 액세스 해야 하는 경우에는 해당 사용자 계정만 동기화 할 수 있습니다. 이 범위 동기화는 그룹을 기반으로 합니다. 그룹 기반 범위 동기화를 구성 하는 경우 지정한 그룹에 속하는 사용자 계정만 관리 되는 도메인에 동기화 됩니다. 중첩 그룹이 동기화 되지 않고 선택한 특정 그룹만 동기화 됩니다.

관리 되는 도메인을 만들 때 또는 배포 된 후에 동기화 범위를 변경할 수 있습니다. 이제 기존 관리 되는 도메인의 동기화 범위를 다시 만들 필요 없이 변경할 수 있습니다.

동기화 프로세스에 대해 자세히 알아보려면 [Azure AD Domain Services의 동기화 이해][concepts-sync]를 참조 하세요.

> [!WARNING]
> 동기화 범위를 변경 하면 관리 되는 도메인에서 모든 데이터를 다시 동기화 합니다. 고려 사항은 다음과 같습니다.
>
>  * 관리되는 도메인의 동기화 범위를 변경하면 전체 다시 동기화가 수행됩니다.
>  * 관리 되는 도메인에서 더 이상 필요 하지 않은 개체는 삭제 됩니다. 새 개체는 관리되는 도메인에서 만들어집니다.

## <a name="enable-scoped-synchronization"></a>범위 동기화 사용

Azure Portal에서 범위 동기화를 사용 하도록 설정 하려면 다음 단계를 완료 합니다.

1. Azure Portal에서 **Azure AD Domain Services**를 검색하여 선택합니다. 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. 왼쪽의 메뉴에서 **동기화** 를 선택 합니다.
1. *동기화 유형에*대해 **범위**지정을 선택 합니다.
1. **그룹 선택**을 선택 하 고 추가할 그룹을 검색 하 여 선택 합니다.
1. 모든 변경이 수행 되 면 **동기화 범위 저장**을 선택 합니다.

동기화 범위를 변경 하면 관리 되는 도메인에서 모든 데이터를 다시 동기화 합니다. 관리 되는 도메인에서 더 이상 필요 하지 않은 개체가 삭제 되며 다시 동기화를 완료 하는 데 다소 시간이 걸릴 수 있습니다.

## <a name="modify-scoped-synchronization"></a>범위 동기화 수정

사용자가 관리 되는 도메인에 동기화 해야 하는 그룹 목록을 수정 하려면 다음 단계를 완료 합니다.

1. Azure Portal에서 **Azure AD Domain Services**를 검색하여 선택합니다. 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. 왼쪽의 메뉴에서 **동기화** 를 선택 합니다.
1. 그룹을 추가 하려면 위쪽에서 **+ 그룹을 선택** 하 고 추가할 그룹을 선택 합니다.
1. 동기화 범위에서 그룹을 제거 하려면 현재 동기화 된 그룹 목록에서 해당 그룹을 선택 하 고 **그룹 제거**를 선택 합니다.
1. 모든 변경이 수행 되 면 **동기화 범위 저장**을 선택 합니다.

동기화 범위를 변경 하면 관리 되는 도메인에서 모든 데이터를 다시 동기화 합니다. 관리 되는 도메인에서 더 이상 필요 하지 않은 개체가 삭제 되며 다시 동기화를 완료 하는 데 다소 시간이 걸릴 수 있습니다.

## <a name="disable-scoped-synchronization"></a>범위 동기화 사용 안 함

관리 되는 도메인에 대 한 그룹 기반 범위 동기화를 사용 하지 않도록 설정 하려면 다음 단계를 완료 합니다.

1. Azure Portal에서 **Azure AD Domain Services**를 검색하여 선택합니다. 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. 왼쪽의 메뉴에서 **동기화** 를 선택 합니다.
1. *동기화 유형을* **범위** 에서 **모두**로 변경한 다음, **동기화 범위 저장**을 선택 합니다.

동기화 범위를 변경 하면 관리 되는 도메인에서 모든 데이터를 다시 동기화 합니다. 관리 되는 도메인에서 더 이상 필요 하지 않은 개체가 삭제 되며 다시 동기화를 완료 하는 데 다소 시간이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

동기화 프로세스에 대해 자세히 알아보려면 [Azure AD Domain Services의 동기화 이해][concepts-sync]를 참조 하세요.

<!-- INTERNAL LINKS -->
[scoped-sync-powershell]: powershell-scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
