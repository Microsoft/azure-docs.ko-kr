---
title: ARM 템플릿을 사용하여 IoT Hub, 스토리지 계정, 경로 메시지 게시
description: ARM 템플릿을 사용하여 IoT Hub, 스토리지 계정, 경로 메시지 게시
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: robinsh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: fd2742744ef4f2f616e772c8f387887e113b132a
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88861742"
---
# <a name="quickstart-deploy-an-azure-iot-hub-and-a-storage-account-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure IoT Hub 및 스토리지 계정 배포

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 메시지를 Azure Storage로 라우팅하는 IoT Hub 및 메시지를 보관할 스토리지 계정을 만듭니다. 메시지를 전송할 가상 IoT 디바이스가 허브에 수동으로 추가되면 메시지를 이 디바이스에서 허브로 전송하도록 *arm-read-write*라는 애플리케이션에서 해당 연결 정보를 구성합니다. 그러면 허브로 보낸 메시지가 스토리지 계정으로 자동으로 라우팅되도록 허브가 구성됩니다. 이 빠른 시작이 완료되면 스토리지 계정을 열어 보낸 메시지를 확인할 수 있습니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-iothub-auto-route-messages)에서 `101-iothub-auto-route-messages`라고 합니다.

:::code language="json" source="~/quickstart-templates/101-iothub-auto-route-messages/azuredeploy.json":::

템플릿에는 두 개의 Azure 리소스가 정의되어 있습니다. 
* [Microsoft.Devices/Iothubs](/azure/templates/microsoft.iothubs)
* [Microsoft.Storage/](/azure/templates/microsoft.storage)

## <a name="deploy-the-template-and-run-the-sample-app"></a>템플릿 배포 및 샘플 앱 실행

이 섹션에서는 템플릿을 배포하고, 가상 디바이스를 만들고, arm-read-write 애플리케이션을 실행하여 메시지를 보내는 단계를 제공합니다.

