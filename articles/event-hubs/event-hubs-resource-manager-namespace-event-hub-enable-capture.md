---
title: 캡쳐를 사용하도록 설정된 이벤트 허브 만들기 - Azure Event Hubs | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 하나의 이벤트 허브가 있는 Azure Event Hubs 네임스페이스를 만들고 캡처를 사용하도록 설정
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 8bdda6a2-5ff1-45e3-b696-c553768f1090
ms.service: event-hubs
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: 51b69e8b7f6c980fd851cdf3e60ecfe0ade29e71
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187336"
---
# <a name="create-a-namespace-with-event-hub-and-enable-capture-using-a-template"></a>이벤트 허브가 있는 네임스페이스를 만들고 템플릿을 사용하여 캡처를 사용하도록 설정

이 문서에서는 Azure Resource Manager 템플릿을 사용하여 하나의 이벤트 허브 인스턴스가 있는 [Event Hubs](event-hubs-what-is-event-hubs.md) 네임스페이스를 만들고 해당 이벤트 허브에서 [캡처 기능](event-hubs-capture-overview.md)을 사용하도록 설정하는 방법을 보여 줍니다. 또한 어떤 리소스를 배포할지 정의하는 방법 및 배포를 실행할 때 매개 변수를 지정하는 방법을 설명합니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다.

이 문서에는 선택한 대상에 따라 이벤트가 Azure Storage Blobs에 캡처되는지, 아니면 Azure Data Lake Store에 캡쳐되는지를 지정하는 방법을 보여줍니다.

템플릿을 만드는 더 자세한 내용은 [Azure Resource Manager 템플릿 작성하기][Authoring Azure Resource Manager templates]를 참조하십시오. 템플릿에서 사용할 JSON 구문 및 속성은 [Microsoft.EventHub 리소스 종류](/azure/templates/microsoft.eventhub/allversions)를 참조하세요.

Azure 리소스 명명 규칙의 패턴 및 방법에 대 한 자세한 내용은 [Azure 리소스 명명 규칙][Azure Resources naming conventions]을 참조 하세요.

전체 템플릿은 다음 GitHub 링크를 클릭합니다.

- [이벤트 허브 및 저장소 템플릿에 캡처 사용][Event Hub and enable Capture to Storage template] 
- [이벤트 허브 및 Azure Data Lake Store 템플릿에 캡처 사용][Event Hub and enable Capture to Azure Data Lake Store template]

> [!NOTE]
> 최신 템플릿을 확인하려면 [Azure 빠른 시작 템플릿][Azure Quickstart Templates] 갤러리를 방문하여 Event Hubs를 검색하세요.
> 
> 

## <a name="what-will-you-deploy"></a>배포할 항목

이 템플릿을 사용하면 하나의 이벤트 허브가 있는 Event Hubs 네임스페이스를 배포하고 [Event Hubs 캡처](event-hubs-capture-overview.md)도 사용할 수 있습니다. Event Hubs 캡처를 사용하면 Event Hubs의 스트리밍 데이터를 지정한 시간이나 선택한 크기 간격 내에서 Azure Blob Storage 또는 Azure Data Lake Store에 자동으로 전달할 수 있습니다. Azure Storage로 Event Hubs 캡처를 사용하도록 설정하려면 다음 단추를 클릭합니다.

