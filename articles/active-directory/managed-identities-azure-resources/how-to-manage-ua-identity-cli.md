---
title: 사용자 할당 관리 id 관리-Azure CLI-Azure AD
description: Azure CLI를 사용 하 여 사용자 할당 관리 id를 만들고, 나열 하 고, 삭제 하는 방법에 대 한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5c3af6c46dd8ad69915e8f870d739f33375dba5e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266411"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Azure CLI를 사용하여 사용자 할당 관리 ID 생성, 나열 또는 삭제


Azure 리소스에 대 한 관리 id는 Azure Active Directory에서 관리 id를 사용 하 여 Azure 서비스를 제공 합니다. 이 ID를 사용하면 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다. 

이 문서에서는 Azure CLI를 사용 하 여 사용자 할당 관리 id를 만들고, 나열 하 고, 삭제 하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#managed-identity-types)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- CLI 스크립트 예제는 다음의 세 가지 옵션 중 하나로 실행할 수 있습니다.
    - Azure Portal에서 [Azure Cloud Shell](../../cloud-shell/overview.md)을 사용합니다(다음 섹션 참조).
    - 각 코드 블록의 오른쪽 위 모퉁이에 있는 "사용해 보세요." 단추를 통해 포함 된 Azure Cloud Shell를 사용 합니다.
    - 로컬 CLI 콘솔을 사용하려는 경우 [Azure CLI의 최신 버전을 설치](/cli/azure/install-azure-cli)합니다(2.0.13 이상). 사용자 할당 관리 ID를 배포하려는 Azure 구독과 연결된 계정으로 `az login`을 사용하여 Azure에 로그인합니다.


> [!NOTE]
> CLI를 사용 하 여 앱 servivce 보안 주체를 사용 하는 경우 사용자 권한을 수정 하려면 CLI의 일부에서 Graph API에 대해 GET 요청을 수행 하는 서비스 사용자에 게 Azure Graph API AD의 추가 권한을 제공 해야 합니다. 그렇지 않으면 ' 작업을 완료할 수 있는 권한이 없습니다. ' 메시지가 표시 될 수 있습니다. 이렇게 하려면 Azure Active Directory에서 앱 등록으로 이동 하 고, 앱을 선택 하 고, API 권한을 클릭 하 고, 아래로 스크롤하여 Azure Active Directory Graph를 선택 해야 합니다. 여기에서 응용 프로그램 사용 권한을 선택 하 고 적절 한 사용 권한을 추가 합니다. 



[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기 

사용자 할당 관리 ID를 만들려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 만들려면 [az identity create](/cli/azure/identity#az-identity-create) 명령을 사용합니다. `-g` 매개 변수는 사용자 할당 관리 ID가 만들어질 리소스 그룹을 지정하고 `-n` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<USER ASSIGNED IDENTITY NAME>` 매개 변수 값을 원하는 값으로 바꿉니다.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>사용자 할당 관리 ID 나열

사용자 할당 관리 ID를 나열하려면/읽으려면 계정에 [관리 ID 운영자](../../role-based-access-control/built-in-roles.md#managed-identity-operator) 또는 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 나열하려면 [az identity list](/cli/azure/identity#az-identity-list) 명령을 사용합니다. `<RESOURCE GROUP>`을 원하는 값으로 바꿉니다.

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
JSON 응답에서 사용자 할당 관리 ID에는 키 `type`에 대해 반환된 `"Microsoft.ManagedIdentity/userAssignedIdentities"` 값이 있습니다.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 삭제

사용자 할당 관리 ID를 삭제하려면 계정에 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.

사용자 할당 관리 ID를 삭제하려면 [az identity delete](/cli/azure/identity#az-identity-delete) 명령을 사용합니다.  -n 매개 변수는 그 이름을 지정하고 -g 매개 변수는 사용자 할당 관리 ID가 만들어진 리소스 그룹을 지정합니다. `<USER ASSIGNED IDENTITY NAME>` 및 `<RESOURCE GROUP>` 매개 변수 값을 원하는 값으로 바꿉니다.

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> 사용자 할당 관리 ID를 삭제해도 할당된 리소스에서 참조는 제거되지 않습니다. 이러한 항목을 VM/VMSS에서 제거하려면 `az vm/vmss identity remove` 명령을 사용합니다.

## <a name="next-steps"></a>다음 단계

Azure CLI identity 명령의 전체 목록을 보려면 [az identity](/cli/azure/identity)를 참조하세요.

Azure VM에 사용자 할당 관리 ID를 할당하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)을 참조하세요.


