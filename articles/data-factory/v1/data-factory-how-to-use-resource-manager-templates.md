---
title: Data Factory에서 Resource Manager 템플릿 사용 | Microsoft 문서
description: Azure Resource Manager 템플릿을 만들고 사용하여 데이터 팩터리 엔터티를 만드는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 37724021-f55f-4e85-9206-6d4a48bda3d8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: ca8b3930b9d9f708d83dc760be3ee89737b074dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583369"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>템플릿을 사용하여 Azure Data Factory 엔터티 만들기
> [!NOTE]
> 이 문서의 내용은 Data Factory 버전 1에 적용됩니다. 

## <a name="overview"></a>개요
데이터 통합 요구에 Azure Data Factory를 사용하면서 다양한 환경에서 동일한 패턴을 재사용하거나 동일한 작업을 동일한 솔루션에서 반복적으로 구현하는 상황이 발생할 수 있습니다. 템플릿을 사용하면 이러한 시나리오에서 간편하게 구현 및 관리할 수 있습니다. Azure Data Factory의 템플릿은 재사용 및 반복이 관계된 시나리오에 적합합니다.

전 세계에 10개 제조 공장이 있는 조직의 상황을 고려해 보겠습니다. 각 공장의 로그는 개별 온-프레미스 SQL Server 데이터베이스에 저장됩니다. 회사에서는 임시 분석을 위해 클라우드에서 단일 데이터 웨어하우스를 구축할 하려고 합니다. 또한 논리는 동일하면서 구성은 다른 개발, 테스트 및 프러덕션 환경이 필요합니다.

이 경우 동일한 환경에서 작업을 반복하지만 각 제조 공장에 대해 서로 다른 값을 갖는 10개 데이터 팩터리가 생깁니다. 실제로 **반복**되는 것입니다. 템플릿에서는 이 고유 흐름(즉 각 데이터 팩터리에서 동일한 활동의 파이프라인)의 추상이 가능하지만 각 제조 공장마다 별도의 매개 변수 파일을 사용합니다. 

나아가 조직이 서로 다른 환경에서 수차례 이 10개 데이터 팩터리를 배포하려 하므로 템플릿은 개발, 테스트 및 프러덕션 환경에 별도의 매개 변수 파일을 적용함으로써 이러한 **재사용성**을 활용할 수 있습니다.

