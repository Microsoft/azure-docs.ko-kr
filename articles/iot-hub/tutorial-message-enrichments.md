---
title: 자습서-Azure IoT Hub 메시지 강화 사용
description: Azure IoT Hub 메시지에 대해 메시지 강화를 사용 하는 방법을 보여 주는 자습서
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 1e04ed8fe9087cb06b1533887da3c0fed61259ef
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019728"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>자습서: Azure IoT Hub 메시지 강화 사용

*메시지 강화* 메시지를 지정 된 끝점으로 보내기 전에 추가 정보를 사용 하 여 메시지를 *스탬프* Azure IoT Hub 기능을 설명 합니다. 메시지 강화를 사용 하는 한 가지 이유는 다운스트림 처리를 간소화 하는 데 사용할 수 있는 데이터를 포함 하는 것입니다. 예를 들어 장치 쌍 태그를 사용 하는 장치 원격 분석 메시지를 보강 고객에 대 한 부하를 줄여이 정보에 대 한 장치 쌍 API 호출을 수행할 수 있습니다. 자세한 내용은 [메시지 강화 개요](iot-hub-message-enrichments-overview.md)를 참조 하세요.

이 자습서에서는 IoT hub에 대 한 메시지 강화를 테스트 하는 데 필요한 리소스를 만들고 구성 하는 두 가지 방법을 볼 수 있습니다. 리소스에는 두 개의 저장소 컨테이너를 포함 하는 하나의 저장소 계정이 포함 되어 있습니다. 한 컨테이너는 보강 메시지를 보유 하 고 다른 컨테이너는 원래 메시지를 보유 합니다. 또한 메시지를 수신 하 고 보강 여부에 따라 적절 한 저장소 컨테이너에 라우팅하는 IoT 허브가 포함 되어 있습니다.

