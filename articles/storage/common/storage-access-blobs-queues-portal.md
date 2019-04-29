---
title: Azure portal을 사용 하 여 blob에 액세스 하거나 Azure Storage 데이터 큐
description: Blob에 액세스 또는 Azure portal을 사용 하 여 큐 데이터를 포털에서는 요청을 Azure Storage 내부적 경우. Azure Storage에 대 한 이러한 요청 인증할 수 및 Azure AD 계정 또는 저장소 계정 액세스 키 중 하나를 사용 하 여 인증 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/19/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5eba650ac2a052f264d82260e9fc07bf195235da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483873"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Blob 또는 큐 데이터에 액세스 하려면 Azure portal을 사용 하 여

Blob 또는 큐 데이터를 사용 하 여 액세스할 때 합니다 [Azure portal](https://portal.azure.com), 포털에서는 요청을 Azure Storage 내부적입니다. Azure Storage에 대 한 이러한 요청 인증할 수 및 Azure AD 계정 또는 저장소 계정 액세스 키 중 하나를 사용 하 여 인증 합니다. 포털을 사용 하는 하 고 적절 한 권한이 있는 경우 둘 간에 전환할 수 있도록 인증 방법을 나타냅니다.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Blob 또는 큐 데이터에 액세스 하는 데 필요한 사용 권한

Azure portal에서 blob 또는 큐 데이터 액세스를 인증 하려는 방법에 따라 특정 사용 권한이 필요 합니다. 대부분의 경우에서 이러한 권한은 역할 기반 액세스 제어 (RBAC)를 통해 제공 됩니다. RBAC에 대 한 자세한 내용은 참조 하세요. [역할 기반 액세스 제어 (RBAC) 란?](../../role-based-access-control/overview.md)합니다.

### <a name="account-access-key"></a>계정 액세스 키

계정 액세스 키를 사용 하 여 blob 및 큐 데이터에 액세스 하려면 사용자에 게 할당 RBAC 작업을 포함 하는 RBAC 역할이 있어야 **Microsoft.Storage/storageAccounts/listkeys/action**합니다. 이 RBAC 역할은 기본 제공 또는 사용자 지정 역할을 수 있습니다. 지 원하는 기본 제공 역할 **Microsoft.Storage/storageAccounts/listkeys/action** 포함:

- Azure Resource Manager [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할
- Azure Resource Manager [참가자](../../role-based-access-control/built-in-roles.md#contributor) 역할
- 합니다 [Storage 계정 참여자](../../role-based-access-control/built-in-roles.md#storage-account-contributor) 역할

Azure portal에서 blob 또는 큐 데이터에 액세스 하려고 할 때 포털 먼저 확인 여부를 할당 된 역할을 사용 하 여 **Microsoft.Storage/storageAccounts/listkeys/action**합니다. 이 작업을 사용 하 여 역할 할당 되었습니다. 포털 blob 및 큐 데이터에 액세스 하기 위한 계정 키를 사용 합니다. 이 작업을 사용 하 여 역할 할당 되었습니다 없습니다, 포털 Azure AD 계정을 사용 하 여 데이터에 액세스 하려고 합니다.

> [!NOTE]
> 클래식 구독 관리자 역할 서비스 관리자 및 공동 관리자 포함 Azure Resource Manager의 해당 [소유자](../../role-based-access-control/built-in-roles.md#owner) 역할입니다. **소유자** 역할을 포함 한 모든 작업을 포함 합니다 **Microsoft.Storage/storageAccounts/listkeys/action**이므로 이러한 관리 역할 중 하나를 사용 하 여 사용자와 blob 및 큐 데이터에 액세스할 수도 있습니다는 계정 키입니다. 자세한 내용은 [클래식 구독 관리자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)입니다.

### <a name="azure-ad-account"></a>Azure AD 계정

Azure AD 계정을 사용 하 여 Azure portal에서 blob 또는 큐 데이터에 액세스 하려면 다음 문 중 둘 다에 맞아야 수 있습니다:

- 할당 된 Azure Resource Manager [판독기](../../role-based-access-control/built-in-roles.md#reader) 이상 저장소 계정 수준으로 범위가 지정 된 최소한의 역할을 합니다. 합니다 **판독기** 역할은 가장 제한 된 권한을 부여 하지만 저장소 계정 관리 리소스에 대 한 액세스 권한을 부여 하는 다른 Azure 리소스 관리자 역할에도 적용 됩니다.
- 하거나 blob 또는 큐 데이터에 대 한 액세스를 제공 하는 기본 제공 또는 사용자 지정 역할 할당 되었습니다.

합니다 **판독기** 역할 할당 또는 다른 Azure 리소스 관리자 역할 할당은 필요한 사용자 보기를 업데이트 하 고 Azure portal에서 저장소 계정 관리 리소스를 탐색할 수 있도록 합니다. Blob 또는 큐 데이터에 대 한 액세스 권한을 부여 하는 RBAC 역할은 저장소 계정 관리 리소스에 대 한 액세스를 부여 하지 마세요. 포털에서 데이터를 blob 또는 큐에 액세스 하려면 사용자에는 저장소 계정 리소스를 탐색 하는 권한이 필요 합니다. 이 요구 사항에 대 한 자세한 내용은 참조 하세요. [포털 액세스에 대 한 읽기 권한자 역할을 할당](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)합니다.

Blob 또는 큐 데이터에 대 한 액세스를 지 원하는 기본 제공 역할은 다음과 같습니다.

- [저장소 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): Azure Data Lake 저장소 Gen2에 대 한 POSIX 액세스 제어입니다.
- [Storage Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Blob에 대 한 읽기/쓰기/삭제 권한입니다.
- [Storage Blob 데이터 판독기](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): Blob에 대 한 읽기 / 쓰기 권한입니다.
- [Storage 큐 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): 큐에 대 한 읽기/쓰기/삭제 권한입니다.
- [Storage 큐 데이터 판독기](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): 큐에 대 한 읽기 / 쓰기 권한입니다.
    
사용자 지정 역할에는 기본 제공 역할에 의해 제공 되 권한인의 다양 한 조합을 지원할 수 있습니다. 사용자 지정 RBAC 역할을 만드는 방법에 대 한 자세한 내용은 참조 하세요. [Azure 리소스에 대 한 사용자 지정 역할](../../role-based-access-control/custom-roles.md) 하 고 [Azure 리소스에 대 한 역할 정의 이해할](../../role-based-access-control/role-definitions.md)합니다.

> [!NOTE]
> 클래식 구독 관리자 역할을 사용 하 여 큐를 나열 하는 것은 지원 되지 않습니다. 큐를 나열 하려면, 사용자를 할당 해야 합니다에 Azure Resource Manager **판독기** 역할을 합니다 **Storage 큐 데이터 판독기** 역할 또는 **Storage 큐 데이터 기여자** 역할입니다.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Blob 또는 포털에서 큐로 이동

포털에서 blob 또는 큐 데이터를 보려는 이동 합니다 **개요** 저장소 계정에 대 한 링크를 클릭 **Blob** 또는 **큐**합니다. 또는 이동할 수 있습니다 합니다 **Blob service** 하 고 **큐 서비스** 메뉴의 섹션입니다. 

![Azure portal에서 데이터를 blob 또는 큐로 이동](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>현재 인증 방법을 결정 합니다.

컨테이너 또는 큐로 이동 하면 Azure portal 여부를 현재 사용 중인 계정 액세스 키 또는 Azure AD 계정 인증을 나타냅니다.

이 섹션의 예제에서는 컨테이너 및 해당 blob에 액세스할 보여주지만 큐 및 해당 메시지에 액세스 하거나 큐를 나열 하는 경우 포털에서 동일한 메시지를 표시 합니다.

### <a name="account-access-key"></a>계정 액세스 키

계정 액세스 키를 사용 하 여를 인증 하는 경우 표시 됩니다 **선택키가** 포털에서 인증 방법으로 지정 합니다.

![현재 컨테이너 데이터가 계정 키를 사용 하 여 액세스](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Azure AD 계정을 사용 하 여를 전환 하려면 이미지에서 강조 표시 링크를 클릭 합니다. 사용자에 게 RBAC 역할 할당을 통해 적절 한 권한이 계속 수 있습니다. 그러나 적절 한 권한이, 부족 한 경우 다음과 같은 오류 메시지가 나타납니다.

![Azure AD 계정 액세스를 지원 하지 않는 경우 표시 된 오류](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Azure AD 계정을 표시 하는 권한이 없는 경우 blob 목록에 나타나는지 확인 합니다. 클릭 합니다 **액세스 키로 전환** 액세스 키를 사용 하 여 다시 인증 하는 링크입니다.

### <a name="azure-ad-account"></a>Azure AD 계정

Azure AD 계정을 사용 하 여를 인증 하는 경우 표시 됩니다 **Azure AD 사용자 계정** 포털에서 인증 방법으로 지정 합니다.

![현재 Azure AD 계정으로 컨테이너 데이터에 액세스](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

계정 액세스 키를 사용 하 여를 전환 하려면 이미지에서 강조 표시 링크를 클릭 합니다. 계정 키에 대 한 액세스를 사용 하는 경우 계속할 수 있습니다. 그러나 계정 키에 대 한 액세스, 부족 한 경우 다음과 같은 오류 메시지가 나타납니다.

![계정 키에 액세스할 수 없는 경우 표시 된 오류](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

계정 키에 액세스할 수 없는 경우 blob 목록에 나타나는지 확인 합니다. 클릭 합니다 **Azure AD 사용자 계정으로 전환** 다시 인증에 대 한 Azure AD 계정을 사용 하는 링크입니다.

## <a name="next-steps"></a>다음 단계

- [Azure blob 및 Azure Active Directory를 사용 하 여 큐에 대 한 액세스를 인증 합니다.](storage-auth-aad.md)
- [Azure 컨테이너 및 Azure portal에서 RBAC 사용 하 여 큐 액세스 권한 부여](storage-auth-aad-rbac-portal.md)
- [Azure CLI를 사용 하 여 RBAC 사용 하 여 Azure blob 및 큐 데이터에 액세스 권한 부여](storage-auth-aad-rbac-cli.md)
- [PowerShell을 사용 하 여 RBAC 사용 하 여 Azure blob 및 큐 데이터에 액세스 권한 부여](storage-auth-aad-rbac-powershell.md)
