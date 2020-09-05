---
title: 'Azure Resource Manager: 단일 데이터베이스 만들기'
description: Azure Resource Manager 템플릿을 사용하여 Azure SQL Database에서 단일 데이터베이스를 만듭니다.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: subject-armqs sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: quickstart
author: mumian
ms.author: jgao
ms.reviewer: carlrab
ms.date: 06/24/2020
ms.openlocfilehash: 2c1ae6bd69a87ad0ae941fba66785344d91e8268
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660840"
---
# <a name="quickstart-create-a-single-database-in-azure-sql-database-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure SQL Database에서 단일 데이터베이스 만들기

[단일 데이터베이스](single-database-overview.md)를 만드는 것이 Azure SQL Database에서 데이터베이스를 만드는 가장 빠르고 간단한 옵션입니다. 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 단일 데이터베이스를 만드는 방법을 보여줍니다.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-database%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>템플릿 검토

단일 데이터베이스는 두 [구매 모델](purchasing-models.md) 중 하나를 사용하여 컴퓨팅, 메모리, IO 및 스토리지 리소스 세트가 정의됩니다. 단일 데이터베이스를 만들 때 지정된 지역의 [Azure 리소스 그룹](../../active-directory-b2c/overview.md) 내에서 데이터베이스를 관리 및 배치하기 위한 [서버](logical-servers.md)도 정의합니다.

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-sql-database/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-sql-database/azuredeploy.json":::

이러한 리소스는 템플릿에 정의되어 있습니다.

- [**Microsoft.Sql/servers**](/azure/templates/microsoft.sql/servers)
- [**Microsoft.Sql/servers/databases**](/azure/templates/microsoft.sql/servers/databases)

추가 Azure SQL Database 템플릿 샘플은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sql&pageNumber=1&sort=Popular)에서 찾을 수 있습니다.

## <a name="deploy-the-template"></a>템플릿 배포

다음 PowerShell 코드 블록에서 **사용해 보세요**를 선택하여 Azure Cloud Shell을 엽니다.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter an Azure location (i.e. centralus)"
$adminUser = Read-Host -Prompt "Enter the SQL server administrator username"
$adminPassword = Read-Host -Prompt "Enter the SQl server administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-sql-database/azuredeploy.json" -administratorLogin $adminUser -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

## <a name="validate-the-deployment"></a>배포 유효성 검사

데이터베이스를 쿼리하려면 [데이터베이스 쿼리](single-database-create-quickstart.md#query-the-database)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

[다음 단계](#next-steps)로 이동하려면 이 리소스 그룹, 서버 및 단일 데이터베이스를 그대로 유지합니다. 다음 단계에서는 다른 메서드를 사용하여 데이터베이스를 연결하고 쿼리하는 방법을 보여줍니다.

리소스 그룹을 삭제하려면 다음을 수행합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>다음 단계

- 온-프레미스 또는 원격 도구에서 단일 데이터베이스에 연결하기 위한 서버 수준 방화벽 규칙을 만듭니다. 자세한 내용은 [서버 수준 방화벽 규칙 만들기](firewall-create-server-level-portal-quickstart.md)를 참조하세요.
- 서버 수준 방화벽 규칙을 만든 후 여러 다양한 도구 및 언어를 사용하여 데이터베이스를 [연결 및 쿼리](connect-query-content-reference-guide.md)할 수 있습니다.
  - [SQL Server Management Studio를 사용하여 연결 및 쿼리](connect-query-ssms.md)
  - [Azure Data Studio를 사용하여 연결 및 쿼리](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Azure CLI를 사용하여 단일 데이터베이스를 만들려면 [Azure CLI 샘플](az-cli-script-samples-content-guide.md)을 참조하세요.
- Azure PowerShell을 사용하여 단일 데이터베이스를 만들려면 [Azure PowerShell 샘플](powershell-script-content-guide.md)을 참조하세요.
- ARM 템플릿을 만드는 방법을 알아보려면 [첫 번째 템플릿 만들기](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)를 참조하세요.
