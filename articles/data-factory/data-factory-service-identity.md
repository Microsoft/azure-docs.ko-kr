---
title: Data Factory에 대한 관리 ID
description: Azure Data Factory에 대한 관리 id에 대해 알아봅니다.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: 45699680ad2003c034bce588857f8b102a0b6d26
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76121780"
---
# <a name="managed-identity-for-data-factory"></a>Data Factory에 대한 관리 ID

이 문서에서는 Data Factory (이전의 관리 서비스 ID/MSI)의 관리 되는 id 및 작동 방법을 이해 하는 데 도움을 줍니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요

데이터 팩터리를 만들 때 팩터리 생성과 함께 관리 되는 id를 만들 수 있습니다. 관리 id는 Azure Active Directory에 등록 된 관리 되는 응용 프로그램으로,이 특정 데이터 팩터리를 나타냅니다.

Data Factory에 대한 관리 id는 다음과 같은 기능을 제공 합니다.

- [Azure Key Vault에 자격 증명을 저장](store-credentials-in-key-vault.md)합니다 .이 경우 데이터 팩터리 관리 id는 Azure Key Vault 인증에 사용 됩니다.
- [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure SQL Database](connector-azure-sql-database.md) 및 [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md)를 포함하는 커넥터.
- [웹 작업](control-flow-web-activity.md)

## <a name="generate-managed-identity"></a>관리 id 생성

Data Factory에 대한 관리 id는 다음과 같이 생성 됩니다.

- **Azure Portal 또는 PowerShell**을 통해 데이터 팩터리를 만들 때 관리 id는 항상 자동으로 만들어집니다.
- **SDK**를 통해 데이터 팩터리를 만들 때 관리 id는 생성을 위해 팩터리 개체에 "identity = new FactoryIdentity ()"를 지정 하는 경우에만 생성 됩니다. [.NET 빠른 시작 - 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md#create-a-data-factory)에서 예제를 참조하세요.
- **REST API**를 통해 데이터 팩터리를 만들 때 요청 본문에 "identity" 섹션을 지정 하는 경우에만 관리 id가 만들어집니다. [REST 빠른 시작 - 데이터 팩터리 만들기](quickstart-create-data-factory-rest-api.md#create-a-data-factory)에서 예제를 참조하세요.

데이터 팩터리가 관리 [id를 검색](#retrieve-managed-identity) 하는 것과 관련 된 관리 id가 없는 경우 데이터 팩터리를 프로그래밍 방식으로 id 초기자로 업데이트 하 여 명시적으로 생성할 수 있습니다.

- [PowerShell을 사용 하 여 관리 되는 id 생성](#generate-managed-identity-using-powershell)
- [REST API를 사용 하 여 관리 id 생성](#generate-managed-identity-using-rest-api)
- [Azure Resource Manager 템플릿을 사용 하 여 관리 id 생성](#generate-managed-identity-using-an-azure-resource-manager-template)
- [SDK를 사용 하 여 관리 되는 id 생성](#generate-managed-identity-using-sdk)

>[!NOTE]
>- 관리 id는 수정할 수 없습니다. 관리 id가 이미 있는 데이터 팩터리 업데이트는 영향을 주지 않습니다. 관리 id는 변경 되지 않고 유지 됩니다.
>- 팩터리 개체에 "identity" 매개 변수를 지정 하지 않거나 REST 요청 본문에 "identity" 섹션을 지정 하지 않고 관리 id가 이미 있는 데이터 팩터리를 업데이트 하는 경우 오류가 발생 합니다.
>- 데이터 팩터리를 삭제 하면 연결 된 관리 id가 함께 삭제 됩니다.

### <a name="generate-managed-identity-using-powershell"></a>PowerShell을 사용 하 여 관리 되는 id 생성

**AzDataFactoryV2** 명령을 다시 호출 합니다. 그러면 새로 생성 되는 "id" 필드가 표시 됩니다.

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>REST API를 사용 하 여 관리 id 생성

요청 본문의 "identity" 섹션에서 아래 API를 호출합니다.

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**요청 본문**: "identity": { "type": "SystemAssigned" }를 추가합니다.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**응답**: 관리 id가 자동으로 생성 되 고 "identity" 섹션이 그에 따라 채워집니다.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여 관리 id 생성

**템플릿**: "identity": { "type": "SystemAssigned" }를 추가합니다.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>SDK를 사용 하 여 관리 되는 id 생성

Identity=new FactoryIdentity()를 사용하여 데이터 팩터리 create_or_update 함수를 호출합니다. .NET을 사용하는 샘플 코드:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>관리 id 검색

Azure Portal에서 또는 프로그래밍 방식으로 관리 되는 id를 검색할 수 있습니다. 다음 섹션에서는 몇 가지 샘플을 보여 줍니다.

>[!TIP]
> 관리 id가 표시 되지 않으면 팩터리를 업데이트 하 여 [관리 id를 생성](#generate-managed-identity) 합니다.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Azure Portal를 사용 하 여 관리 되는 id 검색

데이터 팩터리 > 속성 > Azure Portal에서 관리 되는 id 정보를 찾을 수 있습니다.

- 관리 Id 개체 ID
- 관리 Id 테넌트
- 관리 Id 응용 프로그램 ID

관리 id 정보는 Azure Blob, Azure Data Lake Storage, Azure Key Vault 등 관리 되는 id 인증을 지 원하는 연결 된 서비스를 만들 때에도 표시 됩니다.

권한을 부여할 때 개체 ID 또는 데이터 팩터리 이름 (관리 id 이름)을 사용 하 여이 id를 찾습니다.

### <a name="retrieve-managed-identity-using-powershell"></a>PowerShell을 사용 하 여 관리 되는 id 검색

다음과 같이 특정 데이터 팩터리를 가져올 때 관리 id 보안 주체 ID 및 테넌트 ID가 반환 됩니다. **Principalid** 를 사용 하 여 액세스 권한을 부여 합니다.

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

보안 주체 ID를 복사 하 여 응용 프로그램 ID를 가져온 다음 보안 주체 ID를 매개 변수로 사용 하 여 아래 Azure Active Directory 명령을 실행할 수 있습니다.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>다음 단계
데이터 팩터리 관리 id를 사용 하는 경우 및 방법을 소개 하는 다음 항목을 참조 하세요.

- [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)
- [Azure 리소스 인증을 위해 관리 ID를 사용하여 Azure Data Lake Store 간에 데이터 복사](connector-azure-data-lake-store.md)

Azure 리소스에 대한 관리 되는 id에 대한 자세한 배경 정보는 Azure 리소스에 대한 [관리 Id 개요](/azure/active-directory/managed-identities-azure-resources/overview) 를 참조 하세요. 
