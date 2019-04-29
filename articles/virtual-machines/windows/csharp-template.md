---
title: C# 및 Resource Manager 템플릿을 사용하여 VM 배포 | Microsoft Docs
description: C# 및 Resource Manager 템플릿을 사용하여 Azure VM을 배포하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: cynthn
ms.openlocfilehash: 50d0d78e9dc0c7f51fcd82dd16eab5a180eae073
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61402186"
---
# <a name="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template"></a>C# 및 Resource Manager 템플릿을 사용하여 Azure Virtual Machine 배포

이 문서에서는 C#을 사용하여 Azure Resource Manager 템플릿을 배포하는 방법을 보여줍니다. 만든 템플릿은 단일 서브넷을 사용하는 새 가상 네트워크에서 Windows Server를 실행하는 단일 가상 머신을 배포합니다.

가상 머신 리소스에 대한 자세한 설명은 [Azure Resource Manager 템플릿의 가상 머신](template-description.md)를 참조하세요. 템플릿에 있는 모든 리소스에 대한 자세한 내용은 [Azure Resource Manager 템플릿 연습](../../azure-resource-manager/resource-manager-template-walkthrough.md)을 참조하세요.

이러한 단계를 수행하려면 약 10분이 걸립니다.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

이 단계에서는 Visual Studio가 설치되어 있는지 확인하고 템플릿을 배포하는 데 사용하는 콘솔 애플리케이션을 만듭니다.

1. [Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio)를 아직 설치하지 않았으면 설치합니다. 작업 페이지에서 **.NET 데스크톱 개발**을 선택한 다음 **설치**를 클릭합니다. 요약에서 **.NET Framework 4 - 4.6 개발 도구**가 자동으로 선택되는 것을 볼 수 있습니다. Visual Studio를 이미 설치한 경우 Visual Studio 시작 관리자를 사용하여 .NET 작업을 추가할 수 있습니다.
2. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 클릭합니다.
3. **템플릿** > **Visual C#** 에서 **콘솔 앱(.NET Framework)** 을 선택하고, 프로젝트의 이름에 *myDotnetProject*를 입력하고 프로젝트의 위치를 선택한 다음 **확인**을 클릭합니다.

## <a name="install-the-packages"></a>패키지 설치

NuGet 패키지는 이러한 단계를 완료하는데 필요한 라이브러리를 설치하는 가장 쉬운 방법입니다. Visual Studio에서 필요한 라이브러리를 가져오려면 다음 단계를 수행합니다.

1. **도구** > **Nuget 패키지 관리자**를 클릭한 다음 **패키지 관리자 콘솔**을 클릭합니다.
2. 콘솔에서 다음이 명령을 입력합니다.

    ```powershell
    Install-Package Microsoft.Azure.Management.Fluent
    Install-Package WindowsAzure.Storage
    ```

## <a name="create-the-files"></a>파일 만들기

이 단계에서는 리소스를 배포하는 템플릿 파일과 템플릿에 매개 변수 값을 제공하는 매개 변수 파일을 만듭니다. 또한 Azure Resource Manager 작업을 수행하는 데 사용되는 권한 부여 파일을 만듭니다.

### <a name="create-the-template-file"></a>템플릿 파일 만들기

