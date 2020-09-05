---
title: 템플릿을 사용하여 Azure IoT Hub 만들기(.NET) | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 C# 프로그램으로 IoT Hub를 만드는 방법입니다.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: dcec1e40e9095c27abb1470e3739f65035a96834
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89007182"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Azure Resource Manager 템플릿을 사용하여 IoT Hub 만들기(.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Azure 리소스 관리자를 사용하여 Azure IoT Hub를 프로그래밍 방식으로 만들고 관리합니다. 이 자습서는 Azure Resource Manager 템플릿을 사용하여 C# 프로그램에서 IoT Hub를 만드는 방법을 보여 줍니다.

> [!NOTE]
> Azure에는 리소스를 만들고 작업 하기 위한 두 가지 배포 모델인  [Azure Resource Manager와 클래식](../azure-resource-manager/management/deployment-models.md)이 있습니다.  이 문서에서는 Azure Resource Manager 배포 모델 사용에 대해 설명합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 자습서를 완료하려면 다음이 필요합니다.

* Visual Studio.
* 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial] 을 만들 수 있습니다.
* Azure Resource Manager 템플릿 파일을 저장할 수 있는 [Azure Storage 계정][lnk-storage-account]입니다.
* [Azure PowerShell 1.0][lnk-powershell-install] 이상.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Visual Studio 프로젝트 준비

1. Visual Studio에서 **콘솔 앱(.NET Framework)** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 바탕 화면 프로젝트를 만듭니다. 프로젝트 이름을 **CreateIoTHub**로 지정합니다.

2. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.

3. NuGet 패키지 관리자에서 **시험판 포함**을 선택하고 **찾아보기** 페이지에서 **Microsoft.Azure.Management.ResourceManager**를 검색합니다. 패키지를 선택하고 **설치**를 클릭하고 **변경 내용 검토**에서 **확인**을 클릭한 다음 **동의함**을 클릭하여 라이선스에 동의합니다.

4. NuGet 패키지 관리자에서 **Microsoft.IdentityModel.Clients.ActiveDirectory**를 검색합니다.  **설치**를 클릭하고 **변경 내용 검토**에서 **확인**을 클릭한 다음 **동의함**을 클릭하여 라이선스에 동의합니다.

5. Program.cs에서 기존 **using** 문을 다음 코드로 바꿉니다.

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. Program.cs에서 다음 정적 변수를 추가하여 자리 표시자 값을 바꿉니다. 이 자습서의 앞부분에서 **ApplicationId**, **SubscriptionId**, **TenantId** 및 **암호**를 적어 두었습니다. **Azure Storage 계정 이름**은 Azure Resource Manager 템플릿 파일을 저장할 Azure Storage 계정의 이름입니다. **리소스 그룹 이름**은 IoT Hub를 만들 때 사용할 리소스 그룹의 이름입니다. 이름은 기존 또는 새 리소스 그룹일 수 있습니다. **배포 이름**은 **Deployment_01**과 같은 배포의 이름입니다.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>IoT hub를 만들 템플릿 제출

JSON 템플릿과 매개 변수 파일을 사용하여 리소스 그룹에 IoT Hub를 만듭니다. Azure Resource Manager 템플릿을 사용하여 기존 IoT Hub를 변경할 수도 있습니다.

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**, **새 항목**을 차례로 클릭합니다. 프로젝트에 **template.json**이라는 JSON 파일을 추가합니다.

2. 표준 IoT Hub를 **미국 동부** 지역에 추가하려면 **template.json**의 콘텐츠를 다음 리소스 정의로 바꿉니다. IoT Hub를 지원하는 현재 지역 목록에 대해서는 [Azure 상태][lnk-status]를 참조하세요.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**, **새 항목**을 차례로 클릭합니다. 프로젝트에 **parameters.json**이라는 JSON 파일을 추가합니다.

4. 새 IoT Hub의 이름을 **{your initials}mynewiothub** 등으로 설정하는 다음 매개 변수 정보로 **parameters.json**의 내용을 대체합니다. IoT Hub 이름은 전역적으로 고유해야 하므로 사용자의 이름 또는 이니셜을 포함해야 합니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. **서버 탐색기**에서 Azure 구독에 연결하고 Azure Storage 계정에서 **templates**라는 컨테이너를 만듭니다. **속성** 패널에서 **templates** 컨테이너의 **공용 읽기 액세스** 권한을 **Blob**으로 설정합니다.

6. **서버 탐색기**에서 **templates** 컨테이너를 마우스 오른쪽 단추로 클릭한 다음 **Blob 컨테이너 보기**를 클릭합니다. **Blob 업로드** 단추를 클릭하고, **parameters.json** 및 **templates.json** 파일을 선택한 다음 **열기**를 클릭하여 JSON 파일을 **templates** 컨테이너에 업로드합니다. JSON 데이터를 포함하는 Blob의 URL은 다음과 같습니다.

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Program.cs에 다음 메서드를 추가합니다.

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. **CreateIoTHub** 메서드에 다음 코드를 추가하여 Azure Resource Manager에게 템플릿 및 매개 변수 파일을 제출합니다.

    ```csharp
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

9. 새 IoT Hub의 상태 및 키를 표시하는 다음 코드를 **CreateIoTHub** 메서드에 추가합니다.

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>애플리케이션을 완료하고 실행합니다.

이제 애플리케이션을 빌드하고 실행하기 전에 **CreateIoTHub** 메서드를 호출하여 완료합니다.

1. **Main** 메서드의 끝에 다음 코드를 추가 합니다.

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. **빌드**, **솔루션 빌드**를 차례로 클릭합니다. 오류를 수정합니다.

3. **디버그**, **디버깅 시작**을 차례로 클릭하여 애플리케이션을 실행합니다. 배포를 실행하는 데 몇 분 정도 걸릴 수 있습니다.

4. 애플리케이션이 새 IoT Hub에 추가되었는지 확인하려면 [Azure Portal][lnk-azure-portal]을 방문하여 리소스 목록을 확인합니다. 또는 **AzResource** PowerShell cmdlet을 사용 합니다.

> [!NOTE]
> 이 예제 애플리케이션은 대금이 청구되는 S1 표준 IoT Hub를 추가합니다. 완료 되 면 [Azure Portal][lnk-azure-portal] 또는 **AzResource** PowerShell cmdlet을 사용 하 여 IoT hub를 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Azure Resource Manager 템플릿을 사용하여 C# 프로그램에서 IoT Hub를 배포했으므로 구체적인 내용을 알아볼 차례입니다.

* [IoT Hub 리소스 공급자 REST API][lnk-rest-api]의 기능을 읽어보세요.
* Azure Resource Manager의 기능에 대해 자세히 알아보려면 [Azure Resource Manager 개요][lnk-azure-rm-overview]를 참조하세요.
* 템플릿에서 사용할 JSON 구문 및 속성은 [Microsoft.Devices 리소스 종류](/azure/templates/microsoft.devices/iothub-allversions)를 참조하세요.

IoT Hub를 개발하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [C SDK 소개][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure IoT Edge를 사용하여 에지 디바이스에 AI 배포][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
