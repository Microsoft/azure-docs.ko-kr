---
title: Azure AD Domain Services에서 클래식 배포 마이그레이션의 이점 | Microsoft Docs
description: Azure Active Directory Domain Services의 클래식 배포를 리소스 관리자 배포 모델로 마이그레이션하는 이점에 대 한 자세한 정보
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: iainfou
ms.openlocfilehash: 93dcc1202c08be905cf08513f38e79a8a7674e01
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650131"
---
# <a name="benefits-of-migration-from-the-classic-to-resource-manager-deployment-model-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services의 클래식에서 리소스 관리자 배포 모델로 마이그레이션하는 이점

Azure Active Directory Domain Services (Azure AD DS)를 사용 하면 클래식 배포 모델을 사용 하는 기존 관리 되는 도메인을 리소스 관리자 배포 모델로 마이그레이션할 수 있습니다. 리소스 관리자 배포 모델을 사용 하는 Azure AD DS 관리 되는 도메인은 세분화 된 암호 정책, 감사 로그 및 계정 잠금 보호와 같은 추가 기능을 제공 합니다.

이 문서에서는 마이그레이션의 이점을 간략하게 설명 합니다. 시작 하려면 [클래식 가상 네트워크 모델에서 Azure AD Domain Services 리소스 관리자로 마이그레이션][howto-migrate]을 참조 하세요.

> [!NOTE]
> 2017에서는 Azure Resource Manager 네트워크의 호스트에서 Azure AD Domain Services를 사용할 수 있었습니다. 그 이후에는 Azure Resource Manager의 최신 기능을 사용 하 여 보다 안전한 서비스를 구축할 수 있었습니다. Azure Resource Manager 배포가 클래식 배포를 완전히 대체 하기 때문에 Azure AD DS 클래식 가상 네트워크 배포는 2023 년 3 월 1 일에 사용 중지 됩니다.
>
> 자세한 내용은 공식 사용 중단 [알림](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/) 을 참조 하세요.

## <a name="migration-benefits"></a>마이그레이션 이점

마이그레이션 프로세스는 클래식 배포 모델을 사용 하 고 리소스 관리자 배포 모델을 사용 하도록 이동 하는 기존 관리 되는 도메인을 사용 합니다. 클래식에서 리소스 관리자 배포 모델로 관리 되는 도메인을 마이그레이션하는 경우 컴퓨터를 관리 되는 도메인에 다시 가입 하거나 관리 되는 도메인을 삭제 하 고 처음부터 새로 만들 필요가 없습니다. 마이그레이션 프로세스가 끝날 때 Vm이 관리 되는 도메인에 계속 가입 됩니다.

마이그레이션 후 Azure AD DS는 다음과 같은 리소스 관리자 배포 모델을 사용 하는 도메인에만 사용할 수 있는 많은 기능을 제공 합니다.

* [세분화 암호 정책 지원][password-policy].
* Azure AD와 Azure AD Domain Services 간의 동기화 속도가 빨라집니다.
* Azure AD 관리자와 employeeID [에서 동기화 되는][attributes]두 개의 새 특성  -  *manager* 입니다. *employeeID*
* [SKU를 업그레이드할][skus]때 더 높은 수준의 도메인 컨트롤러에 액세스 합니다.
* AD 계정 잠금 보호.
* [관리 되는 도메인에 대 한 경고에 대 한 전자 메일 알림][email-alerts]
* [Azure 통합 문서 및 azure monitor를 사용 하 여 감사 로그 및 로그인 활동을 볼 수][workbooks]있습니다.
* 지원 되는 지역에서 [Azure 가용성 영역][availability-zones]합니다.
* [Azure Files][azure-files], [HD Insights][hd-insights]및 [Windows 가상 데스크톱과][wvd]같은 다른 Azure 제품과 통합.
* 지원은 더 많은 원격 분석에 액세스할 수 있으며 더 효과적으로 문제를 해결 하는 데 도움이 됩니다.
* 관리 되는 도메인 컨트롤러의 데이터에 대해 [Azure Managed Disks][managed-disks] 를 사용 하 여 미사용 암호화를 사용 합니다.

리소스 관리자 배포 모델을 사용 하는 관리 되는 도메인은 최신 새로운 기능을 최신 상태로 유지 하는 데 도움이 됩니다. 클래식 배포 모델을 사용 하는 관리 되는 도메인에는 새로운 기능을 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

시작 하려면 [클래식 가상 네트워크 모델에서 Azure AD Domain Services 리소스 관리자로 마이그레이션][howto-migrate]을 참조 하세요.

<!-- LINKS - INTERNAL -->
[password-policy]: password-policy.md
[skus]: change-sku.md
[email-alerts]: notifications.md
[workbooks]: use-azure-monitor-workbooks.md
[azure-files]: ../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md
[hd-insights]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[wvd]: ../virtual-desktop/overview.md
[availability-zones]: ../availability-zones/az-overview.md
[howto-migrate]: migrate-from-classic-vnet.md
[attributes]: synchronization.md#attribute-synchronization-and-mapping-to-azure-ad-ds
[managed-disks]: ../virtual-machines/managed-disks-overview.md
