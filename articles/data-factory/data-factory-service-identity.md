---
title: Data Factory에 대한 관리 ID
description: Azure Data Factory의 관리 ID에 대해 알아봅니다.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: abnarain
ms.openlocfilehash: 632c5930fdae7a4a7504d57b9cb8f285efad8d0a
ms.sourcegitcommit: 1fbd591a67e6422edb6de8fc901ac7063172f49e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109481664"
---
# <a name="managed-identity-for-data-factory"></a>Data Factory에 대한 관리 ID

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Data Factory(이전의 Managed Service Identity/MSI)의 관리 ID가 무엇이며 어떻게 작동하는지를 이해하는 데 도움을 줍니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요

데이터 팩터리를 만들 때 팩터리가 만들어지면서 관리 ID도 생성할 수 있습니다. 관리 ID는 Azure Activity Directory에 등록된 관리형 애플리케이션이고 이 특정 데이터 팩터리를 나타냅니다.

Data Factory에 대한 관리 ID는 다음과 같은 기능을 제공합니다.

- [Azure Key Vault에서 자격 증명을 저장](store-credentials-in-key-vault.md)하는 경우에 데이터 팩터리 관리 ID를 Azure Key Vault 인증에 사용합니다.
- Azure Blob Storage, Azure Data Explorer, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Azure SQL Managed Instance, Azure Synapse Analytics, REST, Databricks 활동, 웹 활동 등을 비롯한 관리 ID 인증을 사용하여 데이터 저장소 또는 컴퓨터에 액세스합니다. 자세한 내용은 커넥터 및 작업 문서를 확인하세요.

## <a name="generate-managed-identity"></a>관리 ID 생성

Data Factory에 대한 관리 ID는 다음과 같이 생성됩니다.

- **Azure Portal 또는 PowerShell** 을 통해 데이터 팩터리를 만들 때 관리 ID가 항상 자동으로 생성됩니다.
- **SDK** 를 통해 데이터 팩터리를 만들 때, 생성할 팩터리 개체에서 "Identity = new FactoryIdentity()"를 지정하는 경우에만 관리 ID가 생성됩니다. [.NET 빠른 시작 - 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md#create-a-data-factory)에서 예제를 참조하세요.
- **REST API** 를 통해 데이터 팩터리를 만들 때, 요청 본문에 "identity" 섹션을 지정하는 경우에만 관리 ID가 생성됩니다. [REST 빠른 시작 - 데이터 팩터리 만들기](quickstart-create-data-factory-rest-api.md#create-a-data-factory)에서 예제를 참조하세요.

데이터 팩터리에 다음 [관리 ID 검색](#retrieve-managed-identity) 지침과 관련된 관리 ID가 없는 것이 확인되면 데이터 팩터리를 프로그래밍 방식으로 ID 초기자로 업데이트하여 명시적으로 생성할 수 있습니다.

- [PowerShell을 사용하여 관리 ID 생성](#generate-managed-identity-using-powershell)
- [REST API를 사용하여 관리 ID 생성](#generate-managed-identity-using-rest-api)
- [Azure Resource Manager 템플릿을 사용하여 관리 ID 생성](#generate-managed-identity-using-an-azure-resource-manager-template)
- [SDK를 사용하여 관리 ID 생성](#generate-managed-identity-using-sdk)

>[!NOTE]
>- 관리 ID는 수정할 수 없습니다. 이미 관리 ID가 있는 데이터 팩터리를 업데이트해도 영향은 없으며 관리 ID는 변경되지 않습니다.
>- 팩터리 개체에 "identity" 매개 변수를 지정하지 않거나 REST 요청 본문에 "identity" 섹션을 지정하지 않고 관리 ID가 이미 있는 데이터 팩터리를 업데이트하면 오류가 발생합니다.
>- 데이터 팩터리를 삭제하면 연결된 관리 ID가 함께 삭제됩니다.

### <a name="generate-managed-identity-using-powershell"></a>PowerShell을 사용하여 관리 ID 생성

**Set-AzDataFactoryV2** 명령을 다시 호출하면 새로 생성된 "Identity" 필드가 표시됩니다.

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

### <a name="generate-managed-identity-using-rest-api"></a>REST API를 사용하여 관리 ID 생성

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

**응답**: 관리 ID가 자동으로 생성되고 "identity" 섹션이 그에 따라 채워집니다.

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

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 관리 ID 생성

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

### <a name="generate-managed-identity-using-sdk"></a>SDK를 사용하여 관리 ID 생성

Identity=new FactoryIdentity()를 사용하여 데이터 팩터리 create_or_update 함수를 호출합니다. .NET을 사용하는 샘플 코드:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>관리 ID 검색

Azure Portal에서 또는 프로그래밍 방식으로 관리 ID를 검색할 수 있습니다. 다음 섹션에서는 몇 가지 샘플을 보여 줍니다.

>[!TIP]
> 관리 ID가 표시되지 않으면 팩터리를 업데이트하여 [관리 ID를 생성](#generate-managed-identity)합니다.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Azure Portal을 사용하여 관리 ID 검색

Azure Portal -> 데이터 팩터리 -> 속성에서 관리 ID 정보를 찾을 수 있습니다.

- 관리 ID 개체 ID
- 관리 ID 테넌트

관리 ID 정보는 Azure Blob, Azure Data Lake Storage, Azure Key Vault 등의 관리 ID 인증을 지원하는 연결된 서비스를 만들 때도 표시됩니다.

사용 권한을 부여할 때 Azure 리소스의 액세스 제어(IAM) 탭에서 역할 할당을 추가하고 액세스 권한을 할당하고 시스템 할당 관리 ID에서 Data Factory을 선택하고 팩터리 이름으로 선택합니다. 또는 일반적으로 개체 ID 또는 데이터 팩터리 이름을 관리 ID 이름으로 사용하여 이 ID를 찾을 수 있습니다. 관리 ID의 애플리케이션 ID를 알아야 하는 경우 PowerShell을 사용할 수 있습니다.

### <a name="retrieve-managed-identity-using-powershell"></a>PowerShell을 사용하여 관리 ID 검색

다음과 같이 특정 데이터 팩터리를 가져오는 경우 관리 ID의 보안 주체 ID 및 테넌트 ID가 반환됩니다. **Principalid** 를 사용하여 액세스 권한을 부여합니다.

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

보안 주체 ID를 복사하여 애플리케이션 ID를 가져온 다음, 보안 주체 ID를 매개 변수로 사용하여 아래 Azure Active Directory 명령을 실행할 수 있습니다.

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

### <a name="retrieve-managed-identity-using-rest-api"></a>REST API를 사용하여 관리 ID 검색

다음과 같이 특정 데이터 팩터리를 가져오는 경우 관리 ID의 보안 주체 ID 및 테넌트 ID가 반환됩니다.

요청에서 아래 API를 호출합니다.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**응답**: 아래 예제에서와 같이 응답을 받게 됩니다. "ID" 섹션이 그에 따라 채워집니다.

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> ARM 템플릿에서 관리 ID를 검색하려면 ARM JSON에 **출력** 섹션을 추가합니다.

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```

## <a name="next-steps"></a>다음 단계
데이터 팩터리 관리 ID를 사용하는 시기 및 방법을 소개하는 다음 항목을 참조하세요.

- [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)
- [Azure 리소스 인증을 위해 관리 ID를 사용하여 Azure Data Lake Store 간에 데이터 복사](connector-azure-data-lake-store.md)

데이터 팩터리 관리 ID의 기준이 되는 Azure 리소스의 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.