## <a name="templating-with-azure-resource-manager"></a>Azure Resource Manager의 템플릿
[Azure Resource Manager 템플릿](../../azure-resource-manager/resource-group-overview.md#template-deployment)은 Azure Data Factory에서 템플릿을 만드는 좋은 방법입니다. Resource Manager 템플릿은 JSON 파일을 통해 Azure 솔루션의 인프라와 구성을 정의합니다. Azure Resource Manager 템플릿이 모든/대부분의 Azure 서비스에서 작동하므로 광범위한 사용을 통해 Azure 자산의 모든 리소스를 간편하게 관리할 수 있습니다. Azure Resource Manager 템플릿에 대한 일반적인 내용은 [Azure Resource Manager 템플릿 작성](../../azure-resource-manager/resource-group-authoring-templates.md)을 참조하세요.

## <a name="tutorials"></a>자습서
Resource Manager 템플릿을 사용하여 데이터 팩터리 엔터티를 만들기 위한 단계별 지침은 다음 자습서를 참조하세요.

* [자습서: Azure Resource Manager 템플릿을 사용하여 데이터를 복사하기 위해 파이프라인 생성](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [자습서: Azure Resource Manager 템플릿을 사용하여 데이터를 처리하기 위해 파이프라인 생성](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>GitHub의 Data Factory 템플릿
GitHub에서 다음 Azure 빠른 시작 템플릿을 확인해 보세요.

* [Azure Blob Storage에서 Azure SQL Database로 데이터를 복사하는 데이터 팩터리 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Azure HDInsight 클러스터에서 Hive 활동으로 데이터 팩터리 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Salesforce에서 Azure Blob으로 데이터를 복사하는 데이터 팩터리 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [작업을 연결하는 Data Factory 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob): 데이터를 FTP 서버에서 Azure Blob으로 복사하고, 주문형 HDInsight 클러스터의 하이브 스크립트를 호출하여 데이터를 변환하며, Azure SQL Database에 결과를 복사합니다.

[Azure 빠른 시작](https://azure.microsoft.com/documentation/templates/)에서 Azure Data Factory를 자유롭게 공유할 수 있습니다. 이 리포지토리를 통해 공유할 수 있는 템플릿을 개발할 때는 [기여 가이드](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE)를 참조하세요.

다음 섹션에서는 Resource Manager 템플릿에서 데이터 팩터리 리소스를 정의하는 것과 관련한 세부 정보를 제공합니다. 

## <a name="defining-data-factory-resources-in-templates"></a>템플릿의 데이터 팩터리 리소스 정의
데이터 팩터리 정의를 위한 최상위 템플릿은 다음과 같습니다.

```JSON
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": { ...
},
"variables": { ...
},
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
    "resources": [
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>데이터 팩터리 정의
다음 예제에서처럼 Resource Manager 템플릿에서 데이터 팩터리를 정의합니다.

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
dataFactoryName은 “variables”에 다음과 같이 정의됩니다.

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>연결된 서비스 정의

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

배포하려는 특정 연결 서비스의 JSON 속성과 관련한 자세한 내용은 [스토리지 연결 서비스](data-factory-azure-blob-connector.md#azure-storage-linked-service) 또는 [Compute 연결 서비스](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)를 참조하세요. "DependsOn" 매개 변수는 해당 데이터 팩터리의 이름을 지정합니다. Azure Storage의 연결 서비스 정의 예제는 다음 JSON 정의에 나와 있습니다.

### <a name="define-datasets"></a>데이터 세트 정의

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
배포할 특정 데이터 세트 형식의 JSON 속성에 관한 자세한 내용은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats)를 참조하세요. "DependsOn" 매개 변수는 해당 데이터 팩터리와 저장소 연결 서비스의 이름을 지정합니다. Azure 스토리지 서비스는 Blob Storage, Table Storage, Queue Storage 및 File Storage 등의 4가지 서비스를 제공합니다.

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>파이프라인 정의

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

배포할 특정 파이프라인과 활동의 JSON 속성에 대한 자세한 내용은 [파이프라인 정의](data-factory-create-pipelines.md#pipeline-json)를 참조하세요. "dependsOn" 매개 변수는 데이터 팩터리와 해당 연결 서비스 또는 저장소 집합의 이름을 지정합니다. Azure Blob Storage의 데이터를 Azure SQL Database에 복사하는 파이프라인 예제는 다음 JSON 코드 조각에 있습니다.

```JSON
"type": "datapipelines",
"name": "[variables('pipelineName')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('azureSqlLinkedServiceName')]",
    "[variables('blobInputDatasetName')]",
    "[variables('sqlOutputDatasetName')]"
],
"apiVersion": "2015-10-01",
"properties": {
    "activities": [
    {
        "name": "CopyFromAzureBlobToAzureSQL",
        "description": "Copy data frm Azure blob to Azure SQL",
        "type": "Copy",
        "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
        ],
        "outputs": [
            {
                "name": "[variables('sqlOutputDatasetName')]"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
            },
            "translator": {
                "type": "TabularTranslator",
                "columnMappings": "Column0:FirstName,Column1:LastName"
            }
        },
        "Policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 3,
            "timeout": "01:00:00"
        }
    }
    ],
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>데이터 팩터리 템플릿 매개 변수화
매개 변수화의 모범 사례는 [Azure Resource Manager 템플릿 생성 모범 사례](../../azure-resource-manager/resource-manager-template-best-practices.md)를 참조하세요. 일반적으로 매개 변수는 최소로 사용해야 합니다. 특히 그 대신 변수를 사용할 수 있는 경우가 그렇습니다. 다음 시나리오에서는 매개 변수만 제공합니다.

* 설정은 환경에 따라 달라집니다(예: 개발, 테스트, 프러덕션 환경).
* 암호(Secret)(예: 암호(password))

템플릿을 사용하여 Azure Data Factory 엔터티를 배포할 때 [Azure Key Vault](../../key-vault/key-vault-overview.md)에서 암호를 가져와야 할 경우 다음 예제처럼 **키 자격 증명 모음**과 **암호 이름**을 표시합니다.

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> 기존 데이터 팩터리에 대한 템플릿 내보내기는 아직 지원되지 않지만 작업 중에 있습니다.
>
>
