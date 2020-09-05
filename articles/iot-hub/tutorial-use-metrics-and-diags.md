---
title: Azure IoT Hub를 사용하여 메트릭 및 진단 로그 설정 및 사용
description: Azure IoT 허브를 사용하여 메트릭 및 진단 로그를 설정하고 사용하는 방법을 알아봅니다. 이렇게 하면 허브에서 발생할 수 있는 문제를 진단하는 데 도움이 되는 분석 데이터를 제공합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 3/13/2019
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: b24d9e1cbbcf875d7b4bde3981b28d8999ba8d47
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019133"
---
# <a name="tutorial-set-up-and-use-metrics-and-diagnostic-logs-with-an-iot-hub"></a>자습서: IoT Hub를 사용하여 메트릭 및 진단 로그 설정 및 사용

프로덕션 환경에서 IoT Hub 솔루션을 실행 중인 경우 몇 가지 메트릭을 설정하고 진단 로그를 사용하도록 설정할 수 있습니다. 그러면 문제가 발생하는 경우 문제를 진단하고 더 빠르게 해결하는 데 도움이 되는 데이터를 살펴볼 수 있습니다. 이 문서에서는 진단 로그를 사용하도록 설정하는 방법 및 오류에 대해 확인하는 방법을 알아봅니다. 또한 관찰할 일부 메트릭 및 메트릭이 특정 경계에 도달할 때 발생하는 경고를 설정하게 됩니다. 예를 들어, 전송된 원격 분석 메시지 수가 특정 경계를 초과하는 경우 또는 사용된 메시지 수가 IoT Hub에 대해 일별 허용된 메시지 할당량에 근접하는 경우 이메일이 전송되도록 할 수 있습니다. 

펌프가 IoT Hub와의 통신을 전송하는 IoT 디바이스인 주유소가 그 사용 사례입니다. 신용 카드의 유효성을 검사하며 마지막 트랜잭션이 데이터 저장소에 기록됩니다. IoT 디바이스가 허브 연결과 메시지 전송을 중지하는 경우 상황을 제대로 파악하지 못하면 해결이 훨씬 더 어렵습니다.