[![Azure에 배포](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

Azure Data Lake Store로 Event Hubs 캡처를 사용하도록 설정하려면 다음 단추를 클릭합니다.

[![Azure에 배포](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture-for-adls%2Fazuredeploy.json)

## <a name="parameters"></a>매개 변수

Azure 리소스 관리자와 함께 템플릿을 배포할 때 지정하고자 하는 값으로 매개 변수를 정의합니다. 템플릿은 모든 매개 변수 값이 포함된 `Parameters` 라는 섹션을 포함합니다. 배포하는 프로젝트에 따라 또는 환경에 따라 달라지는 이러한 값에 대한 매개 변수를 정의해야 합니다. 항상 동일하게 유지되는 값으로 매개 변수를 정의하지 마십시오. 각 매개 변수 값은 배포되는 리소스를 정의하는 템플릿에 사용됩니다.

템플릿은 다음 매개 변수를 정의합니다.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

만들 Event Hubs 네임스페이스의 이름입니다.

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName

Event Hubs 네임스페이스에서 만든 이벤트 허브의 이름입니다.

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays

이벤트 허브에서 메시지를 보관할 기간(일수)입니다. 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount

이벤트 허브에서 만들 파티션 수입니다.

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### <a name="captureenabled"></a>captureEnabled

이벤트 허브에서 캡처를 사용하도록 설정합니다.

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
### <a name="captureencodingformat"></a>captureEncodingFormat

이벤트 데이터를 직렬화하기 위해 지정하는 인코딩 형식입니다.

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

### <a name="capturetime"></a>captureTime

Event Hubs 캡처를 통해 데이터를 캡처하기 시작하는 시간 간격입니다.

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"The time window in seconds for the capture"
    }
}
```

### <a name="capturesize"></a>captureSize
캡처를 통해 데이터를 캡처하기 시작하는 크기 간격입니다.

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

### <a name="capturenameformat"></a>captureNameFormat

Avro 파일을 쓰기 위해 Event Hubs 캡처에 의해 사용되는 이름 형식입니다. 캡처 이름 형식은 `{Namespace}`, `{EventHub}`, `{PartitionId}`, `{Year}`, `{Month}`, `{Day}`, `{Hour}`, `{Minute}` 및 `{Second}` 필드를 포함해야 합니다. 구분 기호 유무에 관계 없이 정렬될 수 있습니다.
 
```json
"captureNameFormat": {
      "type": "string",
      "defaultValue": "{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}",
      "metadata": {
        "description": "A Capture Name Format must contain {Namespace}, {EventHub}, {PartitionId}, {Year}, {Month}, {Day}, {Hour}, {Minute} and {Second} fields. These can be arranged in any order with or without delimeters. E.g.  Prod_{EventHub}/{Namespace}\\{PartitionId}_{Year}_{Month}/{Day}/{Hour}/{Minute}/{Second}"
      }
    }
  
```

### <a name="apiversion"></a>apiVersion

템플릿의 API 버전입니다.

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2017-04-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

Azure Storage를 대상으로 선택한 경우 다음 매개 변수를 사용합니다.

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId

원하는 Storage 계정에 캡처하도록 설정하기 위해 캡처에 Azure Storage 계정 리소스 ID가 필요합니다.

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource ID where you want the blobs be captured"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName

이벤트 데이터를 캡처할 BLOB 컨테이너입니다.

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```

Azure Data Lake Store Gen 1을 대상으로 선택 하는 경우 다음 매개 변수를 사용 합니다. 이벤트를 캡처하려는 Data Lake Store 경로에서 사용 권한을 설정해야 합니다. 권한을 설정 하려면 [Azure Data Lake Storage Gen 1로 데이터 캡처](event-hubs-capture-enable-through-portal.md#capture-data-to-azure-data-lake-storage-gen-1)를 참조 하세요.

### <a name="subscriptionid"></a>subscriptionId

Event Hubs 네임스페이스와 Azure Data Lake Store에 대한 구독 ID입니다. 이러한 두 리소스가 동일한 구독 ID에 있어야 합니다.

```json
"subscriptionId": {
    "type": "string",
    "metadata": {
        "description": "Subscription ID of both Azure Data Lake Store and Event Hubs namespace"
     }
 }
```

### <a name="datalakeaccountname"></a>dataLakeAccountName

캡처된 이벤트에 대한 Azure Data Lake Store 이름입니다.

```json
"dataLakeAccountName": {
    "type": "string",
    "metadata": {
        "description": "Azure Data Lake Store name"
    }
}
```

### <a name="datalakefolderpath"></a>dataLakeFolderPath

캡처된 이벤트에 대한 대상 폴더 경로입니다. 캡처 작업이 진행되는 동안 이벤트가 푸시될 Data Lake Store의 폴더입니다. 이 폴더에 대한 권한을 설정하려면 [Azure Data Lake Store를 사용하여 Event Hubs에서 데이터 캡처](../data-lake-store/data-lake-store-archive-eventhub-capture.md)를 참조하세요.

```json
"dataLakeFolderPath": {
    "type": "string",
    "metadata": {
        "description": "Destination capture folder path"
    }
}
```

## <a name="resources-to-deploy-for-azure-storage-as-destination-to-captured-events"></a>캡처된 이벤트에 대한 대상으로 Azure Storage에 배포할 리소스

하나의 이벤트 허브가 있는 **EventHub** 형식의 네임스페이스를 만들고 Azure Blob Storage에 캡처를 사용하도록 설정합니다.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('eventHubNamespaceName')]",
      "type": "Microsoft.EventHub/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "isAutoInflateEnabled": "true",
        "maximumThroughputUnits": "7"
      },
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('eventHubName')]",
          "type": "EventHubs",
          "dependsOn": [
            "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
          ],
          "properties": {
            "messageRetentionInDays": "[parameters('messageRetentionInDays')]",
            "partitionCount": "[parameters('partitionCount')]",
            "captureDescription": {
              "enabled": "true",
              "skipEmptyArchives": false,
              "encoding": "[parameters('captureEncodingFormat')]",
              "intervalInSeconds": "[parameters('captureTime')]",
              "sizeLimitInBytes": "[parameters('captureSize')]",
              "destination": {
                "name": "EventHubArchive.AzureBlockBlob",
                "properties": {
                  "storageAccountResourceId": "[parameters('destinationStorageAccountResourceId')]",
                  "blobContainer": "[parameters('blobContainerName')]",
                  "archiveNameFormat": "[parameters('captureNameFormat')]"
                }
              }
            }
          }

        }
      ]
    }
  ]