1. ARM 템플릿을 배포하여 리소스를 만듭니다.

    > [!TIP]
    > 템플릿 배포를 시작하려면 아래 단추를 선택합니다. 실행되는 동안 arm-read-write 애플리케이션을 실행하도록 설정합니다.

    [![Azure에 배포](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

1. [IoT C# 샘플](https://Azure-Samples/azure-iot-samples-csharp)을 다운로드하고 압축을 풉니다.

1. 명령 창을 열고, IoT C# 샘플의 압축을 푼 폴더로 이동합니다. arm-read-write.csproj 파일이 있는 폴더를 찾습니다. 이 명령 창에서 환경 변수를 만듭니다. [Azure Portal](https://portal.azure.com )에 로그인하여 키를 가져옵니다. **리소스 그룹**을 선택한 다음, 이 빠른 시작에서 사용되는 리소스 그룹을 선택합니다.

   ![리소스 그룹 선택](./media/horizontal-arm-route-messages/01-select-resource-group.png)

1. ARM 템플릿을 배포할 때 만든 IoT Hub 및 스토리지 계정이 표시됩니다. 템플릿이 완전히 배포될 때까지 기다린 후 계속합니다. 그런 다음, 리소스 그룹을 선택하여 리소스를 확인합니다.

   ![리소스 그룹의 리소스 보기](./media/horizontal-arm-route-messages/02-view-resources-in-group.png)

1. **허브 이름**이 필요합니다. 리소스 목록에서 허브를 선택합니다. IoT Hub 섹션의 위쪽에 있는 허브 이름을 Windows 클립보드에 복사합니다. 
 
   ![허브 이름 복사](./media/horizontal-arm-route-messages/03-copy-hub-name.png)

    이 명령에서 표시된 허브 이름으로 바꾸고, 명령 창에서 다음 명령을 실행합니다.
   
    ```cmd
    SET IOT_HUB_URI=<hub name goes here>.azure-devices-net;
    ```

   이 명령의 예제는 다음과 같습니다.

   ```cmd
   SET IOT_HUB_URI=ContosoTestHubdlxlud5h.azure-devices-net;
   ```

1. 다음 환경 변수는 IoT 디바이스 키입니다. 허브에 대한 IoT Hub 메뉴에서 **IoT 디바이스**를 선택하여 새 디바이스를 허브에 추가합니다. 

   ![IoT 디바이스 선택](./media/horizontal-arm-route-messages/04-select-iot-devices.png)

1. 화면 오른쪽에서 **+ 새로 만들기**를 선택하여 새 디바이스를 추가합니다. 

   새 디바이스 이름을 입력합니다. 이 빠른 시작에서는 **Contoso-Test-Device**로 시작하는 이름을 사용합니다. 디바이스를 저장한 다음, 해당 화면을 다시 열어 디바이스 키를 검색합니다. (키는 창을 닫을 때 생성됩니다.) 기본 키 또는 보조 키를 선택하고, Windows 클립보드에 복사합니다. 명령 창에서 실행할 명령을 설정한 다음, **Enter** 키를 누릅니다. 이 명령은 다음과 같지만 디바이스 키가 붙여넣어집니다.

   ```cmd
   SET IOT_DEVICE_KEY=<device-key-goes-here>
   ```

1. 마지막 환경 변수는 **디바이스 ID**입니다. 명령 창에서 명령을 설정하고 실행합니다. 
   
   ```cms
   SET IOT_DEVICE_ID=<device-id-goes-here> 
   ```

   이 명령의 예제는 다음과 같습니다.

   ```cmd
   SET IOT_DEVICE_ID=Contoso-Test-Device
   ```

1. 정의한 환경 변수를 보려면 명령줄에서 SET을 입력하고, **Enter** 키를 누른 다음, **IoT**로 시작하는 변수를 찾습니다.

   ![환경 변수 참조](./media/horizontal-arm-route-messages/06-environment-variables.png)

이제 환경 변수가 설정되었으므로 동일한 명령 창에서 애플리케이션을 실행합니다. 동일한 창을 사용하고 있으므로 애플리케이션을 실행할 때 메모리에서 변수에 액세스할 수 있습니다.

1. 애플리케이션을 실행하려면 명령 창에서 다음 명령을 입력하고, **Enter** 키를 누릅니다.

    `dotnet run arm-read-write`

   애플리케이션에서 각 메시지를 IoT 허브에 보낼 때 메시지를 생성하여 콘솔에 표시합니다. 허브는 ARM 템플릿에서 자동으로 라우팅되도록 구성되었습니다. "level = storage"라는 텍스트가 포함된 메시지는 자동으로 스토리지 계정으로 라우팅됩니다. 앱이 10~15분 동안 실행되도록 한 다음, 실행이 중지될 때까지 **Enter** 키를 한두 번 누릅니다.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

1. [Azure Portal](https://portal.azure.com)에 로그인하고, 리소스 그룹을 선택한 다음, 스토리지 계정을 선택합니다.

1. 파일을 찾을 때까지 스토리지 계정을 드릴다운합니다.

   ![스토리지 계정 파일 보기](./media/horizontal-arm-route-messages/07-see-storage.png)

1. 파일 중 하나를 선택하고, **다운로드**를 선택하여 파일을 나중에 찾을 수 있는 위치에 다운로드합니다. 47과 같이 숫자로 된 이름을 사용합니다. ".txt"를 끝에 추가한 다음, 파일을 두 번 클릭하여 엽니다.

1. 파일이 열리면 각 행은 서로 다른 메시지에 대한 것이며, 각 메시지의 본문도 암호화되어 있습니다. 이는 메시지 본문에 대한 쿼리를 수행하는 데 반드시 필요한 것입니다.

   ![보낸 메시지 보기](./media/horizontal-arm-route-messages/08-messages.png)

   > [!NOTE]
   > 이러한 메시지는 UTF-32 및 base64로 인코딩됩니다. 메시지를 다시 읽는 경우 ASCII로 읽으려면 base64 및 utf-32에서 디코딩해야 합니다. 관심이 있는 경우 라우팅 자습서의 ReadOneRowFromFile 메서드를 사용하여 이러한 메시지 파일 중 하나에서 한 항목을 읽어 ASCII로 디코딩할 수 있습니다. ReadOneRowFromFile은 이 빠른 시작을 위해 압축을 푼 IoT C# 샘플 리포지토리에 있습니다. 해당 폴더 위쪽의 경로는 *./iot-hub/Tutorials/Routing/SimulatedDevice/Program.cs*입니다. `readTheFile` 부울을 true로 설정하고 디스크의 파일에 대한 경로를 하드코딩하면 파일의 첫 번째 행이 열리고 변환됩니다.

ARM 템플릿을 배포하여 IoT Hub 및 스토리지 계정을 만들고, 프로그램을 실행하여 메시지를 허브에 보내는 프로그램을 실행했습니다. 그런 다음, 메시지는 볼 수 있는 스토리지 계정에 자동으로 저장되었습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 추가된 리소스를 제거하려면 [Azure Portal](https://portal.azure.com)에 로그인합니다. **리소스 그룹**을 선택한 다음, 이 빠른 시작에서 사용한 리소스 그룹을 찾습니다. 리소스 그룹을 선택한 다음, *삭제*를 선택합니다. 그러면 그룹의 모든 리소스가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: 첫 번째 ARM 템플릿 만들기 및 배포](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)
