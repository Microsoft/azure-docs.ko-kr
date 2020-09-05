---
title: '빠른 시작: Azure Portal을 사용하여 Azure IoT Connector for FHIR(미리 보기) 배포'
description: 이 빠른 시작에서는 Azure Portal을 사용하여 Azure API for FHIR의 Azure IoT Connector for FHIR 기능을 배포, 구성 및 사용하는 방법을 알아봅니다.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 08/04/2020
ms.author: punagpal
ms.openlocfilehash: 55d072492a1d90c6964935f2a79f73aacdceff21
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87826775"
---
# <a name="quickstart-deploy-azure-iot-connector-for-fhir-preview-using-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Azure IoT Connector for FHIR(미리 보기) 배포

Azure IoT Connector for FHIR*는 IoMT(의료 사물 인터넷) 디바이스에서 데이터를 수집하는 기능을 제공하는 Azure API for FHIR의 선택적 기능입니다. 미리 보기 단계 중에는 Azure IoT Connector for FHIR 기능을 무료로 사용할 수 있습니다. 이 빠른 시작에서 다음을 수행하는 방법을 알아봅니다.
- Azure Portal을 사용하여 Azure IoT Connector for FHIR 배포 및 구성
- 시뮬레이션된 디바이스를 사용하여 Azure IoT Connector for FHIR로 데이터 전송
- Azure API for FHIR에서 Azure IoT Connector for FHIR가 만든 리소스 보기

## <a name="prerequisites"></a>필수 구성 요소

