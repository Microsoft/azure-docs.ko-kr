---
title: Azure IoT Hub에서 디바이스 상태 동기화 | Microsoft Docs
description: 디바이스 쌍을 사용하여 디바이스와 IoT 허브 간의 상태를 동기화합니다.
services: iot-hub
documentationcenter: ''
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/18/2019
ms.custom: mvc
ms.openlocfilehash: 85f1c051f13484ea8e14a6ae8402067b613fe2bc
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597540"
---
<!-- **TODO** Update publish config with repo paths before publishing! -->

# <a name="tutorial-configure-your-devices-from-a-back-end-service"></a>자습서: 백 엔드 서비스에서 디바이스 구성

디바이스에서 원격 분석을 받는 것뿐만 아니라 백 엔드 서비스에서 디바이스를 구성해야 할 수도 있습니다. 원하는 구성을 디바이스로 보내면 해당 디바이스에서 상태 및 준수 업데이트를 받을 수도 있습니다. 예를 들어 디바이스의 대상 작동 온도 범위를 설정하거나 디바이스의 펌웨어 버전 정보를 수집할 수 있습니다.

디바이스와 IoT 허브 간에 상태 정보를 동기화하려면 _디바이스 쌍_을 사용합니다. [디바이스 쌍](iot-hub-devguide-device-twins.md)은 특정 디바이스와 관련된 JSON 문서이며, IoT Hub를 통해 [쿼리](iot-hub-devguide-query-language.md)할 수 있는 클라우드에 저장됩니다. 디바이스 쌍에는 _desired 속성_, _reported 속성_ 및 _태그_가 있습니다. desired 속성은 백 엔드 애플리케이션에서 설정하고 장치에서 읽습니다. reported 속성은 장치에서 설정하고 백 엔드 애플리케이션에서 읽습니다. 태그는 백 엔드 애플리케이션에서 설정하고 장치로 보내지 않습니다. 태그를 사용하여 디바이스를 구성합니다. 이 자습서에서는 desired 속성과 reported 속성을 사용하여 상태 정보를 동기화하는 방법을 보여 줍니다.

![쌍 요약](media/tutorial-device-twins/DeviceTwins.png)

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * IoT 허브를 만들고 ID 레지스트리에 테스트 디바이스를 추가합니다.
> * desired 속성을 사용하여 시뮬레이션된 디바이스에 상태 정보를 보냅니다.
> * reported 속성을 사용하여 시뮬레이션된 디바이스에서 상태 정보를 받습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서 실행하는 두 개의 샘플 애플리케이션은 Node.js를 사용하여 작성되었습니다. 개발 머신에 Node.js v10.x.x 이상이 필요합니다.