이 자습서에서는 [IoT Hub 라우팅](tutorial-routing.md)의 Azure 샘플을 사용하여 IoT Hub에 메시지를 보냅니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * Azure CLI 사용, IoT Hub, 시뮬레이션된 디바이스 및 스토리지 계정 만들기  
> * 진단 로그 활성화
> * 메트릭 사용
> * 이러한 메트릭에 대한 경고 설정 
> * 허브로 메시지를 전송하는 IoT 디바이스를 시뮬레이션하는 앱을 다운로드하여 실행 
> * 경고가 발생될 때까지 앱 실행 
> * 메트릭 결과를 보고 진단 로그를 확인합니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- [Visual Studio](https://www.visualstudio.com/)를 설치합니다. 

- 이메일을 받을 수 있는 이메일 계정입니다.

- 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 자습서의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-resources"></a>리소스 설정

이 자습서에서는 IoT Hub, 스토리지 계정 및 시뮬레이션된 IoT 디바이스가 필요합니다. 이러한 리소스는 Azure CLI 또는 Azure PowerShell을 사용하여 만들 수 있습니다. 모든 리소스에 대해 동일한 리소스 그룹 및 위치를 사용합니다. 그런 다음, 마지막에 리소스 그룹을 삭제하면 모든 내용을 한 번에 삭제할 수 있습니다.

다음과 같은 단계가 필요합니다.

1. [리소스 그룹](../azure-resource-manager/management/overview.md)을 만듭니다. 

2. IoT Hub를 만듭니다.

3. Standard_LRS 복제본을 사용하여 표준 V1 스토리지 계정을 만듭니다.

4. 허브에 메시지를 보내는 시뮬레이션된 디바이스에 사용할 디바이스 ID를 만듭니다. 테스트 단계에 대한 키를 저장합니다.

### <a name="set-up-resources-using-azure-cli"></a>Azure CLI를 사용하여 리소스 설정

이 스크립트를 복사하여 Cloud Shell에 붙여넣습니다. 이미 로그인했다고 가정하면 한 번에 한 줄씩 스크립트가 실행됩니다. 새 리소스가 리소스 그룹 ContosoResources에서 생성됩니다.

전역적으로 고유해야 하는 변수에는 `$RANDOM`이 연결되어 있습니다. 스크립트가 실행되고 변수가 설정되면 임의의 수치 문자열이 생성되고 고정 문자열 마지막에 연결되어 이것이 고유하게 됩니다.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity. 
az extension add --name azure-iot

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device 

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$RANDOM
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku F1 --location $location

# The storage account name must be globally unique, so add a random number to the end.
storageAccountName=contosostoragemon$RANDOM
echo "Storage account name = " $storageAccountName

# Create the storage account.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName 

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>디바이스 ID를 만들 때 다음 오류가 표시될 수도 있습니다. *IoT Hub ContosoTestHub의 iothubowner 정책에 대한 키를 찾을 수 없습니다*. 이 오류를 해결하려면 Azure CLI IoT 확장을 업데이트한 다음, 마지막 두 명령을 스크립트에서 다시 실행합니다. 
>
>확장을 업데이트하는 명령은 다음과 같습니다. 이를 Cloud Shell 인스턴스에서 실행합니다.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="enable-the-diagnostic-logs"></a>진단 로그 활성화 

[진단 로그](../azure-monitor/platform/platform-logs-overview.md)는 새 IoT Hub를 만들 때 기본적으로 비활성화됩니다. 이 섹션에서는 허브에 대한 진단 로그를 사용하도록 설정합니다.

1. 첫째, 아직 포털의 허브에서 수행하지 않은 경우 **리소스 그룹**을 클릭하고 Contoso 리소스의 리소스 그룹을 클릭합니다. 표시되는 리소스 목록에서 허브를 선택합니다. 

2. IoT Hub 블레이드에서 **모니터링** 섹션을 찾습니다. **진단 설정**을 클릭합니다. 

   ![IoT Hub 블레이드의 진단 설정 부분을 보여주는 스크린샷.](./media/tutorial-use-metrics-and-diags/01-diagnostic-settings.png)


3. 구독 및 리소스 그룹이 올바른지 확인합니다. **리소스 형식**에서 **모두 선택**을 선택 취소한 다음, **IoT Hub**를 찾아서 확인합니다. (*모두 선택* 옆에 확인 표시가 다시 생깁니다. 무시하면 됩니다.) **리소스**에서 허브 이름을 선택합니다. 화면이 다음과 같이 표시됩니다. 

   ![IoT Hub 블레이드의 진단 설정 부분을 보여주는 스크린샷.](./media/tutorial-use-metrics-and-diags/02-diagnostic-settings-start.png)

4. 이제 **진단 켜기**를 클릭합니다. 진단 설정 창이 표시됩니다. 진단 로그 설정의 이름을 “진단 허브”로 지정합니다.

5. **스토리지 계정에 보관**을 선택합니다. 

   ![스토리지 계정에 보관하는 진단 설정을 보여주는 스크린샷.](./media/tutorial-use-metrics-and-diags/03-diagnostic-settings-storage.png)

    **구성**을 클릭하여 **스토리지 계정 선택** 화면을 표시하고, 정확한 계정(*contosostoragemon*)을 선택한 후 **확인**을 클릭하여 진단 설정 창으로 돌아갑니다. 

   ![스토리지 계정에 보관하는 진단 로그 설정을 보여주는 스크린샷.](./media/tutorial-use-metrics-and-diags/04-diagnostic-settings-after-storage.png)

6. **로그**에서 **연결** 및 **디바이스 원격 분석**을 확인하고 **보존(일)** 을 각각 7일로 설정합니다. 진단 설정 화면은 다음 이미지와 같이 표시됩니다.

   ![최종 진단 로그 설정을 보여주는 스크린샷.](./media/tutorial-use-metrics-and-diags/05-diagnostic-settings-done.png)

7. **저장**을 클릭하여 설정을 저장합니다. 진단 설정 창을 닫습니다.

나중에 진단 로그를 볼 때 연결을 확인하고 디바이스에 대한 로깅 연결을 끊을 수 있습니다. 

## <a name="set-up-metrics"></a>메트릭 설정 

이제 허브에 메시지를 보낼 때 관찰하도록 몇 가지 메트릭을 설정합니다. 

1. IoT Hub에 대한 설정 창에서 **모니터링** 섹션의 **메트릭** 옵션을 클릭합니다.

2. 화면 맨 위에서 **지난 24시간(자동)** 을 클릭합니다. 표시되는 드롭다운 목록에서 **마지막 4시간**을 **시간 범위**에서 선택하고 **시간 단위**를 **1분**, 현지 시간으로 설정합니다. **적용**을 클릭하여 이러한 설정을 저장합니다. 

   ![메트릭 시간 설정을 보여주는 스크린샷.](./media/tutorial-use-metrics-and-diags/06-metrics-set-time-range.png)

3. 기본적으로 하나의 메트릭 항목이 있습니다. 리소스 그룹 및 메트릭 네임스페이스를 기본값으로 둡니다. **메트릭** 드롭다운 목록에서 **전송된 원격 분석 메시지**를 선택합니다. **집계**를 **합계**로 설정합니다.

   ![전송된 원격 분석 메시지에 대한 메트릭 추가를 보여주는 스크린샷.](./media/tutorial-use-metrics-and-diags/07-metrics-telemetry-messages-sent.png)


4. 이제 **메트릭 추가**를 클릭하여 다른 메트릭을 차트에 추가합니다. 리소스 그룹(**ContosoTestHub**)을 선택합니다. **메트릭**에서 **사용된 전체 메시지 수**를 선택합니다. **집계**에서 **평균**을 선택합니다. 

   이제 화면에 *전송된 원격 분석 메시지*에 대한 최소화된 메트릭과 함께 *사용된 총 메시지 수*에 대해 새 메트릭이 표시됩니다.

   ![전송된 원격 분석 메시지에 대한 메트릭 추가를 보여주는 스크린샷.](./media/tutorial-use-metrics-and-diags/07-metrics-num-messages-used.png)

   **대시보드에 고정**을 클릭합니다. 다시 액세스할 수 있도록 Azure Portal의 대시보드에서 고정됩니다. 대시보드에 고정하지 않으면 설정이 유지되지 않습니다.

## <a name="set-up-alerts"></a>경고 설정

포털에서 허브로 이동합니다. **리소스 그룹**을 클릭하고 *ContosoResources*를 선택한 다음, IoT Hub *ContosoTestHub*를 선택합니다. 

IoT Hub가 [Azure Monitor의 메트릭](/azure/azure-monitor/platform/data-collection#metrics)에 마이그레이션되지 않은 경우 [클래식 경고](/azure/azure-monitor/platform/alerts-classic.overview)를 사용해야 합니다.

1. **모니터링**에서 **경고**를 클릭합니다. 그러면 기본 경고 화면이 표시됩니다. 

   ![클래식 경고를 찾는 방법을 보여주는 스크린샷.](./media/tutorial-use-metrics-and-diags/08-find-classic-alerts.png)

2. 여기서 클래식 경고를 가져오려면 **클래식 경고 보기**를 클릭합니다. 

    ![클래식 경고 화면을 보여주는 스크린샷.](./media/tutorial-use-metrics-and-diags/09-view-classic-alerts.png)

    다음 필드를 입력합니다. 

    **구독**: 현재 구독으로 설정된 이 필드를 그대로 둡니다.

    **원본**: 이 필드를 *메트릭*으로 설정합니다.

    **리소스 그룹**: 이 필드를 현재 리소스 그룹 *ContosoResources*로 설정합니다. 

    **리소스 종류**: 이 필드를 IoT Hub로 설정합니다. 

    **리소스**: IoT Hub *ContosoTestHub*를 선택합니다.

3. **메트릭 경고 추가(클래식)** 를 클릭하여 새 경고를 설정합니다.

    다음 필드를 입력합니다.

    **Name**: *telemetry-messages*와 같은 경고 규칙의 이름을 입력합니다.

    **설명**: *1000개의 원격 분석 메시지를 보낸 경우 경고*와 같이 경고에 대한 설명을 입력합니다. 

    **원본**: 이를 *메트릭*으로 설정합니다.

    **구독**, **리소스 그룹** 및 **리소스**는 **클래식 경고 보기** 화면에서 선택한 값으로 설정되어야 합니다. 

    **메트릭**을 *전송된 원격 분석 메시지*로 설정합니다.

    ![전송된 원격 분석 메시지에 대한 클래식 경고 설정을 보여주는 스크린샷.](./media/tutorial-use-metrics-and-diags/10-alerts-add-rule-telemetry-top.png)

4. 차트 아래에 다음 필드를 설정합니다.

   **조건**: *다음보다 큼*으로 설정합니다.

   **임계값**: 1000으로 설정합니다.

   **기간**: *지난 5분 동안*으로 설정합니다.

   **알림 이메일 받는 사람**: 이메일 주소를 여기에 입력합니다. 

   ![경고 화면의 절반 아래쪽을 보여주는 스크린샷.](./media/tutorial-use-metrics-and-diags/11-alerts-add-rule-bottom.png)

   **확인**을 클릭하여 경고를 저장합니다. 

5. 이제 *사용된 전체 메시지 수*에 대한 다른 경고를 설정합니다. 이 메트릭은 사용된 메시지 수가 IoT Hub의 할당량에 도달할 때 경고를 보내려는 경우에 유용하며, 허브가 곧 메시지를 거부하기 시작할 것임을 알 수 있습니다.

   **클래식 경고 보기** 화면에서 **메트릭 경고 추가(클래식)** 를 클릭한 다음, **규칙 추가** 창에서 이러한 필드를 입력합니다.

   **Name**: *number-of-messages-used*와 같은 경고 규칙의 이름을 입력합니다.

   **설명**: *할당량에 근접할 때 경고*와 같이 경고에 대한 설명을 입력합니다.

   **원본**: 이 필드를 *메트릭*으로 설정합니다.

    **구독**, **리소스 그룹** 및 **리소스**는 **클래식 경고 보기** 화면에서 선택한 값으로 설정되어야 합니다. 

    **메트릭**을 *사용된 전체 메시지 수*로 설정합니다.

6. 차트 아래에서 다음 필드를 입력합니다.

   **조건**: *다음보다 큼*으로 설정합니다.

   **임계값**: 1000으로 설정합니다.

   **기간**: 이 필드를 *지난 5분 동안*으로 설정합니다. 

   **알림 이메일 받는 사람**: 이메일 주소를 여기에 입력합니다. 

   **확인**을 클릭하여 규칙을 저장합니다. 

5. 이제 클래식 경고 창에 두 가지 경고가 표시됩니다. 

   ![새 경고 규칙을 사용한 클래식 경고 화면을 보여주는 스크린샷.](./media/tutorial-use-metrics-and-diags/12-alerts-done.png)

6. 경고 창을 닫습니다. 
    
    이러한 설정을 사용하면 전송된 메시지 수가 400건을 넘고 사용된 전체 메시지 수가 해당 수를 초과할 때 경고가 표시됩니다.

## <a name="run-simulated-device-app"></a>시뮬레이션된 디바이스 앱 실행

앞서 스크립트 설정 섹션에서 IoT 디바이스를 사용하여 시뮬레이션하도록 디바이스를 설정했습니다. 이 섹션에서는 IoT Hub로 디바이스-클라우드 메시지를 전송하는 디바이스를 시뮬레이션하는 .NET 콘솔 앱을 다운로드합니다.  

[IoT 디바이스 시뮬레이션](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)에 대한 솔루션을 다운로드합니다. 이 링크는 여러 애플리케이션을 사용한 리포지토리를 다운로드합니다. 원하는 솔루션은 iot-hub/Tutorials/Routing/에 있습니다.

솔루션 파일(SimulatedDevice.sln)을 두 번 클릭하여 Visual Studio에서 코드를 연 다음, Program.cs를 엽니다. `{iot hub hostname}`을 IoT Hub 호스트 이름으로 대체합니다. IoT Hub 호스트 이름의 형식은 **{iot-hub-name}.azure-devices.net**입니다. 이 자습서의 경우 허브 호스트 이름은 **ContosoTestHub.azure-devices.net**입니다. 다음으로, `{device key}`를 이전에 시뮬레이션된 디바이스를 설정할 때 저장했던 디바이스 키로 대체합니다. 

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHub.azure-devices.net";
        // This is the primary key for the device. This is in the portal. 
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key. 
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>실행 및 테스트 

Program.cs에서 `Task.Delay`를 1000에서 10으로 변경합니다. 그러면 전송되는 메시지 간 시간이 1초에서 0.01초로 단축됩니다. 이러한 지연 시간을 단축하면 전송된 메시지 수가 증가합니다.

```csharp
await Task.Delay(10);
```

콘솔 애플리케이션을 실행합니다. 잠시 기다립니다(10-15분). 애플리케이션의 콘솔 화면에 시뮬레이션된 디바이스에서 허브로 전송되는 메시지를 볼 수 있습니다.

### <a name="see-the-metrics-in-the-portal"></a>포털에서 메트릭 확인

대시보드에서 메트릭을 엽니다. 시간 단위를 *1분*으로 하고 시간 값을 *지난 30분*으로 변경합니다. 차트의 아래쪽에 최신 숫자와 함께 전송된 원격 분석 메시지와 사용된 전체 메시지 수가 차트에 표시됩니다.

   ![메트릭을 보여주는 스크린샷.](./media/tutorial-use-metrics-and-diags/13-metrics-populated.png)

### <a name="see-the-alerts"></a>경고 보기

경고로 돌아갑니다. **리소스 그룹**을 클릭하고 *ContosoResources*를 선택한 다음, Hub *ContosoTestHub*를 선택합니다. 허브에 대해 표시된 속성 페이지에서 **경고**를 선택한 다음, **클래식 경고 보기**를 선택합니다. 

전송된 메시지 수가 제한을 초과하는 경우 이메일 경고가 수신되기 시작합니다. 모든 활성 경고가 있는지 확인하려면 허브로 이동하고 **경고**를 선택합니다. 경고가 있는 경우 활성 상태인 경고가 표시됩니다. 

   ![경고가 발생하였음을 보여주는 스크린샷.](./media/tutorial-use-metrics-and-diags/14-alerts-firing.png)

원격 분석 메시지에 대한 경고를 클릭합니다. 메트릭 결과 및 결과가 포함된 차트가 표시됩니다. 또한 발생한 경고를 알리는 전송된 이메일은 다음 이미지와 같습니다.

   ![경고가 발생하였음을 보여주는 이메일의 스크린샷.](./media/tutorial-use-metrics-and-diags/15-alert-email.png)

### <a name="see-the-diagnostic-logs"></a>진단 로그 보기

진단 로그를 Blob Storage로 내보내도록 설정합니다. 리소스 그룹으로 이동하고 스토리지 계정 *contosostoragemon*을 선택합니다. Blob을 선택한 다음, 컨테이너 *insights-logs-connections*를 엽니다. 현재 날짜까지 드릴다운하고 가장 최근 파일을 선택합니다. 

   ![진단 로그를 보기 위해 스토리지 컨테이너로 드릴다운하는 스크린샷.](./media/tutorial-use-metrics-and-diags/16-diagnostics-logs-list.png)

**다운로드**를 클릭하여 다운로드하고 엽니다. 허브로 메시지를 보낼 때 연결 및 연결 해제 디바이스의 로그를 확인합니다. 샘플은 다음과 같습니다.

``` json
{ 
  "time": "2018-12-17T18:11:25Z", 
  "resourceId": 
    "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
  "operationName": "deviceConnect", 
  "category": "Connections", 
  "level": "Information", 
  "properties": 
      {"deviceId":"Contoso-Test-Device",
       "protocol":"Mqtt",
       "authType":null,
       "maskedIpAddress":"73.162.215.XXX",
       "statusCode":null
       }, 
  "location": "westus"
}
{ 
   "time": "2018-12-17T18:19:25Z", 
   "resourceId": 
     "/SUBSCRIPTIONS/your-subscription-id/RESOURCEGROUPS/CONTOSORESOURCES/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/CONTOSOTESTHUB", 
    "operationName": "deviceDisconnect", 
    "category": "Connections", 
    "level": "Error", 
    "resultType": "404104", 
    "resultDescription": "DeviceConnectionClosedRemotely", 
    "properties": 
        {"deviceId":"Contoso-Test-Device",
         "protocol":"Mqtt",
         "authType":null,
         "maskedIpAddress":"73.162.215.XXX",
         "statusCode":"404"
         }, 
    "location": "westus"
}
```

## <a name="clean-up-resources"></a>리소스 정리 

이 자습서에서 만든 리소스를 모두 제거하려면 리소스 그룹을 삭제합니다. 이렇게 하면 그룹 내에 포함된 모든 리소스가 삭제됩니다. 이 경우 IoT Hub, 스토리지 계정 및 리소스 그룹 자체가 제거됩니다. 메트릭을 대시보드에 고정한 경우에는 각각의 오른쪽 위 모서리에 있는 세 점을 클릭하고 **제거**를 선택하여 수동으로 제거해야 합니다.

리소스 그룹을 제거하려면 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 명령을 사용합니다.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업을 수행하여 메트릭 및 진단 로그를 사용하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure CLI 사용, IoT Hub, 시뮬레이션된 디바이스 및 스토리지 계정 만들기  
> * 진단 로그 활성화 
> * 메트릭 사용
> * 이러한 메트릭에 대한 경고 설정 
> * 허브로 메시지를 전송하는 IoT 디바이스를 시뮬레이션하는 앱을 다운로드하여 실행 
> * 경고가 발생될 때까지 앱 실행 
> * 메트릭 결과를 보고 진단 로그를 확인합니다. 

IoT 디바이스의 상태를 관리하는 방법에 대해 알아보려면 다음 자습서로 이동합니다. 

> [!div class="nextstepaction"]
> [백 엔드 서비스에서 디바이스 구성](tutorial-device-twins.md)
