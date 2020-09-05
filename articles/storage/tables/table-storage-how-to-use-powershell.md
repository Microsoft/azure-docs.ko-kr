---
title: PowerShell을 사용하여 Azure Table Storage 작업 수행 | Microsoft Docs
description: PowerShell을 사용 하 여 Azure Table storage 계정에서 데이터를 생성, 쿼리, 삭제 하는 등의 일반적인 작업을 실행 하는 방법에 대해 알아봅니다.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e643a7ce5ccf4aa5107df1e505d90a0767517350
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070414"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Table Storage 작업 수행 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure Table Storage는 매우 큰 비관계형 구조적 데이터 집합을 저장하고 쿼리하는 데 사용할 수 있는 NoSQL 데이터 저장소입니다. 서비스의 주요 구성 요소로는 테이블, 엔터티 및 속성이 있습니다. 테이블은 엔터티 컬렉션입니다. 엔터티는 속성의 집합입니다. 각 엔터티는 모두 이름 값 쌍으로 구성된 속성을 최대 252개 가질 수 있습니다. 이 문서에서는 Azure Table Storage 서비스 개념에 이미 익숙하다고 가정합니다. 자세한 내용은 [Table Service 데이터 모델 이해](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) 및 [.NET을 사용하여 Azure Table Storage 시작](../../cosmos-db/table-storage-how-to-use-dotnet.md)을 참조하세요.

이 방법 문서에서는 일반 Azure Table Storage 작업에 대해 설명합니다. 다음 방법을 알아봅니다. 

> [!div class="checklist"]
> * 테이블 만들기
> * 테이블 검색
> * 테이블 엔터티 추가
> * 테이블 쿼리
> * 테이블 엔터티 삭제
> * 테이블 삭제

이 아티클에서는 새 리소스 그룹에 새 Azure Storage 계정을 만드는 방법을 보여주며, 이 경우에 작업을 완료할 때 쉽게 제거할 수 있습니다. 기존 Storage 계정을 사용하려는 경우 해당 스토리지 계정을 대신 사용할 수 있습니다.

이 예에는 Az PowerShell modules 및가 필요 합니다 `Az.Storage (1.1.0 or greater)` `Az.Resources (1.2.0 or greater)` . PowerShell 창에서 `Get-Module -ListAvailable Az*`을 실행하여 버전을 확인합니다. 표시되는 항목이 없거나 업그레이드가 필요한 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.

> [!IMPORTANT]
> PowerShell에서 이 Azure 기능을 사용하려면 `Az` 모듈이 설치되어 있어야 합니다. 최신 버전의 `AzTable` 가 이전 AzureRM 모듈과 호환 되지 않습니다.
> 필요한 경우 [설치 된 Az module의 최신 설치 지침](/powershell/azure/install-az-ps) 을 따르세요.

Azure PowerShell를 설치 하거나 업데이트 한 후에는 엔터티를 관리 하기 위한 명령이 있는 모듈 **AzTable**를 설치 해야 합니다. 이 모듈을 설치하려면 PowerShell을 관리자 권한으로 실행하고 **Install-Module** 명령을 사용합니다.

> [!IMPORTANT]
> 모듈 이름 호환성을 위해 PowerShell 갤러리의 이전 이름에 동일한 모듈을 계속 게시 하 고 있습니다 `AzureRmStorageTables` . 이 문서에서는 새 이름만 참조 합니다.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Azure에 로그인

`Add-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>위치의 목록 검색

사용하려는 위치를 모르는 경우 사용 가능한 위치를 나열할 수 있습니다. 목록이 표시되면 사용할 위치를 찾습니다. 이러한 예제에서는 **eastus**를 사용합니다. 이 값은 나중에 사용하기 위해 변수 **location**에 저장합니다.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 리소스 그룹을 만듭니다. 

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 리소스 그룹 이름을 나중에 사용할 수 있도록 변수에 저장합니다. 이 예제에서는 *eastus* 지역에 *pshtablesrg*라는 리소스 그룹을 만듭니다.

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>스토리지 계정 만들기

[New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)를 사용하여 LRS(로컬 중복 스토리지)에 표준 범용 스토리지 계정을 만듭니다. 고유한 저장소 계정 이름을 지정 해야 합니다. 그런 다음 저장소 계정을 나타내는 컨텍스트를 가져옵니다. 저장소 계정에서 동작 하는 경우 자격 증명을 반복 해 서 제공 하는 대신 컨텍스트를 참조할 수 있습니다.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>새 테이블 만들기

테이블을 만들려면 [AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) cmdlet을 사용 합니다. 이 예제에서는 테이블을 `pshtesttable`이라고 합니다.

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>스토리지 계정의 테이블 목록 검색

[AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable)를 사용 하 여 저장소 계정의 테이블 목록을 검색 합니다.

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>특정 테이블에 대한 참조 가져오기

테이블에 대한 작업을 수행하려면 특정 테이블에 대한 참조가 필요합니다. [AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable)를 사용 하 여 참조를 가져옵니다.

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>특정 테이블의 CloudTable 속성 참조

> [!IMPORTANT]
> **AzTable** PowerShell 모듈을 사용 하는 경우 cloudtable을 사용 하는 것이 필수입니다. **AzStorageTable** 명령을 호출 하 여이 개체에 대 한 참조를 가져옵니다. 또한이 명령은 테이블이 아직 없는 경우 테이블을 만듭니다.

**AzTable**를 사용 하 여 테이블에 대 한 작업을 수행 하려면 특정 테이블의 cloudtable 속성에 대 한 참조가 필요 합니다.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>테이블 삭제

테이블을 삭제 하려면 [AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable)를 사용 합니다. 이 cmdlet은 테이블 및 포함된 모든 데이터를 제거합니다.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>리소스 정리

이 방법 문서를 시작하면서 새 리소스 그룹 및 스토리지 계정을 만들었으면, 해당 리소스 그룹을 제거하여 이 연습에서 만든 자산을 모두 제거할 수 있습니다. 이 명령은 리소스 그룹 자체뿐만 아니라 해당 그룹에 포함된 모든 리소스를 삭제합니다.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

이 방법 문서에서는 다음 방법을 포함하여 PowerShell을 사용한 일반적인 Azure Table Storage 작업에 대해 알아보았습니다. 

> [!div class="checklist"]
> * 테이블 만들기
> * 테이블 검색
> * 테이블 엔터티 추가
> * 테이블 쿼리
> * 테이블 엔터티 삭제
> * 테이블 삭제

자세한 내용은 다음 문서를 참조하세요.

* [스토리지 PowerShell cmdlet](/powershell/module/az.storage#storage)

* [PowerShell에서 Azure 테이블 작업-AzureRmStorageTable/AzTable PS 모듈 v 2.0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, macOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.
