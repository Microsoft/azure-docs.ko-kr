---
title: 리소스를 구성 하는 관리 그룹 만들기-Azure 관리
description: Azure 관리 그룹을 만들어 포털, Azure PowerShell 및 Azure CLI를 통해 여러 리소스를 관리하는 방법을 알아봅니다.
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: d9bb2e82404c0188094298f40da3346ee132eec3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436526"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>리소스 조직 및 관리에 대한 관리 그룹 만들기

관리 그룹은 여러 구독에서 액세스, 정책 및 규정 준수를 관리하는 데 도움이 되는 컨테이너입니다. 이러한 컨테이너를 만들어 [Azure 정책](../policy/overview.md) 및 [Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)와 함께 사용할 수 있는 효과적이고 효율적인 계층을 구축합니다. 관리 그룹에 대한 자세한 내용은 [Azure 관리 그룹으로 리소스 구성](overview.md)을 참조하세요.

디렉터리에서 만드는 첫 번째 관리 그룹을 완료하려면 최대 15분이 소요될 수 있습니다. 디렉터리에 대해 Azure 내의 관리 그룹 서비스를 설정하기 위해 처음으로 실행되는 프로세스가 있습니다. 프로세스가 완료되면 알림이 수신됩니다. 자세한 내용은 [관리 그룹의 초기 설정](./overview.md#initial-setup-of-management-groups)을 참조 하세요. 

## <a name="create-a-management-group"></a>관리 그룹 만들기

테넌트의 모든 Azure AD 사용자는 관리 그룹 쓰기 권한이 해당 사용자에 게 할당 되지 않은 상태에서 관리 그룹을 만들 수 있습니다.  이 새 관리 그룹은 루트 관리 그룹의 자식이 되 고 작성자는 "소유자" 역할 할당을 받습니다. 관리 그룹 서비스는이 기능을 허용 하 여 루트 수준에서 역할 할당이 필요 하지 않도록 합니다. 루트 관리 그룹을 만들 때 사용자에 게 액세스할 수 있는 권한이 없습니다.  관리 그룹 사용을 시작 하기 위해 Azure AD 전역 관리자를 찾을 장애물을 방지 하기 위해 루트 수준에서 초기 관리 그룹을 만들 수 있습니다.      

포털, PowerShell 또는 Azure CLI를 사용하여 관리 그룹을 만들 수 있습니다. 현재 Resource Manager 템플릿을 사용하여 관리 그룹을 만들 수 없습니다.

### <a name="create-in-portal"></a>포털에서 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **모든 서비스** > **관리 + 거 버 넌 스**를 선택 합니다.

1. **Cost Management + 청구** 선택

1. Cost Management + 청구-관리 그룹 페이지에서 **관리 그룹** 을 선택 합니다.

1. **+ 관리 그룹 추가**를 선택 합니다.

   ![관리 그룹 사용을 위한 페이지](./media/main.png)

1. 관리 그룹 ID 필드를 채웁니다.

   - **관리 그룹 ID**는 이 관리 그룹에 명령을 전송하는 데 사용되는 디렉터리 고유 식별자입니다. 이 식별자는 Azure 시스템 전체에서 이 그룹을 식별하는 데 사용되므로 만든 후에 편집할 수 없습니다. Azure Active Directory ID 인 ID를 사용 하 여 [루트 관리 그룹이](overview.md#root-management-group-for-each-directory) 자동으로 만들어집니다. 다른 모든 관리 그룹의 경우 고유 ID를 할당 합니다.
   - 표시 이름 필드는 Azure Portal 내에 표시되는 이름을 포함합니다. 별도 표시 이름은 관리 그룹을 만들 때 사용되는 선택적 필드로, 언제든지 변경할 수 있습니다.  

   ![새 관리 그룹을 만들기 위한 옵션 창](./media/create_context_menu.png)  

1. **저장**을 선택합니다.

### <a name="create-in-powershell"></a>PowerShell에서 열기

PowerShell의 경우 [AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup) cmdlet을 사용 하 여 새 관리 그룹을 만듭니다.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName**은 생성되는 고유 식별자입니다. 이 ID는 다른 명령에서이 그룹을 참조 하는 데 사용 되며 나중에 변경할 수 없습니다.

관리 그룹이 Azure Portal 내에 다른 이름을 표시 하도록 하려면 **DisplayName** 매개 변수를 추가 합니다. 예를 들어 Contoso의 GroupName과 "Contoso Group"의 표시 이름을 사용 하 여 관리 그룹을 만들려면 다음 cmdlet을 사용 합니다.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

앞의 예제에서 새 관리 그룹은 루트 관리 그룹 아래에 생성 됩니다. 다른 관리 그룹을 부모로 지정 하려면 **ParentId** 매개 변수를 사용 합니다.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Azure CLI에서 만들기

Azure CLI의 경우 [az account management-group create](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) 명령을 사용 하 여 새 관리 그룹을 만듭니다.

```azurecli-interactive
az account management-group create --name Contoso
```

**이름은** 생성 되는 고유 식별자입니다. 이 ID는 다른 명령에서이 그룹을 참조 하는 데 사용 되며 나중에 변경할 수 없습니다.

관리 그룹이 Azure Portal 내에 다른 이름을 표시 하도록 하려면 **표시 이름** 매개 변수를 추가 합니다. 예를 들어 Contoso의 GroupName과 "Contoso Group"의 표시 이름을 사용 하 여 관리 그룹을 만들려면 다음 명령을 사용 합니다.

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

앞의 예제에서 새 관리 그룹은 루트 관리 그룹 아래에 생성 됩니다. 다른 관리 그룹을 부모로 지정 하려면 **parent** 매개 변수를 사용 하 고 부모 그룹의 이름을 제공 합니다.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>다음 단계

관리 그룹에 대해 자세히 알아보려면 다음 항목을 참조하세요.

- [관리 그룹을 만들어 Azure 리소스 구성](create.md)
- [관리 그룹을 변경, 삭제 또는 관리하는 방법](manage.md)
- [Azure PowerShell 리소스 모듈에서 관리 그룹 검토](/powershell/module/az.resources#resources)
- [REST API에서 관리 그룹 검토](/rest/api/resources/managementgroups)
- [Azure CLI에서 관리 그룹 검토](/cli/azure/account/management-group)
