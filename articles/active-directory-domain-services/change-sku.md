---
title: Azure AD Domain Services에 대한 SKU 변경 | Microsoft Docs
description: 비즈니스 요구 사항이 변경 되는 경우 Azure AD Domain Services 관리 되는 도메인의 SKU 계층에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 46557d802222190c0ed82f6243dd2a9b997ecaa5
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960569"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>기존 Azure AD Domain Services 관리 되는 도메인에 대한 SKU 변경

Azure Active Directory Domain Services (Azure AD DS)에서 사용할 수 있는 성능 및 기능은 SKU 유형을 기반으로 합니다. 이러한 기능 차이에는 백업 빈도 또는 단방향 아웃 바운드 포리스트 트러스트의 최대 수 (현재 미리 보기 상태)가 포함 됩니다. 관리 되는 도메인을 만들 때 SKU를 선택 하 고 관리 되는 도메인이 배포 된 후 비즈니스 요구 사항이 변경 되 면 Sku를 전환할 수 있습니다. 비즈니스 요구 사항에 대한 변경에는 더 자주 백업 해야 하는 필요성 또는 추가 포리스트 트러스트를 만들 수 있습니다. 다른 Sku의 제한 및 가격 책정에 대한 자세한 내용은 [azure AD DS SKU 개념][concepts-sku] 및 [azure AD DS 가격 책정][pricing] 페이지를 참조 하세요.

이 문서에서는 Azure Portal를 사용 하 여 기존 Azure AD DS 관리 되는 도메인에 대한 SKU를 변경 하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료 하려면 다음 리소스와 권한이 필요 합니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 자습서를 완료 하 여 [Azure Active Directory Domain Services 인스턴스를 만들고 구성][create-azure-ad-ds-instance]합니다.

## <a name="sku-change-limitations"></a>SKU 변경 제한 사항

리소스 포리스트 (현재 미리 보기 상태)를 사용 하 고 Azure AD DS에서 온-프레미스 AD DS 환경으로 단방향 아웃 바운드 포리스트 트러스트를 만든 경우 SKU 변경 작업에 대한 몇 가지 제한 사항이 있습니다. *Premium* 및 *Enterprise* sku는 만들 수 있는 트러스트의 수에 대한 제한을 정의 합니다. 현재 구성 된 것 보다 낮은 최대 제한이 있는 SKU로 변경할 수 없습니다.

예를 들어 *Premium* SKU에서 두 개의 포리스트 트러스트를 만든 경우 *표준* SKU로 변경할 수 없습니다. *표준* SKU는 포리스트 트러스트를 지원 하지 않습니다. 또는 *프리미엄* SKU에서 7 개의 트러스트를 만든 경우 *Enterprise* SKU로 변경할 수 없습니다. *Enterprise* SKU는 최대 5 개의 트러스트를 지원 합니다.

이러한 제한에 대한 자세한 내용은 [Azure AD DS SKU 기능 및 제한][concepts-sku]을 참조 하세요.

## <a name="select-a-new-sku"></a>새 SKU 선택

Azure Portal를 사용 하 여 Azure AD DS 관리 되는 도메인에 대한 SKU를 변경 하려면 다음 단계를 완료 합니다.

1. Azure Portal 맨 위에서 **Azure AD Domain Services**를 검색 하 고 선택 합니다. 목록에서 관리 되는 도메인 (예: *aadds.contoso.com*)을 선택 합니다.
1. Azure AD DS 페이지의 왼쪽에 있는 메뉴에서 **설정 > SKU**를 선택 합니다.

    ![Azure Portal에서 Azure AD DS 관리 되는 도메인에 대한 SKU 메뉴 옵션을 선택 합니다.](media/change-sku/overview-change-sku.png)

1. 드롭다운 메뉴에서 Azure AD DS 관리 되는 도메인에 대해 원하는 SKU를 선택 합니다. 리소스 포리스트가 있는 경우 *엔터프라이즈* sku 이상 에서만 포리스트 트러스트를 사용할 수 있으므로 *표준* SKU를 선택할 수 없습니다.

    드롭다운 메뉴에서 원하는 SKU를 선택 하 고 **저장**을 선택 합니다.

    ![Azure Portal의 드롭다운 메뉴에서 필요한 SKU를 선택 합니다.](media/change-sku/change-sku-selection.png)

SKU 유형을 변경 하는 데 1 ~ 2 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

리소스 포리스트가 있고 SKU 변경 후 추가 트러스트를 만들려는 경우 [Azure AD DS (미리 보기)에서 온-프레미스 도메인에 아웃 바운드 포리스트 트러스트 만들기][create-trust]를 참조 하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