* 첫 번째 방법은 Azure CLI를 사용 하 여 리소스를 만들고 메시지 라우팅을 구성 하는 것입니다. 그런 다음 [Azure Portal](https://portal.azure.com)를 사용 하 여 강화를 수동으로 정의 합니다.

* 두 번째 방법은 Azure Resource Manager 템플릿을 사용 하 여 메시지 라우팅 및 메시지 강화에 대 한 리소스 *와* 구성을 모두 만드는 것입니다.

메시지 라우팅 및 메시지 강화에 대 한 구성을 완료 한 후 응용 프로그램을 사용 하 여 IoT hub에 메시지를 보냅니다. 그런 다음 허브는 두 저장소 컨테이너에 모두 라우팅합니다. **보강** 저장소 컨테이너의 끝점에 전송 된 메시지만 보강 됩니다.

이 자습서를 완료 하기 위해 수행 하는 작업은 다음과 같습니다.

**IoT Hub 메시지 강화 사용**
> [!div class="checklist"]
> * 첫 번째 방법: Azure CLI를 사용 하 여 리소스를 만들고 메시지 라우팅을 구성 합니다. [Azure Portal](https://portal.azure.com)를 사용 하 여 수동으로 메시지 강화를 구성 합니다.
> * 두 번째 방법: 리소스 관리자 템플릿을 사용 하 여 리소스를 만들고 메시지 라우팅 및 메시지 강화를 구성 합니다. 
> * 허브로 메시지를 보내는 IoT 장치를 시뮬레이트하는 앱을 실행 합니다.
> * 결과를 확인 하 고 강화 메시지가 정상적으로 작동 하는지 확인 합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 있어야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* [Visual Studio](https://www.visualstudio.com/)를 설치합니다.

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 자습서의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>IoT c # 샘플 리포지토리 검색

GitHub에서 [IoT c # 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) 을 다운로드 하 고 압축을 풉니다. 이 리포지토리에는 여러 응용 프로그램, 스크립트 및 리소스 관리자 템플릿이 있습니다. 이 자습서에 사용할 수 있는 항목은 다음과 같습니다.

* 수동 방법의 경우 리소스를 만드는 데 사용 되는 CLI 스크립트가 있습니다. 이 스크립트는/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli. azcli에 있습니다. 이 스크립트는 리소스를 만들고 메시지 라우팅을 구성 합니다. 이 스크립트를 실행 한 후 [Azure Portal](https://portal.azure.com)를 사용 하 여 강화 메시지를 수동으로 만듭니다.
* 자동화 된 방법의 경우 Azure Resource Manager 템플릿이 있습니다. 템플릿은/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json입니다. 이 템플릿은 리소스를 만들고 메시지 라우팅을 구성한 다음 강화 메시지를 구성 합니다.
* 사용 하는 세 번째 응용 프로그램은 IoT hub에 메시지를 보내고 강화 메시지를 테스트 하는 데 사용 하는 장치 시뮬레이션 앱입니다.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Azure CLI를 사용 하 여 수동으로 설정 및 구성

Azure CLI 스크립트는 필요한 리소스를 만드는 것 외에도 별도의 저장소 컨테이너인 끝점에 대 한 두 개의 경로도 구성 합니다. 메시지 라우팅을 구성 하는 방법에 대 한 자세한 내용은 [라우팅 자습서](tutorial-routing.md)를 참조 하십시오. 리소스를 설정한 후에는 [Azure Portal](https://portal.azure.com) 를 사용 하 여 각 끝점에 대 한 메시지 강화 구성 합니다. 그런 다음 테스트 단계를 계속 진행 합니다.

> [!NOTE]
> 모든 메시지는 두 끝점으로 모두 라우팅되고 구성 된 메시지 강화를 사용 하 여 끝점으로 이동 하는 메시지만 보강 됩니다.
>

다음 스크립트를 사용 하거나 다운로드 한 리포지토리의/resources 폴더에 있는 스크립트를 열 수 있습니다. 스크립트는 다음 단계를 수행합니다.

* IoT Hub를 만듭니다.
* 스토리지 계정을 만듭니다.
* 저장소 계정에 두 개의 컨테이너를 만듭니다. 한 컨테이너는 보강 메시지를 위한 것이 고 다른 하나는 보강 않은 메시지에 대 한 컨테이너입니다.
* 두 개의 서로 다른 저장소 계정에 대 한 라우팅을 설정 합니다.
    * 각 저장소 계정 컨테이너에 대 한 끝점을 만듭니다.
    * 각 저장소 계정 컨테이너 끝점에 대 한 경로를 만듭니다.

IoT hub 이름 및 저장소 계정 이름과 같이 전역적으로 고유 해야 하는 여러 리소스 이름이 있습니다. 스크립트를 더 쉽게 실행 하기 위해 이러한 리소스 이름에는 *randomValue*라는 임의의 영숫자 값이 추가 됩니다. 난수 값은 스크립트의 맨 위에 한 번 생성 됩니다. 스크립트 전체에서 필요에 따라 리소스 이름에 추가 됩니다. 값을 무작위로 지정 하지 않으려면 빈 문자열이 나 특정 값으로 설정할 수 있습니다.

아직 수행 하지 않은 경우 Azure [Cloud Shell 창을](https://shell.azure.com) 열고 Bash로 설정 되었는지 확인 합니다. 압축을 푼 리포지토리에서 스크립트를 열고 Ctrl + A를 선택 하 여 모두 선택한 다음 Ctrl + C를 선택 하 여 복사 합니다. 또는 다음 CLI 스크립트를 복사 하거나 Cloud Shell에서 직접 열 수 있습니다. 명령줄을 마우스 오른쪽 단추로 클릭 하 고 **붙여넣기**를 선택 하 여 Cloud Shell 창에 스크립트를 붙여 넣습니다. 스크립트는 한 번에 하나의 문을 실행 합니다. 스크립트가 실행을 중지 한 후 **Enter 키** 를 선택 하 여 마지막 명령이 실행 되는지 확인 합니다. 다음 코드 블록은 사용 하는 스크립트를 보여 주는 설명과 함께 수행 되는 작업을 설명 합니다.

스크립트에서 만든 리소스는 다음과 같습니다. *보강* 는 강화가 포함 된 메시지에 대 한 리소스 임을 의미 합니다. *원래* 는 보강 않은 메시지에 대 한 리소스 임을 의미 합니다.

| Name | 값 |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| 컨테이너 이름 | 오리지널  |
| 컨테이너 이름 | 보강  |
| IoT 장치 이름 | Contoso-테스트-장치 |
| IoT Hub 이름 | ContosoTestHubMsgEn |
| 저장소 계정 이름 | : contosostorage |
| 끝점 이름 1 | ContosoStorageEndpointOriginal |
| 끝점 이름 2 | ContosoStorageEndpointEnriched|
| 경로 이름 1 | ContosoStorageRouteOriginal |
| 경로 이름 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that
#   don't have to be globally unique.
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
# The storage account name must be globally unique,
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key.
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for the first container, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for the second container, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

이 시점에서 리소스가 모두 설정 되 고 메시지 라우팅이 구성 됩니다. 포털에서 메시지 라우팅 구성을 확인 하 고 **보강** 저장소 컨테이너로 이동 하는 메시지에 대 한 메시지 강화를 설정할 수 있습니다.

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 수동으로 메시지 강화 구성

1. **리소스 그룹**을 선택 하 여 IoT hub로 이동 합니다. 그런 다음이 자습서에 대해 설정 된 리소스 그룹을 선택 합니다 (**ContosoResourcesMsgEn**). 목록에서 IoT hub를 찾아 선택 합니다. IoT hub에 대 한 **메시지 라우팅** 을 선택 합니다.

   ![메시지 라우팅 선택](./media/tutorial-message-enrichments/select-iot-hub.png)

   메시지 라우팅 창에는 **경로**, **사용자 지정 끝점**및 **보강 메시지**라는 세 개의 탭이 있습니다. 처음 두 탭을 탐색 하 여 스크립트에 의해 설정 된 구성을 확인 합니다. 세 번째 탭을 사용 하 여 메시지 강화를 추가 합니다. **보강**라는 저장소 컨테이너의 끝점으로 이동 하는 메시지를 보강 하겠습니다. 이름 및 값을 입력 한 다음 드롭다운 목록에서 **ContosoStorageEndpointEnriched** 끝점을 선택 합니다. 메시지에 IoT hub 이름을 추가 하는 보강을 설정 하는 방법의 예는 다음과 같습니다.

   ![첫 번째 보강 추가](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. 이러한 값을 ContosoStorageEndpointEnriched 끝점 목록에 추가 합니다.

   | 키 | 값 | 끝점 (드롭다운 목록) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | Msds-devicelocation | $twin. tags. location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |customerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > 장치에 쌍이 없는 경우 여기에 입력 하는 값은 메시지 강화의 값에 대 한 문자열로 기록 됩니다. 장치 쌍 정보를 보려면 포털에서 허브로 이동 하 여 **IoT 장치**를 선택 합니다. 장치를 선택 하 고 페이지 위쪽에서 **장치** 쌍을 선택 합니다.
   >
   > 쌍 정보를 편집 하 여 위치와 같은 태그를 추가 하 고 특정 값으로 설정할 수 있습니다. 자세한 내용은 [IoT Hub의 디바이스 쌍 이해 및 사용](iot-hub-devguide-device-twins.md)을 참조하세요.

3. 작업이 완료 되 면 창이 다음 이미지와 같이 표시 됩니다.

   ![모든 강화이 추가 된 테이블](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. **적용** 을 선택 하 여 변경 내용을 저장 합니다. [테스트 메시지 강화](#test-message-enrichments) 섹션으로 건너뜁니다.

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용 하 여 만들기 및 구성
리소스 관리자 템플릿을 사용 하 여 리소스, 메시지 라우팅 및 메시지 강화를 만들고 구성할 수 있습니다.

1. Azure Portal에 로그인합니다. **+ 리소스 만들기** 를 선택 하 여 검색 상자를 표시 합니다. *템플릿 배포*를 입력 하 고 검색 합니다. 결과 창에서 **템플릿 배포 (사용자 지정 템플릿을 사용 하 여 배포)** 을 선택 합니다.

   ![Azure Portal 템플릿 배포](./media/tutorial-message-enrichments/template-select-deployment.png)

1. **템플릿 배포** 창에서 **만들기** 를 선택 합니다.

1. **사용자 지정 배포** 창에서 **편집기에서 고유한 템플릿 빌드**를 선택 합니다.

1. **템플릿 편집** 창에서 **파일 로드**를 선택 합니다. Windows 탐색기가 나타납니다. **/Iot-hub/Tutorials/Routing/SimulatedDevice/resources**의 압축을 푼 리포지토리 파일에서 파일 **의template_messageenrichments.js** 를 찾습니다. 

   ![로컬 컴퓨터에서 템플릿 선택](./media/tutorial-message-enrichments/template-select.png)

1. **열기** 를 선택 하 여 로컬 컴퓨터에서 템플릿 파일을 로드 합니다. 로드 되 고 편집 창에 표시 됩니다.

   이 템플릿은 기본 이름의 끝에 임의 값을 추가 하 여 전역적으로 고유한 IoT hub 이름 및 저장소 계정 이름을 사용 하도록 설정 되므로 템플릿을 변경할 필요 없이 템플릿을 사용할 수 있습니다.

   템플릿을 로드 하 여 만든 리소스는 다음과 같습니다. **보강** 는 강화가 포함 된 메시지에 대 한 리소스 임을 의미 합니다. **원래** 는 보강 않은 메시지에 대 한 리소스 임을 의미 합니다. 이러한 값은 Azure CLI 스크립트에서 사용 되는 것과 동일한 값입니다.

   | Name | 값 |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | 컨테이너 이름 | 오리지널  |
   | 컨테이너 이름 | 보강  |
   | IoT 장치 이름 | Contoso-테스트-장치 |
   | IoT Hub 이름 | ContosoTestHubMsgEn |
   | 저장소 계정 이름 | : contosostorage |
   | 끝점 이름 1 | ContosoStorageEndpointOriginal |
   | 끝점 이름 2 | ContosoStorageEndpointEnriched|
   | 경로 이름 1 | ContosoStorageRouteOriginal |
   | 경로 이름 2 | ContosoStorageRouteEnriched |

1. **저장**을 선택합니다. **사용자 지정 배포** 창이 나타나고 템플릿에서 사용 하는 모든 매개 변수가 표시 됩니다. 설정 해야 하는 유일한 필드는 **리소스 그룹**입니다. 새 항목을 만들거나 드롭다운 목록에서 하나를 선택 합니다.

   **사용자 지정 배포** 창의 위쪽 절반은 다음과 같습니다. 리소스 그룹을 입력 하는 위치를 확인할 수 있습니다.

   ![사용자 지정 배포 창의 위쪽 절반](./media/tutorial-message-enrichments/template-deployment-top.png)

1. **사용자 지정 배포** 창의 아래쪽 절반은 다음과 같습니다. 나머지 매개 변수와 조건을 확인할 수 있습니다. 

   ![사용자 지정 배포 창의 하단 절반](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. 확인란을 선택 하 여 사용 약관에 동의 합니다. 그런 다음 **구매** 를 선택 하 여 템플릿 배포를 계속 합니다.

1. 템플릿이 완전히 배포 될 때까지 기다립니다. 화면 맨 위에 있는 종 모양 아이콘을 선택 하 여 진행 상황을 확인 합니다. 완료 되 면 [테스트 메시지 강화](#test-message-enrichments) 섹션을 계속 진행 합니다.

## <a name="test-message-enrichments"></a>테스트 메시지 강화

강화 메시지를 보려면 **리소스 그룹**을 선택 합니다. 그런 다음이 자습서에 사용 하 고 있는 리소스 그룹을 선택 합니다. 리소스 목록에서 IoT hub를 선택 하 고 **메시징**으로 이동 합니다. 메시지 라우팅 구성과 구성 된 강화 표시 됩니다.

이제 끝점에 대해 강화 메시지를 구성 했으므로 시뮬레이션 된 장치 응용 프로그램을 실행 하 여 IoT hub로 메시지를 보냅니다. 허브는 다음 작업을 수행 하는 설정으로 설정 되었습니다.

* 저장소 끝점 ContosoStorageEndpointOriginal로 라우팅되는 메시지는 보강 되지 않으며 저장소 컨테이너에 저장 됩니다 `original` .

* 저장소 끝점 ContosoStorageEndpointEnriched로 라우팅되는 메시지는 보강 되 고 저장소 컨테이너에 저장 됩니다 `enriched` .

시뮬레이션 된 장치 응용 프로그램은 압축을 푼 다운로드의 응용 프로그램 중 하나입니다. 응용 프로그램은 Azure Storage를 포함 하는 [라우팅 자습서](tutorial-routing.md)의 여러 메시지 라우팅 메서드에 대해 메시지를 보냅니다.

솔루션 파일 **IoT_SimulatedDevice .sln** 을 두 번 클릭 하 여 Visual Studio에서 코드를 열고 **Program.cs**을 엽니다. 표식의 IoT hub 이름을 대체 합니다 `{your hub name}` . IoT hub 호스트 이름의 형식은 **{your hub name}. azure-devices.net**입니다. 이 자습서에서는 허브 호스트 이름이 ContosoTestHubMsgEn.azure-devices.net입니다. 그런 다음, 스크립트를 실행 하 여 마커의 리소스를 만들 때 이전에 저장 한 장치 키를 대체 합니다 `{your device key}` .

장치 키가 없는 경우 포털에서 검색할 수 있습니다. 로그인 한 후 **리소스 그룹**으로 이동 하 여 리소스 그룹을 선택한 다음, IoT hub를 선택 합니다. **IoT 장치** 에서 테스트 장치를 확인 하 고 장치를 선택 합니다. **기본 키** 옆의 복사 아이콘을 선택 하 여 클립보드에 복사 합니다.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>실행 및 테스트

몇 분 동안 콘솔 응용 프로그램을 실행 합니다. 전송 되는 메시지는 응용 프로그램의 콘솔 화면에 표시 됩니다.

앱은 1초마다 새로운 디바이스-클라우드 메시지를 IoT Hub에 보냅니다. 메시지에는 디바이스 ID, 온도, 습도 및 메시지 수준(기본값이 `normal`)과 함께 JSON 직렬화된 개체가 포함됩니다. 또는의 수준을 임의로 할당 하 `critical` 여 `storage` 메시지가 저장소 계정 또는 기본 끝점으로 라우팅됩니다. 저장소 계정의 **보강** 컨테이너로 전송 되는 메시지는 보강 됩니다.

여러 저장소 메시지를 보낸 후에는 데이터를 봅니다.

1. **리소스 그룹**을 선택 합니다. 리소스 그룹인 **ContosoResourcesMsgEn**를 찾고 선택 합니다.

2. 저장소 계정 ( **: contosostorage**)을 선택 합니다. 그런 다음 왼쪽 창에서 **Storage 탐색기 (미리 보기)** 를 선택 합니다.

   ![Storage 탐색기 선택](./media/tutorial-message-enrichments/select-storage-explorer.png)

   **BLOB 컨테이너** 를 선택 하 여 사용할 수 있는 두 컨테이너를 확인 합니다.

   ![저장소 계정의 컨테이너를 참조 하세요.](./media/tutorial-message-enrichments/show-blob-containers.png)

**보강** 라는 컨테이너의 메시지에는 메시지에 포함 된 강화 메시지가 있습니다. **원본** 이라는 컨테이너의 메시지에는 강화 없는 원시 메시지가 있습니다. 아래쪽에 도달할 때까지 컨테이너 중 하나로 드릴 다운 하 고 가장 최근의 메시지 파일을 엽니다. 그런 다음 다른 컨테이너에 대해 동일한 작업을 수행 하 여 해당 컨테이너의 메시지에 강화 추가 되지 않았는지 확인 합니다.

보강 된 메시지를 살펴보면 다음과 같이 허브 이름과 위치 및 고객 ID를 포함 하는 "my IoT Hub"가 표시 되어야 합니다.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Unenriched 메시지는 다음과 같습니다. 이러한 필드는 강화에 의해 추가 되기 때문에 "my IoT Hub," "devicelocation" 및 "customerID"는 여기에 표시 되지 않습니다. 이 끝점에는 강화 없습니다.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스를 모두 제거 하려면 리소스 그룹을 삭제 합니다. 이렇게 하면 그룹 내에 포함된 모든 리소스가 삭제됩니다. 이 경우 IoT Hub, 스토리지 계정 및 리소스 그룹 자체가 제거됩니다.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Azure CLI를 사용하여 리소스 정리

리소스 그룹을 제거하려면 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 명령을 사용합니다. `$resourceGroup`이 자습서의 시작 부분에서 **ContosoResourcesMsgEn** 로 설정 된 리콜입니다.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 단계를 사용 하 여 메시지 강화에 메시지 IoT Hub를 추가 하도록 구성 하 고 테스트 했습니다.

**IoT Hub 메시지 강화 사용**
> [!div class="checklist"]
> * 첫 번째 방법: Azure CLI를 사용 하 여 리소스를 만들고 메시지 라우팅을 구성 합니다. [Azure Portal](https://portal.azure.com)를 사용 하 여 수동으로 메시지 강화를 구성 합니다.
> * 두 번째 방법: Azure Resource Manager 템플릿을 사용 하 여 리소스를 만들고 메시지 라우팅 및 메시지 강화를 구성 합니다.
> * 허브로 메시지를 보내는 IoT 장치를 시뮬레이트하는 앱을 실행 합니다.
> * 결과를 확인 하 고 강화 메시지가 정상적으로 작동 하는지 확인 합니다.

메시지 강화에 대 한 자세한 내용은 [message 강화 개요](iot-hub-message-enrichments-overview.md)를 참조 하세요.

메시지 라우팅에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [IoT Hub 메시지 라우팅을 사용 하 여 다른 끝점으로 장치-클라우드 메시지 보내기](iot-hub-devguide-messages-d2c.md)
* [자습서: IoT Hub 라우팅](tutorial-routing.md)
