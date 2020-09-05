---
title: '자습서: Azure IoT Hub를 통해 디바이스 데이터 수신'
description: 이 자습서에서는 Azure IoT Connector for FHIR을 통해 디바이스 데이터 라우팅을 IoT Hub에서 Azure API for FHIR로 사용하도록 설정하는 방법을 알아봅니다.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 08/03/2020
ms.author: punagpal
ms.openlocfilehash: 04c732b857c06246bdc636f01afd2689c98c2b0d
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87831620"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>자습서: Azure IoT Hub를 통해 디바이스 데이터 수신

Azure IoT Connector for FHIR*은 데이터를 IoMT(의료 사물 인터넷) 디바이스에서 Azure API for FHIR로 수집하는 기능을 제공합니다. [Azure Portal을 사용하여 Azure IoT Connector for FHIR(미리 보기) 배포](iot-fhir-portal-quickstart.md) 빠른 시작에서는 Azure IoT Central에서 [원격 분석을 Azure IoT Connector for FHIR로 보내서](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) 관리하는 디바이스의 예제를 보여 주었습니다. 또한 Azure IoT Connector for FHIR은 Azure IoT Hub를 통해 프로비저닝되고 관리되는 디바이스에서도 작동할 수 있습니다. 이 자습서에서는 디바이스 데이터를 Azure IoT Hub에서 Azure IoT Connector for FHIR로 연결하고 라우팅하는 절차를 제공합니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 하나 이상의 Azure IoT Connector for FHIR이 있는 Azure API for FHIR 리소스 - [Azure Portal을 사용하여 Azure IoT Connector for FHIR(미리 보기) 배포](iot-fhir-portal-quickstart.md)
- 실제 또는 시뮬레이션된 디바이스와 연결된 Azure IoT Hub 리소스 - [Azure Portal을 사용하여 IoT 허브 만들기](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

> [!TIP]
> Azure IoT Hub 시뮬레이션된 디바이스 애플리케이션을 사용하는 경우 지원되는 다양한 언어 및 시스템 중에서 원하는 애플리케이션을 자유롭게 선택할 수 있습니다.

## <a name="get-connection-string-for-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR(미리 보기)에 대한 연결 문자열 가져오기

Azure IoT Hub에는 Azure IoT Connector for FHIR과 안전하게 연결하기 위한 연결 문자열이 필요합니다. [연결 문자열 생성](iot-fhir-portal-quickstart.md#generate-a-connection-string)에서 설명한 대로 Azure IoT Connector for FHIR에 대한 새 연결 문자열을 만듭니다. 이 연결 문자열은 다음 단계에서 사용할 수 있도록 유지합니다.

Azure IoT Connector for FHIR은 내부적으로 Azure Event Hub 인스턴스를 사용하여 디바이스 메시지를 받습니다. 위에서 만든 연결 문자열은 기본적으로 이 기본 Event Hub에 대한 연결 문자열입니다.

## <a name="connect-azure-iot-hub-with-the-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR(미리 보기)과 Azure IoT Hub 연결

Azure IoT Hub는 디바이스 데이터를 Event Hub, 스토리지 계정 및 Service Bus와 같은 다양한 Azure 서비스로 보내는 기능을 제공하는 [메시지 라우팅](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)이라는 기능을 지원합니다. Azure IoT Connector for FHIR은 이 기능을 활용하여 디바이스 데이터를 Azure IoT Hub에서 해당 Event Hub 엔드포인트로 연결하고 보냅니다.

> [!NOTE] 
> Azure IoT Connector for FHIR의 Event Hub가 고객 구독에서 호스팅되지 않으므로 지금은 PowerShell 또는 CLI 명령만 사용하여 [메시지 라우팅을 만들](https://docs.microsoft.com/azure/iot-hub/tutorial-routing) 수 있습니다. 따라서 이 메시지 라우팅은 Azure Portal을 통해 표시되지 않습니다. 그러나 PowerShell 또는 CLI를 사용하여 메시지 경로 개체를 추가하면 Azure Portal에서 해당 개체가 표시되고 여기서 관리할 수 있습니다.

메시지 라우팅을 설정하는 작업은 두 단계로 구성됩니다.

### <a name="add-an-endpoint"></a>엔드포인트 추가
이 단계에서는 IoT Hub에서 데이터를 라우팅하는 엔드포인트를 정의합니다. 이 엔드포인트는 기본 설정에 따라 [Add-AzIotHubRoutingEndpoint](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) PowerShell 명령 또는 [az iot hub routing-endpoint create](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?#az-iot-hub-routing-endpoint-create) CLI 명령을 사용하여 만듭니다.

다음은 엔드포인트를 만드는 명령과 함께 사용할 매개 변수의 목록입니다.

|PowerShell 매개 변수|CLI 매개 변수|Description|
|---|---|---|
|ResourceGroupName|resource-group|IoT Hub 리소스의 리소스 그룹 이름입니다.|
|Name|hub-name|IoT Hub 리소스의 이름입니다.|
|EndpointName|endpoint-name|만드는 엔드포인트에 할당하려는 이름을 사용합니다.|
|EndpointType|endpoint-type|IoT Hub에서 연결해야 하는 엔드포인트의 유형입니다. 리터럴 값으로 PowerShell의 경우 "EventHub"를 사용하고, CLI의 경우 "eventhub"를 사용합니다.|
|EndpointResourceGroup|endpoint-resource-group|Azure IoT Connector for FHIR의 Azure API for FHIR 리소스에 대한 리소스 그룹 이름입니다. 이 값은 Azure API for FHIR의 [개요] 페이지에서 가져올 수 있습니다.|
|EndpointSubscriptionId|endpoint-subscription-id|Azure IoT Connector for FHIR의 Azure API for FHIR 리소스에 대한 구독 ID입니다. 이 값은 Azure API for FHIR의 [개요] 페이지에서 가져올 수 있습니다.|
|ConnectionString|connection-string|Azure IoT Connector for FHIR에 대한 연결 문자열입니다. 이전 단계에서 얻은 값을 사용합니다.|

### <a name="add-a-message-route"></a>메시지 경로 추가
이 단계에서는 위에서 만든 엔드포인트를 사용하여 메시지 경로를 정의합니다. 이 경로는 기본 설정에 따라 [Add-AzIotHubRoute](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIoTHubRoute) PowerShell 명령 또는 [az iot hub route create](https://docs.microsoft.com/cli/azure/iot/hub/route#az-iot-hub-route-create) CLI 명령을 사용하여 만듭니다.

다음은 엔드포인트를 만드는 명령과 함께 사용할 매개 변수의 목록입니다.

|PowerShell 매개 변수|CLI 매개 변수|Description|
|---|---|---|
|ResourceGroupName|g|IoT Hub 리소스의 리소스 그룹 이름입니다.|
|Name|hub-name|IoT Hub 리소스의 이름입니다.|
|EndpointName|endpoint-name|위에서 만든 엔드포인트의 이름입니다.|
|RouteName|route-name|만드는 메시지 경로에 할당하려는 이름입니다.|
|원본|source-type|엔드포인트로 보낼 데이터의 형식입니다. 리터럴 값으로 PowerShell의 경우 "DeviceMessages"를 사용하고, CLI의 경우 "devicemessages"를 사용합니다.|

## <a name="send-device-message-to-iot-hub"></a>IoT Hub에 디바이스 메시지 보내기

실제 또는 시뮬레이션된 디바이스를 사용하여 아래에 표시된 심박수 메시지 샘플을 Azure IoT Hub에 보냅니다. 이 메시지는 Azure IoT Connector for FHIR로 라우팅되며, 여기서 메시지는 FHIR Observation 리소스로 변환되어 Azure API for FHIR에 저장됩니다.

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> Azure IoT Connector for FHIR을 사용하여 구성된 [매핑 템플릿](iot-mapping-templates.md)을 준수하는 디바이스 메시지를 보내야 합니다.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Azure API for FHIR에서 디바이스 데이터 보기

Postman을 사용하여 Azure API for FHIR에서 Azure IoT Connector for FHIR을 통해 만든 FHIR 관찰 리소스를 볼 수 있습니다. 위의 메시지 샘플에서 제출된 심박수 값을 사용하여 FHIR Observation 리소스를 보려면 [Azure API for FHIR에 액세스](access-fhir-postman-tutorial.md)하여 `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4`에 대한 `GET` 요청을 수행하도록 Postman을 설정합니다.

> [!TIP]
> 사용자에게 Azure API for FHIR 데이터 평면에 액세스할 수 있는 적절한 권한이 있어야 합니다. [Azure RBAC(Azure 역할 기반 액세스 제어)](configure-azure-rbac.md)를 사용하여 필요한 데이터 평면 역할을 할당합니다.


## <a name="next-steps"></a>다음 단계

이 빠른 시작 가이드에서는 디바이스 데이터를 Azure IoT Connector for FHIR로 라우팅하도록 Azure IoT Hub를 설정합니다. Azure IoT Connector for FHIR에 대해 자세히 알아보려면 아래 단계 중에서 선택하세요.

Azure IoT Connector for FHIR 내에서 데이터 흐름의 여러 단계를 이해합니다.

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR 데이터 흐름](iot-data-flow.md)

디바이스 및 FHIR 매핑 템플릿을 사용하여 IoT 커넥터를 구성하는 방법을 알아봅니다.

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR 매핑 템플릿](iot-mapping-templates.md)

*Azure Portal에서는 Azure IoT Connector for FHIR을 IoT 커넥터(미리 보기)라고 합니다.

FHIR은 HL7의 등록 상표이며, HL7의 사용 허가 하에 사용됩니다.