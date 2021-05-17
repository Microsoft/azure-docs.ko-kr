---
title: Service Fabric Mesh 앱에서 Azure Files 기반 볼륨 사용
description: Azure CLI를 사용하여 서비스 내부에 Azure Files 기반 볼륨을 탑재하여 Azure Service Fabric Mesh 애플리케이션에 상태를 저장하는 방법을 알아봅니다.
author: georgewallace
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: gwallace
ms.custom: mvc, devcenter , devx-track-azurecli
ms.openlocfilehash: 40d10568e13ad455bc5178821da80e89f4132e93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625840"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Service Fabric Mesh 애플리케이션에서 Azure Files 기반 볼륨 사용 

> [!IMPORTANT]
> Azure Service Fabric Mesh의 미리 보기가 사용 중지되었습니다. 새 배포는 더이상 Service Fabric Mesh API를 통해 허용되지 않습니다. 기존 배포에 대한 지원은 2021년 4월 28일까지 계속됩니다.
> 
> 자세한 내용은 [Azure Service Fabric Mesh 미리 보기 사용 중지](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)를 참조하세요.

이 문서에서는 Service Fabric Mesh 애플리케이션 서비스에 Azure Files 기반 볼륨을 탑재하는 방법을 설명합니다.  Azure Files 볼륨 드라이버는 서비스 상태를 유지하기 위해 컨테이너에 Azure Files 공유를 탑재하는 데 사용되는 Docker 볼륨 드라이버입니다. 볼륨은 범용 파일 스토리지를 제공하며 이를 통해 일반 디스크 I/O 파일 API를 사용하여 파일을 읽고 쓸 수 있습니다.  볼륨 및 애플리케이션 데이터를 저장하기 위한 옵션에 대한 자세한 내용은 [상태 저장](service-fabric-mesh-storing-state.md)을 참조하세요.

서비스에 볼륨을 탑재하려면 Service Fabric Mesh 애플리케이션에 볼륨 리소스를 만든 다음, 사용자 서비스에서 해당 볼륨을 참조합니다.  볼륨 리소스를 선언하고 서비스 리소스에서 참조하는 작업은 [YAML 기반 리소스 파일](#declare-a-volume-resource-and-update-the-service-resource-yaml) 또는 [JSON 기반 배포 템플릿](#declare-a-volume-resource-and-update-the-service-resource-json)에서 수행할 수 있습니다. 볼륨을 탑재하기 전에 먼저 Azure Storage 계정을 만들고 [Azure Files에 파일 공유](../storage/files/storage-how-to-create-file-share.md)를 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항
> [!NOTE]
> **Windows RS5 개발 머신의 배포에 대한 알려진 문제:** RS5 Windows 머신의 Powershell cmdlet New-SmbGlobalMapping에는 Azurefile 볼륨의 탑재를 방지하는 미해결된 버그가 있습니다. 다음은 AzureFile 기반 볼륨이 로컬 개발 머신에 탑재될 때 발생하는 샘플 오류입니다.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
이 이슈에 대한 해결 방법은 1) Powershell 관리자 권한으로 아래 명령을 실행하고 2) 머신을 다시 부팅하는 것입니다.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용하여 이 문서를 완료할 수 있습니다. 

이 문서에서 Azure CLI를 로컬로 사용하려면 `az --version`이 `azure-cli (2.0.43)` 이상을 반환하는지 확인합니다.  다음 [지침](service-fabric-mesh-howto-setup-cli.md)에 따라 Azure Service Fabric Mesh CLI 확장 모듈을 설치 또는 업데이트합니다.

Azure에 로그인하고 구독을 설정합니다.

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>스토리지 계정 및 파일 공유 만들기(선택 사항)
Azure Files 볼륨을 탑재하려면 스토리지 계정 및 파일 공유가 필요합니다.  기존 Azure Storage 계정 및 파일 공유를 사용해도 되고 리소스를 만들어도 됩니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>스토리지 계정 이름 및 키와 파일 공유 이름을 가져옵니다.
스토리지 계정 이름, 스토리지 계정 키 및 파일 공유 이름은 다음 섹션에서 `<storageAccountName>`, `<storageAccountKey>` 및 `<fileShareName>`으로 참조됩니다. 

스토리지 계정을 나열하고 사용하려는 파일 공유와 함께 스토리지 계정의 이름을 가져옵니다.
```azurecli-interactive
az storage account list
```

파일 공유의 이름 가져오기:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

스토리지 계정 키("key1") 가져오기:
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

[Azure Portal](https://portal.azure.com)에서 이러한 값을 찾을 수도 있습니다.
* `<storageAccountName>` - **스토리지 계정** 아래에서 파일 공유를 만들 때 사용한 스토리지 계정 이름입니다.
* `<storageAccountKey>` - **스토리지 계정** 에서 스토리지 계정을 선택한 다음, **액세스 키** 를 선택하고 **key1** 아래의 값을 사용합니다.
* `<fileShareName>` - **스토리지 계정** 에서 스토리지 계정을 선택한 다음, **파일** 을 선택합니다. 사용할 이름은 만든 파일 공유의 이름입니다.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>볼륨 리소스 선언 및 서비스 리소스 업데이트(JSON)

이전 단계에서 확인한 `<fileShareName>`, `<storageAccountName>` 및 `<storageAccountKey>` 값에 대한 매개 변수를 추가합니다. 

애플리케이션 리소스의 피어로 볼륨 리소스를 만듭니다. 이름 및 공급 기업(Azure Files 기반 볼륨을 사용하려면 “SFAzureFile”)를 지정합니다. `azureFileParameters`에서 이전 단계에서 확인한 `<fileShareName>`, `<storageAccountName>` 및 `<storageAccountKey>` 값에 대한 매개 변수를 지정합니다.

서비스에 볼륨을 탑재하려면 서비스의 `codePackages` 요소에 `volumeRefs`를 추가합니다.  `name`은 볼륨의 리소스 ID(또는 볼륨 리소스의 배포 템플릿 매개 변수)와 volume.yaml 리소스 파일에 선언된 볼륨의 이름입니다.  `destinationPath`는 볼륨이 탑재될 로컬 디렉터리입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>볼륨 리소스 선언 및 서비스 리소스 업데이트(YAML)

새 *volume.yaml* 파일을 애플리케이션에 대한 *앱 리소스* 디렉터리에 추가합니다.  이름 및 공급 기업(Azure Files 기반 볼륨을 사용하려면 “SFAzureFile”)를 지정합니다. `<fileShareName>`, `<storageAccountName>` 및 `<storageAccountKey>`는 이전 단계에서 확인한 값입니다.

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

서비스에 볼륨을 탑재하도록 *서비스 리소스* 디렉터리에 있는 *service.yaml* 파일을 업데이트합니다.  `volumeRefs` 요소를 `codePackages` 요소에 추가합니다.  `name`은 볼륨의 리소스 ID(또는 볼륨 리소스의 배포 템플릿 매개 변수)와 volume.yaml 리소스 파일에 선언된 볼륨의 이름입니다.  `destinationPath`는 볼륨이 탑재될 로컬 디렉터리입니다.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>다음 단계

- [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter)에서 Azure Files 볼륨 애플리케이션 예제를 확인합니다.
- Service Fabric 리소스 모델에 대한 자세한 내용은 [Service Fabric Mesh 리소스 모델](service-fabric-mesh-service-fabric-resources.md)을 참조하세요.
- Service Fabric Mesh에 대한 자세한 내용은 [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)를 참조하세요.
