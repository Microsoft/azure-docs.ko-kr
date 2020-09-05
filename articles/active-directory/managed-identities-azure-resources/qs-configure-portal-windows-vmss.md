---
title: Azure VMSS에서 관리 ID 구성 - Azure AD
description: Azure Portal을 사용하여 가상 머신 확장 집합에서 Azure 리소스에 대한 관리 ID를 구성하는 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9ab1adeac5f8c67e06682f49fdded88e6a350e7
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89262676"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-the-azure-portal"></a>Azure Portal을 사용하여 가상 머신 확장 집합에서 Azure 리소스에 대한 관리 ID 구성

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 리소스에 대한 관리 ID는 Azure Active Directory에서 자동으로 관리 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 포함할 필요가 없습니다. 

이 문서에서는 PowerShell을 사용하여 가상 머신 확장 집합에서 Azure 리소스에 대해 다음과 같은 관리 ID 작업을 수행하는 방법을 알아봅니다.

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 이 문서의 관리 작업을 수행하려면 계정에 다음과 같은 Azure 역할이 할당되어야 합니다.

    > [!NOTE]
    > 추가 Azure AD 디렉터리 역할 할당이 필요하지 않습니다.

    - [가상 머신 참가자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor): 가상 머신 확장 집합에서 시스템 할당 관리 ID를 사용하도록 설정하고 제거합니다.

## <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

이 섹션에서는 Azure Portal을 사용하여 시스템 할당 관리 ID를 사용 및 사용하지 않도록 설정하는 방법을 알아봅니다.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>가상 머신 확장 집합을 만드는 동안 시스템 할당 관리 ID를 사용하도록 설정

현재 Azure Portal은 가상 머신 확장 집합을 만드는 동안 시스템 할당 관리 ID를 사용하도록 설정하는 것을 지원하지 않습니다. 대신 다음 가상 머신 확장 집합 만들기 빠른 시작 문서를 참조하여 먼저 가상 머신 확장 집합을 만든 다음, 가상 머신 확장 집합에서 시스템 할당 관리 ID를 사용하도록 설정하는 데 관한 다음 섹션을 진행합니다.

- [Azure Portal에서 가상 머신 확장 집합 만들기](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>기존 가상 머신 확장 집합에서 시스템 할당 관리 ID를 사용하도록 설정

원래 시스템 할당 ID 없이 프로비전된 가상 머신 확장 집합에서 시스템 할당 관리 ID를 사용하도록 설정하려면:

1. 가상 머신 확장 집합을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 원하는 가상 머신 확장 집합으로 이동합니다.

3. **시스템 할당**, **상태**에서 **켜기**를 선택한 다음, **저장**을 클릭합니다.

   ![구성 페이지 스크린샷](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 시스템 할당 관리 ID 제거

시스템 할당 관리 ID가 더 이상 필요하지 않은 가상 머신 확장 집합이 있는 경우 다음을 수행합니다.

1. 가상 머신 확장 집합을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다. 또한 계정이 가상 머신 확장 집합에 대한 쓰기 권한을 부여하는 역할에 속하는지 확인합니다.

2. 원하는 가상 머신 확장 집합으로 이동합니다.

3. **시스템 할당**, **상태**에서 **끄기**를 선택한 다음, **저장**을 클릭합니다.

   ![구성 페이지 스크린샷](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

이 섹션에서는 Azure Portal을 사용하여 가상 머신 확장 집합에서 사용자 할당 관리 ID를 추가하고 제거하는 방법을 알아봅니다.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>가상 머신 확장 집합을 만드는 동안 사용자 할당 관리 ID를 할당합니다.

현재 Azure Portal은 가상 머신 확장 집합을 만드는 동안 사용자 할당 관리 ID를 할당하는 것을 지원하지 않습니다. 대신 다음 가상 머신 확장 집합 만들기 빠른 시작 문서를 참조하여 먼저 가상 머신 확장 집합을 만든 다음, 가상 머신 확장 집합에 사용자 할당 관리 ID를 할당하는 데 관한 다음 섹션을 진행합니다.

- [Azure Portal에서 가상 머신 확장 집합 만들기](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>기존 가상 머신 확장 집합에 사용자 할당 관리 ID 할당

1. 가상 머신 확장 집합을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 원하는 가상 머신 확장 집합으로 이동하고 **ID**, **사용자 할당**을 클릭한 다음, **\+추가**를 클릭합니다.

   ![VMSS에 사용자 할당 ID 추가](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. 가상 머신 확장 집합에 추가할 사용자 할당 ID를 클릭한 다음, **추가**를 클릭합니다.
   
   ![VMSS에 사용자 할당 ID 추가](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>가상 머신 확장 집합에서 사용자가 할당한 관리 ID 제거

1. VM을 포함하는 Azure 구독과 연결된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 원하는 가상 머신 확장 집합으로 이동하고 **ID**, **사용자 할당**, 삭제할 사용자 할당 관리 ID의 이름을 클릭한 다음, **제거**를 클릭합니다(확인 창에서 **예** 클릭).

   ![VMSS에서 사용자 할당 ID 제거](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>다음 단계

- Azure Portal을 사용하여 Azure 가상 머신 확장 집합 관리 ID [액세스 권한을 다른 Azure 리소스에](howto-assign-access-portal.md) 부여합니다.