```

## <a name="resources-to-deploy-for-azure-data-lake-store-as-destination"></a>Azure Data Lake Store에 대상으로 배포할 리소스

하나의 이벤트 허브가 있는 **EventHub** 형식의 네임스페이스를 만들고 Azure Data Lake Store에 캡처를 사용하도록 설정합니다.

```json
 "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('eventHubName')]",
                        "captureDescription": {
                            "enabled": "true",
                            "skipEmptyArchives": false,
                            "encoding": "[parameters('archiveEncodingFormat')]",
                            "intervalInSeconds": "[parameters('captureTime')]",
                            "sizeLimitInBytes": "[parameters('captureSize')]",
                            "destination": {
                                "name": "EventHubArchive.AzureDataLake",
                                "properties": {
                                    "DataLakeSubscriptionId": "[parameters('subscriptionId')]",
                                    "DataLakeAccountName": "[parameters('dataLakeAccountName')]",
                                    "DataLakeFolderPath": "[parameters('dataLakeFolderPath')]",
                                    "ArchiveNameFormat": "[parameters('captureNameFormat')]"
                                }
                            }
                        }
                    }
                }
            ]
        }
    ]
```

> [!NOTE]
> 캡처 기간 동안 이벤트가 하나도 발생하지 않으면 **skipEmptyArchives** 속성을 사용하여 빈 파일 내보내기를 사용 또는 사용하지 않도록 설정할 수 있습니다. 

## <a name="commands-to-run-deployment"></a>배포 실행 명령

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

템플릿을 배포하여 Azure Storage로 Event Hubs 캡처를 사용하도록 설정합니다.
 
```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

템플릿을 배포하여 Azure Data Lake Store로 Event Hubs 캡처를 사용하도록 설정합니다.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI

Azure Blob Storage를 대상으로:

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```

Azure Data Lake Store를 대상으로:

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture-for-adls/azuredeploy.json][]
```

## <a name="next-steps"></a>다음 단계

[Azure Portal](https://portal.azure.com)을 통해 Event Hubs 캡처를 구성할 수도 있습니다. 자세한 내용은 [Azure Portal을 사용하여 Event Hubs 캡처를 사용하도록 설정](event-hubs-capture-enable-through-portal.md)을 참조하세요.

Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요](event-hubs-what-is-event-hubs.md)
* [이벤트 허브 만들기](event-hubs-create.md)
* [Event Hubs FAQ](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Azure Resources naming conventions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Event hub and enable Capture to Storage template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture
[Event hub and enable Capture to Azure Data Lake Store template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture-for-adls