1. 솔루션 탐색기에서 *myDotnetProject* > **추가** > **새 항목**을 마우스 오른쪽 단추로 클릭한 다음 *Visual C# 항목*에서 **텍스트 파일**을 선택합니다. 파일 이름을 *CreateVMTemplate.json*으로 지정하고 **추가**를 클릭합니다.
2. 만든 파일에 이 JSON 코드를 추가합니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUsername": { "type": "string" },
        "adminPassword": { "type": "securestring" }
      },
      "variables": {
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks','myVNet')]", 
        "subnetRef": "[concat(variables('vnetID'),'/subnets/mySubnet')]", 
      },
      "resources": [
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "myPublicIPAddress",
          "location": "[resourceGroup().location]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "dnsSettings": {
              "domainNameLabel": "myresourcegroupdns1"
            }
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "myVNet",
          "location": "[resourceGroup().location]",
          "properties": {
            "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
            "subnets": [
              {
                "name": "mySubnet",
                "properties": { "addressPrefix": "10.0.0.0/24" }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-03-30",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "myNic",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/publicIPAddresses/', 'myPublicIPAddress')]",
            "[resourceId('Microsoft.Network/virtualNetworks/', 'myVNet')]"
          ],
          "properties": {
            "ipConfigurations": [
              {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": { "id": "[resourceId('Microsoft.Network/publicIPAddresses','myPublicIPAddress')]" },
                  "subnet": { "id": "[variables('subnetRef')]" }
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2016-04-30-preview",
          "type": "Microsoft.Compute/virtualMachines",
          "name": "myVM",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Network/networkInterfaces/', 'myNic')]"
          ],
          "properties": {
            "hardwareProfile": { "vmSize": "Standard_DS1" },
            "osProfile": {
              "computerName": "myVM",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]"
            },
            "storageProfile": {
              "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "2012-R2-Datacenter",
                "version": "latest"
              },
              "osDisk": {
                "name": "myManagedOSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
              }
            },
            "networkProfile": {
              "networkInterfaces": [
                {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces','myNic')]"
                }
              ]
            }
          }
        }
      ]
    }
    ```

3. CreateVMTemplate.json 파일을 저장합니다.

### <a name="create-the-parameters-file"></a>매개 변수 파일 만들기

템플릿에 정의된 리소스 매개 변수의 값을 지정하려면 값이 들어 있는 매개 변수 파일을 만듭니다.

1. 솔루션 탐색기에서 *myDotnetProject* > **추가** > **새 항목**을 마우스 오른쪽 단추로 클릭한 다음 *Visual C# 항목*에서 **텍스트 파일**을 선택합니다. 파일 이름을 *Parameters.json*으로 지정하고 **추가**를 클릭합니다.
2. 만든 파일에 이 JSON 코드를 추가합니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "azureuser" },
        "adminPassword": { "value": "Azure12345678" }
      }
    }
    ```

4. Parameters.json 파일을 저장합니다.

### <a name="create-the-authorization-file"></a>권한 부여 파일 만들기

템플릿을 배포하기 전에 [Active Directory 서비스 사용자](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)에 액세스할 수 있는지 확인합니다. 서비스 주체에서 Azure Resource Manager에서 요청을 인증받기 위한 토큰을 얻을 수 있습니다. 또한 권한 부여 파일에서 필요한 애플리케이션 ID, 인증 키 및 테넌트 ID를 기록해 두어야 합니다.

1. 솔루션 탐색기에서 *myDotnetProject* > **추가** > **새 항목**을 마우스 오른쪽 단추로 클릭한 다음 *Visual C# 항목*에서 **텍스트 파일**을 선택합니다. 파일 이름을 *azureauth.properties*로 지정하고 **추가**를 클릭합니다.
2. 다음과 같은 권한 부여 속성을 추가합니다.

    ```
    subscription=<subscription-id>
    client=<application-id>
    key=<authentication-key>
    tenant=<tenant-id>
    managementURI=https://management.core.windows.net/
    baseURL=https://management.azure.com/
    authURL=https://login.windows.net/
    graphURL=https://graph.windows.net/
    ```

    **&lt;subscription-id&gt;** 를 구독 식별자, **&lt;application-id&gt;** 를 Active Directory 애플리케이션 식별자, **&lt;authentication-key&gt;** 를 애플리케이션 키, **&lt;tenant-id&gt;** 를 테넌트 식별자로 바꿉니다.

3. azureauth.properties 파일을 저장합니다.
4. AZURE_AUTH_LOCATION이라는 Windows 환경 변수를 만든 권한 부여 파일의 전체 경로로 설정합니다. 예를 들어 다음 PowerShell 명령을 사용할 수 있습니다.

    ```powershell
    [Environment]::SetEnvironmentVariable("AZURE_AUTH_LOCATION", "C:\Visual Studio 2017\Projects\myDotnetProject\myDotnetProject\azureauth.properties", "User")
    ```

    

## <a name="create-the-management-client"></a>관리 클라이언트 만들기

1. 만들었던 프로젝트에 대한 Program.cs 파일을 연 후, 다음 using 문을 파일의 위쪽에 기존 문에 추가합니다.

    ```csharp
    using Microsoft.Azure.Management.Compute.Fluent;
    using Microsoft.Azure.Management.Compute.Fluent.Models;
    using Microsoft.Azure.Management.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent;
    using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

2. 관리 클라이언트를 만들려면 다음 코드를 Main 메서드에 추가합니다.

    ```csharp
    var credentials = SdkContext.AzureCredentialsFactory
        .FromFile(Environment.GetEnvironmentVariable("AZURE_AUTH_LOCATION"));

    var azure = Azure
        .Configure()
        .WithLogLevel(HttpLoggingDelegatingHandler.Level.Basic)
        .Authenticate(credentials)
        .WithDefaultSubscription();
    ```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

애플리케이션에 대한 값을 지정하려면 다음 코드를 Main 메서드에 추가합니다.

```csharp
var groupName = "myResourceGroup";
var location = Region.USWest;