[nodejs.org](https://nodejs.org)에서 여러 플랫폼에 대한 Node.js를 다운로드할 수 있습니다.

다음 명령을 사용하여 개발 컴퓨터에서 Node.js의 현재 버전을 확인할 수 있습니다.

```cmd/sh
node --version
```

[https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip](https://github.com/Azure-Samples/azure-iot-samples-node/archive/master.zip )에서 샘플 Node.js 프로젝트를 다운로드하고 ZIP 보관 파일을 추출합니다.

## <a name="set-up-azure-resources"></a>Azure 리소스 설정

이 자습서를 완료하려면 Azure 구독에 디바이스 ID 레지스트리에 디바이스가 추가된 IoT 허브가 있어야 합니다. 디바이스 ID 레지스트리의 항목을 사용하면 이 자습서에서 실행하는 시뮬레이션된 디바이스를 허브에 연결할 수 있습니다.

구독에 아직 IoT 허브를 설정하지 않은 경우 다음 CLI 스크립트를 사용하여 하나의 IoT 허브를 설정할 수 있습니다. 다음 스크립트는 IoT 허브에 대해 **tutorial-iot-hub**라는 이름을 사용하므로 실행할 때는 이 이름을 사용자의 고유 이름으로 바꿔야 합니다. 이 스크립트의 경우 **미국 중부** 지역에 리소스 그룹과 허브를 만들지만, 가장 가까운 지역으로 변경할 수 있습니다. 그리고 백 엔드 샘플에서 IoT 허브에 연결하는 데 사용하는 IoT 허브 서비스 연결 문자열을 검색합니다.

```azurecli-interactive
hubname=tutorial-iot-hub
location=centralus

# Install the IoT extension if it's not already installed:
az extension add --name azure-cli-iot-ext

# Create a resource group:
az group create --name tutorial-iot-hub-rg --location $location

# Create your free-tier IoT Hub. You can only have one free IoT Hub per subscription:
az iot hub create --name $hubname --location $location --resource-group tutorial-iot-hub-rg --sku F1

# Make a note of the service connection string, you need it later:
az iot hub show-connection-string --name $hubname -o table

```

이 자습서에서는 **MyTwinDevice**라는 시뮬레이션된 디바이스를 사용합니다. 다음 스크립트는 이 디바이스를 ID 레지스트리에 추가하고 해당 연결 문자열을 검색합니다.

```azurecli-interactive
# Set the name of your IoT hub:
hubname=tutorial-iot-hub

# Create the device in the identity registry:
az iot hub device-identity create --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg

# Retrieve the device connection string, you need this later:
az iot hub device-identity show-connection-string --device-id MyTwinDevice --hub-name $hubname --resource-group tutorial-iot-hub-rg -o table

```

## <a name="send-state-information"></a>상태 정보 보내기

desired 속성을 사용하여 백 엔드 애플리케이션에서 장치로 상태 정보를 보냅니다. 이 섹션에서 수행하는 방법은 다음과 같습니다.

* 디바이스에서 desired 속성을 받고 처리합니다.
* 백 엔드 애플리케이션에서 desired 속성을 보냅니다.

desired 속성을 받는 시뮬레이션된 디바이스 샘플 코드를 보려면, 다운로드한 Node.js 프로젝트 샘플의 **iot-hub/Tutorials/DeviceTwins** 폴더로 이동합니다. 그런 다음, 텍스트 편집기에서 SimulatedDevice.js 파일을 엽니다.

다음 섹션에서는 백 엔드 애플리케이션에서 보낸 desired 속성 변경 내용에 응답하는 시뮬레이션된 장치에서 실행되는 코드에 대해 설명합니다.

### <a name="retrieve-the-device-twin-object"></a>디바이스 쌍 개체 가져오기

다음 코드에서는 디바이스 연결 문자열을 사용하여 IoT 허브에 연결합니다.

[!code-javascript[Create IoT Hub client](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=createhubclient&highlight=2 "Create IoT Hub client")]

다음 코드에서는 클라이언트 개체에서 쌍을 가져옵니다.

[!code-javascript[Get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=gettwin&highlight=2 "Get twin")]

### <a name="sample-desired-properties"></a>desired 속성 샘플

애플리케이션에 편리한 방식으로 desired 속성을 구조화할 수 있습니다. 다음 예제에서는 **fanOn**이라는 최상위 속성 하나를 사용하고, 나머지 속성은 별도의 **components**에 그룹화합니다. 다음 JSON 코드 조각에서는 이 자습서에서 사용하는 desired 속성의 구조를 보여 줍니다.

[!code[Sample desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/desired.json "Sample desired properties")]

### <a name="create-handlers"></a>처리기 만들기

JSON 계층 구조의 여러 수준에서 업데이트에 응답하는 desired 속성 업데이트를 위한 처리기를 만들 수 있습니다. 예를 들어 다음 처리기는 백 엔드 애플리케이션에서 장치로 보낸 모든 desired 속성 변경 내용을 표시합니다. **delta** 변수에는 솔루션 백 엔드에서 보낸 desired 속성이 포함됩니다.

[!code-javascript[Handle all properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=allproperties&highlight=2 "Handle all properties")]

다음 처리기는 **fanOn** desired 속성에 대한 변경 내용에만 대응합니다.

[!code-javascript[Handle fan property](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=fanproperty&highlight=2 "Handle fan property")]

### <a name="handlers-for-multiple-properties"></a>여러 속성에 대한 처리기

앞서의 desired 속성 JSON 예제에서 **components** 아래의 **climate** 노드에는 **minTemperature** 및 **maxTemperature**의 두 속성이 있습니다.

디바이스의 로컬 **쌍** 개체는 desired 속성과 reported 속성의 전체 집합을 저장합니다. 백 엔드에서 보낸 **delta**는 desired 속성의 일부만 업데이트할 수 있습니다. 다음 코드 조각에서는 시뮬레이션된 디바이스에서 **minTemperature** 및 **maxTemperature** 중 하나에 대한 업데이트를 받으면 다른 값에 대한 로컬 쌍의 값을 사용하여 디바이스를 구성합니다.

[!code-javascript[Handle climate component](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=climatecomponent&highlight=2 "Handle climate component")]

로컬 **쌍** 개체는 desired 속성과 reported 속성의 전체 집합을 저장합니다. 백 엔드에서 보낸 **delta**는 desired 속성의 일부만 업데이트할 수 있습니다.

### <a name="handle-insert-update-and-delete-operations"></a>삽입, 업데이트 및 삭제 작업 처리

백 엔드에서 보낸 desired 속성은 특정 desired 속성에 대해 수행되는 작업을 나타내지 않습니다. 코드에서는 로컬에 저장된 현재 desired 속성 집합과 허브에서 보낸 변경 내용에서 작업을 유추해야 합니다.

다음 코드 조각에서는 시뮬레이션된 디바이스에서 desired 속성의 **components** 목록에 대한 삽입, 업데이트 및 삭제 작업을 처리하는 방법을 보여줍니다. **null** 값을 사용하여 구성 요소를 삭제해야 함을 나타내는 방법을 볼 수 있습니다.

[!code-javascript[Handle components](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=components&highlight=2,6,13 "Handle components")]

### <a name="send-desired-properties-to-a-device-from-the-back-end"></a>백 엔드에서 디바이스로 desired 속성 보내기

디바이스에서 desired 속성 업데이트를 받기 위한 처리기를 구현하는 방법을 살펴보았습니다. 이 섹션에서는 desired 속성 변경 내용을 백 엔드 애플리케이션에서 장치로 보내는 방법을 보여 줍니다.

desired 속성을 받는 시뮬레이션된 디바이스 샘플 코드를 보려면, 다운로드한 Node.js 프로젝트 샘플의 **iot-hub/Tutorials/DeviceTwins** 폴더로 이동합니다. 그런 다음, 텍스트 편집기에서 ServiceClient.js 파일을 엽니다.

다음 코드 조각에서는 디바이스 ID 레지스트리에 연결하고 특정 디바이스에 대한 쌍에 액세스하는 방법을 보여줍니다.

[!code-javascript[Create registry and get twin](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=getregistrytwin&highlight=2,6 "Create registry and get twin")]

다음 코드 조각에서는 백 엔드 애플리케이션에서 장치로 보내는 여러 가지 desired 속성 *패치*를 보여 줍니다.

[!code-javascript[Patches sent to device](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=patches&highlight=2,12,26,41,56 "Patches sent to device")]

다음 코드 조각에서는 백 엔드 애플리케이션이 desired 속성 업데이트를 장치로 보내는 방법을 보여 줍니다.

[!code-javascript[Send desired properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=senddesiredproperties&highlight=2 "Send desired properties")]

### <a name="run-the-applications"></a>애플리케이션 실행

이 섹션에서는 두 개의 샘플 애플리케이션을 실행하여 백 엔드 애플리케이션에서 desired 속성 업데이트를 시뮬레이션된 장치 애플리케이션으로 보내는 것을 관찰합니다.

시뮬레이션된 장치 및 백 엔드 애플리케이션을 실행하려면 장치 및 서비스 연결 문자열이 필요합니다. 이 자습서의 시작에서 리소스를 만들 때 연결 문자열을 적어두었습니다.

시뮬레이션된 장치 애플리케이션을 실행하려면 셸 또는 명령 프롬프트 창을 열고 다운로드한 Node.js 프로젝트의 **iot-hub/Tutorials/DeviceTwins** 폴더로 이동합니다. 그런 다음, 다음 명령을 실행합니다.

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

백 엔드 애플리케이션을 실행하려면 다른 셸 또는 명령 프롬프트 창을 엽니다. 그런 다음, 다운로드한 Node.js 프로젝트의 **iot-hub/Tutorials/DeviceTwins** 폴더로 이동합니다. 그런 다음, 다음 명령을 실행합니다.

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

다음 스크린샷에서는 시뮬레이션된 장치 애플리케이션의 출력을 보여 주고, **maxTemperature** desired 속성에 대한 업데이트를 처리하는 방법을 강조 표시하고 있습니다. 최상위 처리기와 climate component(기후 구성 요소) 처리기가 모두 실행되는 방식을 확인할 수 있습니다.

![시뮬레이션된 디바이스](./media/tutorial-device-twins/SimulatedDevice1.png)

다음 스크린샷에서는 백 엔드 애플리케이션의 출력을 보여 주고, **maxTemperature** desired 속성에 대한 업데이트를 보내는 방법을 강조 표시하고 있습니다.

![백 엔드 애플리케이션](./media/tutorial-device-twins/BackEnd1.png)

## <a name="receive-state-information"></a>상태 정보 받기

백 엔드 애플리케이션은 장치의 상태 정보를 reported 속성으로 받습니다. 디바이스에서 reported 속성을 설정하고 이 속성을 허브로 보냅니다. 백 엔드 애플리케이션은 reported 속성의 현재 값을 허브에 저장된 장치 쌍에서 읽을 수 있습니다.

### <a name="send-reported-properties-from-a-device"></a>디바이스에서 reported 속성 보내기

reported 속성 값에 대한 업데이트를 패치로 보낼 수 있습니다. 다음 코드 조각에서는 시뮬레이션된 디바이스에서 보내는 패치에 대한 템플릿을 보여줍니다. 시뮬레이션된 디바이스에서 패치의 필드를 업데이트한 후에 해당 패치를 허브로 보냅니다.

[!code-javascript[Reported properties patches](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=reportedpatch&highlight=2 "Reported properties patches")]

시뮬레이션된 디바이스에서 다음 함수를 사용하여 reported 속성이 포함된 패치를 허브로 보냅니다.

[!code-javascript[Send reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/SimulatedDevice.js?name=sendreportedproperties&highlight=2 "Send reported properties")]

### <a name="process-reported-properties"></a>reported 속성 처리

백 엔드 애플리케이션에서 장치 쌍을 통해 장치에 대한 현재 reported 속성 값에 액세스합니다. 다음 코드 조각에서는 백 엔드 애플리케이션에서 시뮬레이션된 장치에 대한 reported 속성 값을 읽는 방법을 보여 줍니다.

[!code-javascript[Display reported properties](~/iot-samples-node/iot-hub/Tutorials/DeviceTwins/ServiceClient.js?name=displayreportedproperties&highlight=2 "Display reported properties")]

### <a name="run-the-applications"></a>애플리케이션 실행

이 섹션에서는 두 개의 샘플 애플리케이션을 실행하여 시뮬레이션된 장치에서 reported 속성 업데이트를 백 엔드 애플리케이션 애플리케이션으로 보내는 것을 관찰합니다.

desired 속성을 장치로 보내는 방법을 확인하기 위해 실행한 두 개의 샘플 애플리케이션을 동일한 방식으로 실행합니다.

시뮬레이션된 장치 및 백 엔드 애플리케이션을 실행하려면 장치 및 서비스 연결 문자열이 필요합니다. 이 자습서의 시작에서 리소스를 만들 때 연결 문자열을 적어두었습니다.

시뮬레이션된 장치 애플리케이션을 실행하려면 셸 또는 명령 프롬프트 창을 열고 다운로드한 Node.js 프로젝트의 **iot-hub/Tutorials/DeviceTwins** 폴더로 이동합니다. 그런 다음, 다음 명령을 실행합니다.

```cmd/sh
npm install
node SimulatedDevice.js "{your device connection string}"
```

백 엔드 애플리케이션을 실행하려면 다른 셸 또는 명령 프롬프트 창을 엽니다. 그런 다음, 다운로드한 Node.js 프로젝트의 **iot-hub/Tutorials/DeviceTwins** 폴더로 이동합니다. 그런 다음, 다음 명령을 실행합니다.

```cmd/sh
npm install
node ServiceClient.js "{your service connection string}"
```

다음 스크린샷에서는 시뮬레이션된 장치 애플리케이션의 출력을 보여 주고, reported 속성 업데이트를 허브로 보내는 방법을 강조 표시하고 있습니다.

![시뮬레이션된 디바이스](./media/tutorial-device-twins/SimulatedDevice2.png)

다음 스크린샷에서는 백 엔드 애플리케이션의 출력을 보여 주고, 디바이스에서 reported 속성 업데이트를 받고 처리하는 방법을 강조 표시하고 있습니다.

![백 엔드 애플리케이션](./media/tutorial-device-twins/BackEnd2.png)

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서를 완료하려면 리소스 그룹과 IoT 허브를 그대로 두고 나중에 다시 사용합니다.

더 이상 IoT Hub가 필요하지 않으면 포털에서 IoT Hub와 리소스 그룹을 삭제합니다. 이렇게 하려면 IoT 허브가 포함된 **tutorial-iot-hub-rg** 리소스 그룹을 선택하고 **삭제**를 클릭합니다.

또는 CLI를 사용합니다.

```azurecli-interactive
# Delete your resource group and its contents
az group delete --name tutorial-iot-hub-rg
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 디바이스와 IoT 허브 간에 상태 정보를 동기화하는 방법을 알아보았습니다. 디바이스 쌍을 사용하여 펌웨어 업데이트 프로세스를 구현하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [디바이스 펌웨어 업데이트 프로세스 구현](tutorial-firmware-update.md)