- 활성 Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API for FHIR 리소스 - [Azure Portal을 사용하여 Azure API for FHIR 배포](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>Azure API for FHIR 리소스로 이동

[Azure Portal](https://portal.azure.com)을 열고 Azure IoT Connector for FHIR 기능을 만들려는 **Azure API for FHIR** 리소스로 이동합니다.

[![Azure API for FHIR 리소스](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

왼쪽 탐색 메뉴의 **추가 기능** 섹션에서 **IoT Connector(미리 보기)** 를 클릭하여 **IoT Connector** 페이지를 엽니다.

[![IoT Connector 기능](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-azure-iot-connector-for-fhir-preview"></a>새 Azure IoT Connector for FHIR(미리 보기) 만들기

**추가** 단추를 클릭하여 **IoT Connector 만들기** 페이지를 엽니다.

[![IoT Connector 추가](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

새 Azure IoT Connector for FHIR에 대한 설정을 입력합니다. **만들기** 단추를 클릭하고 Azure IoT Connector for FHIR가 배포될 때까지 기다립니다.

> [!NOTE]
> 이 설치에 대한 **해결 유형** 드롭다운의 값으로 **만들기**를 선택해야 합니다. 

[![IoT Connector 만들기](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|설정|값|설명 |
|---|---|---|
|커넥터 이름|고유한 이름|Azure IoT Connector for FHIR을 식별하는 이름을 입력합니다. 이 이름은 Azure API for FHIR 리소스 내에서 고유해야 합니다. 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 문자 또는 숫자로 시작하고 끝나야 하며, 길이가 3-24자 사이여야 합니다.|
|해결 유형|조회 또는 만들기|Azure API for FHIR에서 [디바이스](https://www.hl7.org/fhir/device.html) 및 [환자](https://www.hl7.org/fhir/patient.html) FHIR 리소스를 만드는 대역 외 프로세스가 있는 경우 **조회**를 선택합니다. Azure IoT Connector for FHIR는 디바이스 데이터를 나타내는 [관찰](https://www.hl7.org/fhir/observation.html) FHIR 리소스를 만들 때 이러한 리소스에 대한 참조를 사용합니다. Azure IoT Connector for FHIR가 디바이스 데이터에 제공된 각 식별자 값을 사용하여 Azure API for FHIR에 베어본 디바이스 및 환자 리소스를 만들게 하려면 **만들기**를 선택합니다.|

설치가 완료되면 새로 만든 Azure IoT Connector for FHIR가 **IoT Connector** 페이지에 표시됩니다.

[![IoT Connector 생성됨](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR(미리 보기) 구성

Azure IoT Connector for FHIR에는 디바이스 메시지를 FHIR 기반 관찰 리소스로 변환하는 두 가지 매핑 템플릿인 **디바이스 매핑** 및 **FHIR 매핑**이 필요합니다. 두 매핑이 업로드되기 전에는 Azure IoT Connector for FHIR가 완전하게 작동하지 않습니다.

[![IoT 커넥터 매핑 없음](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

매핑 템플릿을 업로드하려면 새로 배포된 Azure IoT Connector for FHIR을 클릭하여 **IoT Connector** 페이지로 이동합니다.

[![IoT Connector 클릭](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg#lightbox)

#### <a name="device-mapping"></a>디바이스 매핑

디바이스 매핑 템플릿은 디바이스 데이터를 정규화된 스키마로 변환합니다. **IoT Connector** 페이지에서 **디바이스 매핑 구성** 단추를 클릭하여 **디바이스 매핑** 페이지로 이동합니다. 

[![IoT Connector 디바이스 매핑 구성 클릭](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg#lightbox)

**디바이스 매핑** 페이지에서 다음 스크립트를 JSON 편집기에 추가하고 **저장**을 클릭합니다.

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.HeartRate)]",
        "patientIdExpression": "$.SystemProperties.iothub-connection-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

[![IoT Connector 디바이스 매핑](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg#lightbox)

#### <a name="fhir-mapping"></a>FHIR 매핑

FHIR 매핑 템플릿은 정규화된 메시지를 FHIR 기반 관찰 리소스로 변환합니다. **IoT Connector** 페이지에서 **FHIR 매핑 구성** 단추를 클릭하여 **FHIR 매핑** 페이지로 이동합니다.  

[![IoT Connector FHIR 매핑 구성 클릭](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg#lightbox)

**FHIR 매핑** 페이지에서 다음 스크립트를 JSON 편집기에 추가하고 **저장**을 클릭합니다.

```json
{
  "templateType": "CollectionFhir",
  "template": [
    {
      "templateType": "CodeValueFhir",
      "template": {
        "codes": [
          {
            "code": "8867-4",
            "system": "http://loinc.org",
            "display": "Heart rate"
          }
        ],
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

[![IoT Connector FHIR 매핑](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg#lightbox)

## <a name="generate-a-connection-string"></a>연결 문자열 생성

IoMT 디바이스는 Azure IoT Connector for FHIR에 연결하여 메시지를 보내기 위한 연결 문자열이 필요합니다. 새로 배포된 Azure IoT Connector for FHIR의 **IoT Connector** 페이지에서 **클라이언트 연결 관리** 단추를 선택합니다. 

[![IoT Connector 클라이언트 연결 관리 클릭](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg#lightbox)

**연결** 페이지에서 **추가** 단추를 클릭하여 새 연결을 만듭니다. 

[![IoT Connector 연결](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg#lightbox)

오버레이 창에서 이 연결의 식별 이름을 입력하고 **만들기** 단추를 선택합니다.

[![IoT Connector 새 연결](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg)](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg#lightbox)

**연결** 페이지에서 새로 만든 연결을 선택하고, 오른쪽의 오버레이 창에서 **기본 연결 문자열** 필드의 값을 복사합니다.

[![IoT Connector 연결 문자열](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg#lightbox)

이 연결 문자열은 이후 단계에서 사용할 수 있도록 유지합니다. 

## <a name="connect-your-devices-to-iot"></a>IoT에 디바이스 연결

Azure는 IoT 디바이스를 연결하고 관리할 수 있는 광범위한 IoT 제품군을 제공합니다. Azure IoT Hub를 사용하여 PaaS 기반의 자체 솔루션을 빌드할 수도 있고, Azure IoT Central에서 IoT 앱 플랫폼 관리를 시작할 수도 있습니다. 이 자습서에서는 시작하는 데 도움이 되는 업계 중심의 솔루션 템플릿을 포함하고 있는 Azure IoT Central을 활용합니다.

[지속적인 환자 모니터링 애플리케이션 템플릿](https://docs.microsoft.com/azure/iot-central/healthcare/tutorial-continuous-patient-monitoring#create-an-application-template)을 배포합니다. 이 템플릿에는 처음 시작하는 분들을 위해 실시간 데이터를 생성하는 두 개의 시뮬레이션된 디바이스인 **Smart Vitals Patch** 및 **Smart Knee Brace**가 포함되어 있습니다.

> [!NOTE]
> 실제 디바이스가 준비되면 언제든지 동일한 IoT Central 애플리케이션을 사용하여 [디바이스를 온보딩](https://docs.microsoft.com/azure/iot-central/core/howto-set-up-template)하고 디바이스 시뮬레이터를 바꿀 수 있습니다. 디바이스 데이터는 자동으로 FHIR에 전달되기 시작합니다. 

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector for FHIR(미리 보기)과 IoT 데이터 연결
IoT Central 애플리케이션을 배포한 후에는 두 개의 기본 시뮬레이션 디바이스가 원격 분석 데이터를 생성하기 시작합니다. 이 자습서에서는 Azure IoT Connector for FHIR을 통해 *Smart Vitals Patch* 시뮬레이터에서 FHIR로 원격 분석 데이터를 수집합니다. IoT 데이터를 Azure IoT Connector for FHIR로 내보내려면 [IoT Central 내에서 연속 데이터 내보내기를 설정](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#set-up-data-export)합니다. 연속 데이터 내보내기 페이지에서 다음을 수행합니다.
- 내보내기 대상으로 *Azure Event Hubs*를 선택합니다.
- **Event Hubs 네임스페이스**에 대해 *연결 문자열 사용* 값을 선택합니다.
- 이전 단계에서 얻은 Azure IoT Connector for FHIR의 연결 문자열을 **연결 문자열** 필드에 입력합니다.
- **내보낼 데이터** 필드에서 **원격 분석** 옵션을 *켜기*로 유지합니다.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Azure API for FHIR에서 디바이스 데이터 보기

Postman을 사용하여 Azure API for FHIR에서 Azure IoT Connector for FHIR을 통해 만든 FHIR 기반 관찰 리소스를 볼 수 있습니다. 심박수 값을 사용하여 관찰 FHIR 리소스를 보려면 [Azure API for FHIR에 액세스](access-fhir-postman-tutorial.md)하여 `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4`에 대한 `GET` 요청을 수행하도록 Postman을 설정합니다. 

> [!TIP]
> 사용자에게 Azure API for FHIR 데이터 평면에 액세스할 수 있는 적절한 권한이 있어야 합니다. [Azure RBAC(Azure 역할 기반 액세스 제어)](configure-azure-rbac.md)를 사용하여 필요한 데이터 평면 역할을 할당합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 연결된 리소스 그룹, 연결된 Azure API for FHIR 서비스 또는 Azure IoT Connector for FHIR 인스턴스 자체를 제거하여 Azure IoT Connector for FHIR 인스턴스를 삭제할 수 있습니다. 

Azure IoT Connector for FHIR 인스턴스를 직접 제거하려면 **IoT Connector** 페이지에서 인스턴스를 선택하여 **IoT Connector** 페이지로 이동한 다음, **삭제** 단추를 클릭합니다. 확인 메시지가 표시되면 **예**를 선택합니다. 

[![IoT Connector 인스턴스 삭제](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>다음 단계

이 빠른 시작 가이드에서는 Azure API for FHIR 리소스에 Azure IoT Connector for FHIR 기능을 배포했습니다. Azure IoT Connector for FHIR에 대해 자세히 알아보려면 아래 단계 중에서 선택하세요.

Azure IoT Connector for FHIR 내에서 데이터 흐름의 여러 단계를 이해합니다.

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR 데이터 흐름](iot-data-flow.md)

디바이스 및 FHIR 매핑 템플릿을 사용하여 IoT 커넥터를 구성하는 방법을 알아봅니다.

>[!div class="nextstepaction"]
>[Azure IoT Connector for FHIR 매핑 템플릿](iot-mapping-templates.md)

*Azure Portal에서는 Azure IoT Connector for FHIR을 IoT 커넥터(미리 보기)라고 합니다.

FHIR은 HL7의 등록 상표이며, HL7의 사용 허가 하에 사용됩니다.