var resourceGroup = azure.ResourceGroups.Define(groupName)
    .WithRegion(location)
    .Create();
```

## <a name="create-a-storage-account"></a>저장소 계정 만들기

템플릿 및 매개 변수는 Azure의 저장소 계정에서 배포됩니다. 이 단계에서는 계정을 만들고 파일을 업로드합니다. 

계정을 만들려면 Main 메서드에 다음 코드를 추가합니다.

```csharp
string storageAccountName = SdkContext.RandomResourceName("st", 10);

Console.WriteLine("Creating storage account...");
var storage = azure.StorageAccounts.Define(storageAccountName)
    .WithRegion(Region.USWest)
    .WithExistingResourceGroup(resourceGroup)
    .Create();

var storageKeys = storage.GetKeys();
string storageConnectionString = "DefaultEndpointsProtocol=https;"
    + "AccountName=" + storage.Name
    + ";AccountKey=" + storageKeys[0].Value
    + ";EndpointSuffix=core.windows.net";

var account = CloudStorageAccount.Parse(storageConnectionString);
var serviceClient = account.CreateCloudBlobClient();

Console.WriteLine("Creating container...");
var container = serviceClient.GetContainerReference("templates");
container.CreateIfNotExistsAsync().Wait();
var containerPermissions = new BlobContainerPermissions()
    { PublicAccess = BlobContainerPublicAccessType.Container };
container.SetPermissionsAsync(containerPermissions).Wait();

Console.WriteLine("Uploading template file...");
var templateblob = container.GetBlockBlobReference("CreateVMTemplate.json");
templateblob.UploadFromFileAsync("..\\..\\CreateVMTemplate.json").Result();

Console.WriteLine("Uploading parameters file...");
var paramblob = container.GetBlockBlobReference("Parameters.json");
paramblob.UploadFromFileAsync("..\\..\\Parameters.json").Result();
```

## <a name="deploy-the-template"></a>템플릿 배포

만든 저장소 계정에서 템플릿 및 매개 변수를 배포합니다. 

템플릿을 배포하려면 Main 메서드에 다음 코드를 추가합니다.

```csharp
var templatePath = "https://" + storageAccountName + ".blob.core.windows.net/templates/CreateVMTemplate.json";
var paramPath = "https://" + storageAccountName + ".blob.core.windows.net/templates/Parameters.json";
var deployment = azure.Deployments.Define("myDeployment")
    .WithExistingResourceGroup(groupName)
    .WithTemplateLink(templatePath, "1.0.0.0")
    .WithParametersLink(paramPath, "1.0.0.0")
    .WithMode(Microsoft.Azure.Management.ResourceManager.Fluent.Models.DeploymentMode.Incremental)
    .Create();
Console.WriteLine("Press enter to delete the resource group...");
Console.ReadLine();
```

## <a name="delete-the-resources"></a>리소스 삭제

Azure에서 사용되는 리소스에 대한 요금이 부과되기 때문에, 더 이상 필요하지 않은 리소스를 항상 삭제하는 것이 좋습니다. 리소스 그룹에서 각 리소스를 개별적으로 삭제할 필요가 없습니다. 리소스 그룹을 삭제하면 모든 해당 리소스가 자동으로 삭제됩니다. 

리소스 그룹을 삭제하려면 Main 메서드에 다음 코드를 추가합니다.

```csharp
azure.ResourceGroups.DeleteByName(groupName);
```

## <a name="run-the-application"></a>애플리케이션 실행

이 콘솔 애플리케이션을 처음부터 끝까지 완전히 실행하려면 약 5분이 필요합니다. 

1. 콘솔 애플리케이션을 실행하려면 **시작**을 클릭합니다.

2. **Enter** 키를 눌러 리소스를 삭제하기 전에 Azure Portal에서 리소스 만들기를 확인하는 데에 몇 분이 걸릴 수 있습니다. 배포에 대한 정보를 보려면 배포 상태를 클릭합니다.

## <a name="next-steps"></a>다음 단계

* 배포에 문제가 있는 경우 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](../../resource-manager-common-deployment-errors.md)을 살펴봅니다.
* [C#를 사용하여 Azure Virtual Machine 배포](csharp.md)를 검토하여 가상 머신 및 지원 리소스 배포 방법에 대해 알아 봅니다.