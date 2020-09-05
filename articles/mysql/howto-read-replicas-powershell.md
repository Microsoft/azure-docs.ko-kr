---
title: 읽기 복제본 관리-Azure PowerShell-Azure Database for MySQL
description: PowerShell을 사용 하 여 Azure Database for MySQL에서 읽기 복제본을 설정 하 고 관리 하는 방법을 알아봅니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 8/24/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c85af0f4078010fa5b6a1d116b3bfda942c0490c
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816935"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-powershell"></a>PowerShell을 사용 하 여 Azure Database for MySQL에서 읽기 복제본을 만들고 관리 하는 방법

이 문서에서는 PowerShell을 사용 하 여 Azure Database for MySQL 서비스에서 읽기 복제본을 만들고 관리 하는 방법에 대해 알아봅니다. 읽기 복제본에 대한 자세한 내용은 [개요](concepts-read-replicas.md)를 참조하세요.

## <a name="azure-powershell"></a>Azure PowerShell

PowerShell을 사용 하 여 읽기 복제본을 만들고 관리할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 완료하려면 다음이 필요합니다.

- 로컬에 설치 되거나 브라우저에 [Azure Cloud Shell](https://shell.azure.com/) 된 [Az PowerShell 모듈](/powershell/azure/install-az-ps)
- [Azure Database for MySQL 서버](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.MySql PowerShell 모듈이 미리 보기에 있지만 `Install-Module -Name Az.MySql -AllowPrerelease` 명령을 사용하여 Az PowerShell 모듈과 별도로 설치해야 합니다.
> Az.MySql PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스에 포함되며 Azure Cloud Shell 내에서 기본적으로 사용할 수 있습니다.

PowerShell을 로컬로 사용 하도록 선택 하는 경우 [AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet을 사용 하 여 Azure 계정에 연결 합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> 읽기 복제본 기능은 범용 또는 메모리 최적화 가격 책정 계층의 Azure Database for MySQL 서버에서만 사용 가능합니다. 마스터 서버가 이러한 가격 책정 계층 중 하나에 포함되어 있는지 확인하세요.

### <a name="create-a-read-replica"></a>읽기 복제본 만들기

> [!IMPORTANT]
> 기존 복제본이 없는 마스터에 대한 복제본을 만들 때 마스터가 먼저 다시 시작되어 자체적으로 복제를 위해 준비합니다. 이를 고려하고 사용량이 적은 기간 동안 이러한 작업을 수행합니다.

다음 명령을 사용하여 읽기 복제본 서버를 만들 수 있습니다.

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

`New-AzMySqlReplica` 명령에는 다음과 같은 매개 변수가 필요합니다.

| 설정 | 예제 값 | Description  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  복제본 서버가 생성 되는 리소스 그룹입니다.  |
| Name | mydemoreplicaserver | 만들어지는 새 복제본 서버의 이름입니다. |

지역 간 읽기 복제본을 만들려면 **Location** 매개 변수를 사용 합니다. 다음 예에서는 **미국 서 부** 지역에 복제본을 만듭니다.

```azurepowershell-interactive
Get-AzMySqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

복제본을 만들 수 있는 지역에 대해 자세히 알아보려면 [읽기 복제본 개념 문서](concepts-read-replicas.md)를 참조하세요.

기본적으로 **Sku** 매개 변수를 지정 하지 않으면 읽기 복제본이 마스터와 동일한 서버 구성으로 만들어집니다.

> [!NOTE]
> 복제본이 마스터와 동일한 성능을 유지할 수 있도록 복제본 서버의 구성은 마스터의 구성 값 이상으로 유지하는 것이 좋습니다.

### <a name="list-replicas-for-a-master-server"></a>마스터 서버에 대한 복제본 나열

지정된 마스터 서버에 대한 모든 복제본을 보려면 다음 명령을 실행합니다.

```azurepowershell-interactive
Get-AzMySqlReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

`Get-AzMySqlReplica` 명령에는 다음과 같은 매개 변수가 필요합니다.

| 설정 | 예제 값 | Description  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  복제본 서버가 만들어지는 리소스 그룹입니다.  |
| ServerName | mydemoserver | 마스터 서버의 이름 또는 ID입니다. |

### <a name="delete-a-replica-server"></a>복제본 서버 삭제

읽기 복제본 서버 삭제는 cmdlet을 실행 하 여 수행할 수 있습니다 `Remove-AzMySqlServer` .

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-master-server"></a>마스터 서버 삭제

> [!IMPORTANT]
> 마스터 서버를 삭제하면 모든 복제본 서버에 대한 복제가 중지되며 마스터 서버 자체도 삭제됩니다. 그러면 복제본 서버는 읽기와 쓰기를 모두 지원하는 독립 실행형 서버로 설정됩니다.

마스터 서버를 삭제 하려면 cmdlet을 실행 하면 `Remove-AzMySqlServer` 됩니다.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [PowerShell을 사용 하 여 Azure Database for MySQL 서버 다시 시작](howto-restart-server-powershell.md)
