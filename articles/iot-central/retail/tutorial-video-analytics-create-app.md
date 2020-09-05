---
title: 자습서 - Azure IoT Central에서 비디오 분석 - 개체 및 동작 감지 애플리케이션 만들기
description: 이 자습서에서는 IoT Central에서 비디오 분석 애플리케이션을 만드는 방법을 보여줍니다. 이 애플리케이션을 만들어서 사용자 지정하고, 다른 Azure 서비스에 연결합니다.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 07/31/2020
ms.openlocfilehash: b98406984f2c9f2adfca030369a6ea3d47a786f3
ms.sourcegitcommit: e2b36c60a53904ecf3b99b3f1d36be00fbde24fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88762780"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central"></a>자습서: Azure IoT Central에서 비디오 분석 - 개체 및 동작 감지 애플리케이션 만들기

솔루션 개발자로서 IoT Central *비디오 분석 - 개체 및 동작 감지* 애플리케이션 템플릿, Azure IoT Edge 디바이스 및 Azure Media Services를 사용하여 비디오 분석 애플리케이션을 만드는 방법을 알아봅니다. 이 솔루션에서는 소매점을 사용하여 보안 카메라 모니터링이라는 일반적인 비즈니스 요구 사항을 충족하는 방법을 보여줍니다. 이 솔루션에서는 비디오 피드의 자동 개체 감지를 사용하여 관심 있는 이벤트를 신속하게 식별하고 찾습니다.

샘플 애플리케이션에는 시뮬레이션된 디바이스 2개와 IoT Edge 게이트웨이 하나가 포함되어 있습니다. 다음 자습서에서는 게이트웨이의 기능을 실험하고 이해하는 두 가지 방법을 보여줍니다.

* Azure VM에서 IoT Edge 게이트웨이를 만들고 시뮬레이션된 카메라를 연결합니다.
* Intel NUC 같은 실제 디바이스에서 IoT Edge 게이트웨이를 만들고 실제 카메라를 연결합니다.

이 자습서에서는 다음을 수행하는 방법을 알아봅니다.
> [!div class="checklist"]
> * Azure IoT Central 비디오 분석 애플리케이션 템플릿을 사용하여 소매점 애플리케이션 만들기
> * 애플리케이션 설정 사용자 지정
> * IoT Edge 게이트웨이 디바이스에 대한 디바이스 템플릿 만들기
> * IoT Central 애플리케이션에 게이트웨이 디바이스 추가

## <a name="prerequisites"></a>사전 요구 사항

이 자습서 시리즈를 완료하려면 다음이 필요합니다.

* Azure 구독 Azure 구독이 아직 없는 경우 [Azure 가입 페이지](https://aka.ms/createazuresubscription)에서 만들 수 있습니다.
* 실제 카메라를 사용하는 경우 IoT Edge 디바이스와 카메라를 연결해야 하며, **실시간 스트리밍 프로토콜** 채널이 필요합니다.

## <a name="initial-setup"></a>초기 설정

이 자습서에서는 여러 구성 파일을 업데이트하고 사용합니다. 이러한 파일의 초기 버전은 [LVA-게이트웨이](https://github.com/Azure/live-video-analytics/tree/master/ref-apps/lva-edge-iot-central-gateway) GitHub 리포지토리에서 얻을 수 있습니다. 이 리포지토리에는 배포하는 서비스의 구성 값을 기록하는 데 사용할 수 있는 scratchpad 텍스트 파일도 포함되어 있으며, 이 파일을 다운로드해야 합니다.

로컬 머신에 *lva-configuration*이라는 폴더를 만들고 이러한 파일의 복사본을 저장합니다. 그리고 다음 링크를 각각 마우스 오른쪽 단추로 클릭하고 **다른 이름으로 저장**을 선택하여 파일을 *lva-configuration* 폴더에 저장합니다.

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> GitHub 리포지토리에는 **LvaEdgeGatewayModule** 및 **lvaYolov3** IoT Edge 모듈의 소스 코드도 포함되어 있습니다. 소스 코드를 사용하는 방법에 대한 자세한 내용은 [LVA 게이트웨이 모듈 빌드](tutorial-video-analytics-build-module.md)를 참조하세요.

## <a name="deploy-and-configure-azure-media-services"></a>Azure Media Services 배포 및 구성

이 솔루션에서는 Azure Media Services 계정을 사용하여 IoT Edge 게이트웨이 디바이스에서 만든 개체 감지 비디오 클립을 저장합니다.

Media Services 계정을 만들 때 다음과 같이 해야 합니다.

- 계정 이름, Azure 구독, 위치, 리소스 그룹 및 스토리지 계정을 입력해야 합니다. Media Services 계정을 만들 때 기본 설정을 사용하여 새 스토리지 계정을 만듭니다.

- 지역으로 **미국 동부**를 선택합니다.

- **미국 동부** 지역에 Media Services 및 스토리지 계정에 사용할 *lva-rg*라는 새 리소스 그룹을 만듭니다. 자습서를 마친 후 *lva-rg* 리소스 그룹을 삭제하여 간단하게 모든 리소스를 제거할 수 있습니다.

[Azure Portal에서 Media Services 계정](https://portal.azure.com/?r=1#create/Microsoft.MediaService)을 만듭니다.

> [!TIP]
> 이 자습서의 모든 예제에서 **미국 동부** 지역을 사용합니다. 원한다면 본인과 가장 가까운 지역을 사용해도 됩니다.

*scratchpad.txt* 파일에 **Media Services** 계정 이름을 기록해 둡니다.

배포가 완료되면 **Media Services** 계정의 **속성** 페이지로 이동합니다. *scratchpad.txt* 파일에 **리소스 ID**를 기록해 둡니다. 나중에 IoT Edge 모듈을 구성할 때 이 값을 사용합니다.

다음으로, Media Services 리소스에 대한 Azure Active Directory 서비스 주체를 구성합니다. **API 액세스**를 선택한 다음, **서비스 주체 인증**을 선택합니다. Media Services 리소스와 동일한 이름을 사용하여 새 Azure Active Directory 앱을 만들고, *IoT Edge 액세스*라는 설명을 사용하여 비밀을 만듭니다.

:::image type="content" source="./media/tutorial-video-analytics-create-app/media-service-authentication.png" alt-text="AMS에 대한 AAD 앱 구성":::

비밀이 생성되면 **서비스 보안 주체 애플리케이션에 연결하기 위해 자격 증명 복사** 섹션이 나올 때까지 아래로 스크롤합니다. **JSON**을 선택합니다. 여기서 모든 자격 증명 정보를 한 번에 복사할 수 있습니다. 이 정보를 *scratchpad.txt* 파일에 기록해 둡니다. 나중에 IoT Edge 디바이스를 구성할 때 이 정보를 사용합니다.

> [!WARNING]
> 비밀을 보고 저장할 수 있는 기회는 지금밖에 없습니다. 비밀을 분실하면 다른 비밀을 생성해야 합니다.

## <a name="create-the-azure-iot-central-application"></a>Azure IoT Central 애플리케이션 만들기

이 섹션에서는 템플릿에서 새 Azure IoT Central 애플리케이션을 만듭니다. 자습서 시리즈 전체에서 이 애플리케이션을 사용하여 완전한 솔루션을 빌드합니다.

새로운 Azure IoT Central 애플리케이션을 만들려면:

1. [Azure IoT Central 애플리케이션 관리자](https://aka.ms/iotcentral) 웹 사이트로 이동합니다.

1. Azure 구독에 액세스하는 데 사용하는 자격 증명으로 로그인합니다.

1. 새 Azure IoT Central 애플리케이션 만들기를 시작하려면 **빌드** 페이지에서 **새 애플리케이션**을 선택합니다.

1. **소매**를 선택합니다. 소매 페이지는 여러 소매점 애플리케이션 템플릿을 표시합니다.

새 비디오 분석 애플리케이션을 만드는 방법은 다음과 같습니다.

1. **비디오 분석 - 개체 및 동작 감지** 애플리케이션 템플릿을 선택합니다. 이 템플릿에는 이 자습서에서 사용되는 디바이스용 디바이스 템플릿이 포함되어 있습니다. 템플릿에는 운영자가 카메라 모니터링 및 관리와 같은 작업을 수행하는 데 사용할 수 있는 샘플 대시보드가 포함되어 있습니다.

1. 필요에 따라 친숙한 **애플리케이션 이름**을 선택합니다. 이 애플리케이션은 Northwind Traders라는 가상의 소매점을 기반으로 합니다. 이 자습서에서는 *Northwind Traders 비디오 분석*이라는 **애플리케이션 이름**을 사용합니다.

    > [!NOTE]
    > 친숙한 **애플리케이션 이름**을 사용하는 경우에도 애플리케이션 **URL**에 고유한 값을 사용해야 합니다.

1. Azure 구독이 있는 경우 해당하는 **디렉터리** 및 **Azure 구독**을 선택하고, **위치**로 **미국**을 선택합니다. 구독이 없는 경우 **7일 평가판**을 사용하도록 설정하고 필요한 연락처 정보를 작성할 수 있습니다. 이 자습서에서는 두 개의 디바이스(카메라 2대, IoT Edge 디바이스 하나)를 사용하므로, 평가판을 사용하지 않으면 사용량에 따라 요금이 청구됩니다.

    디렉터리, 구독 및 위치에 대한 자세한 내용은 [애플리케이션 만들기 빠른 시작](../core/quick-deploy-iot-central.md)을 참조하세요.

1. **만들기**를 선택합니다.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/new-application.png" alt-text="Azure IoT Central 애플리케이션 페이지":::

### <a name="retrieve-the-configuration-data"></a>구성 데이터 검색

이 자습서의 뒷부분에서 IoT Edge 게이트웨이를 구성할 때 IoT Central 애플리케이션의 구성 데이터가 필요합니다. IoT Edge 디바이스를 애플리케이션에 등록하고 연결하려면 이 정보가 필요합니다.

**관리** 섹션에서 **사용자의 애플리케이션**을 선택하고 **애플리케이션 URL** 및 **애플리케이션 ID**를 *scratchpad.txt* 파일에 기록해 둡니다.

:::image type="content" source="./media/tutorial-video-analytics-create-app/administration.png" alt-text="관리":::

**API 토큰**을 선택하고 **운영자** 역할에 대한 **LVAEdgeToken**이라는 새 토큰을 생성합니다.

:::image type="content" source="./media/tutorial-video-analytics-create-app/token.png" alt-text="토큰 생성":::

나중에 사용할 수 있도록 *scratchpad.txt* 파일에 토큰을 기록해 둡니다. 대화 상자가 닫힌 후에는 토큰을 다시 볼 수 없습니다.

**관리** 섹션에서 **디바이스 연결**을 선택한 다음, **SAS-IoT-Devices**를 선택합니다.

*scratchpad.txt* 파일에 디바이스의 **기본 키**를 기록해 둡니다. 나중에 IoT Edge 디바이스를 구성할 때 이 *기본 그룹 공유 액세스 서명 토큰*을 사용합니다.

## <a name="edit-the-deployment-manifest"></a>배포 매니페스트 편집

배포 매니페스트를 사용하여 IoT Edge 모듈을 배포합니다. IoT Central에서 매니페스트를 디바이스 템플릿으로 가져온 다음, IoT Central에서 모듈 배포를 관리하게 할 수 있습니다.

배포 매니페스트를 준비하는 방법은 다음과 같습니다.

1. 텍스트 편집기를 사용하여 *lva-configuration* 폴더에 저장한 *deployment.amd64.json* 파일을 엽니다.

1. 다음 코드 조각과 같이 `LvaEdgeGatewayModule` 설정을 찾아서 이미지 이름을 변경합니다.

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. `LvaEdgeGatewayModule` 섹션의 `env` 노드에 Media Services 계정 이름을 추가합니다. 앞에서 *scratchpad.txt* 파일에 Media Services 계정 이름을 기록해 두었습니다.

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. 이 템플릿은 IoT Central에서 이러한 속성을 공개하지 않으므로, 배포 매니페스트에 Media Services 구성 값을 추가해야 합니다. `lvaEdge` 모듈을 찾은 다음, 해당 자리 표시자를 Media Services 계정을 만들 때 *scratchpad.txt* 파일에 기록해 둔 값으로 바꿉니다.

    `azureMediaServicesArmId`는 앞에서 Media Services 계정을 만들 때 *scratchpad.txt* 파일에 기록해 둔 **리소스 ID**입니다.

    앞에서 Media Services 계정의 서비스 주체를 만들 때 *scratchpad.txt* 파일에 `aadTenantId`, `aadServicePrincipalAppId` 및 `aadServicePrincipalSecret`을 기록해 두었습니다.

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. 변경 내용을 저장합니다.

원한다면 Yolov3 모듈을 Intel의 하드웨어 최적화 [OpenVINO&trade; 모델 서버 – Edge AI 확장 모듈](https://github.com/openvinotoolkit/model_server/tree/ams/extras/ams_wrapper)로 바꿀 수 있습니다. 배포 매니페스트 샘플 [deployment.openvino.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json)을 다운로드할 수 있습니다. 이 매니페스트는 `lvaYolov3` 모듈의 구성 설정을 다음 구성으로 바꿉니다.

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

## <a name="create-the-azure-iot-edge-gateway-device"></a>Azure IoT Edge 게이트웨이 디바이스 만들기

비디오 분석 - 개체 및 동작 감지 애플리케이션에는 **LVA Edge 개체 감지기** 디바이스 템플릿과 **LVA Edge 동작 감지** 디바이스 템플릿이 포함되어 있습니다. 이 섹션에서는 배포 매니페스트를 사용하여 게이트웨이 디바이스 템플릿을 만들고, IoT Central 애플리케이션에 게이트웨이 디바이스를 추가합니다.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>LVA Edge 게이트웨이에 대한 디바이스 템플릿 만들기

배포 매니페스트를 가져오고 **LVA Edge 게이트웨이** 디바이스 템플릿을 만드는 방법은 다음과 같습니다.

1. IoT Central 애플리케이션에서 **디바이스 템플릿**으로 이동하고 **+ 새로 만들기**를 선택합니다.

1. **템플릿 유형 선택** 페이지에서 **Azure IoT Edge** 타일을 선택합니다. 그런 다음, **Next: 사용자 지정**을 선택합니다.

1. **Azure IoT Edge 배포 매니페스트 업로드** 페이지에서 템플릿 이름으로 *LVA Edge 게이트웨이*를 입력하고, **다운스트림 디바이스를 사용하는 게이트웨이 디바이스**를 확인합니다.

    아직 배포 매니페스트를 검색하지 마세요. 검색할 경우 배포 마법사에는 각 모듈의 인터페이스가 필요하지만, 우리는 **LvaEdgeGatewayModule**에 대한 인터페이스만 공개해야 합니다. 이후 단계에서 매니페스트를 업로드합니다.

    :::image type="content" source="./media/tutorial-video-analytics-create-app/upload-deployment-manifest.png" alt-text="배포 매니페스트를 다운로드하지 말 것":::

    완료되면 **다음: 검토**를 클릭합니다.

1. **검토** 페이지에서 **만들기**를 선택합니다.

### <a name="import-the-device-capability-model"></a>디바이스 기능 모델 가져오기

디바이스 템플릿에는 디바이스 기능 모델이 포함되어야 합니다. **LVA Edge 게이트웨이** 페이지에서 **기능 모델 가져오기** 타일을 선택합니다. 앞에서 만든 *lva-configuration* 폴더로 이동하여 *LvaEdgeGatewayDcm.json* 파일을 선택합니다.

이제 **LVA Edge 게이트웨이** 디바이스 템플릿에는 **LVA Edge 게이트웨이 모듈**과 함께 **디바이스 정보**, **LVA Edge 게이트웨이 설정** 및 **LVA Edge 게이트웨이 인터페이스**가 포함되어 있습니다.

### <a name="replace-the-manifest"></a>매니페스트 바꾸기

**LVA Edge 게이트웨이** 페이지에서 **+ 매니페스트 바꾸기**를 선택합니다.

:::image type="content" source="./media/tutorial-video-analytics-create-app/replace-manifest.png" alt-text="매니페스트 바꾸기":::

*lva-configuration* 폴더로 이동한 다음, 앞에서 편집한 *deployment.amd64.json* 매니페스트 파일을 선택합니다. **업로드**를 선택합니다. 유효성 검사가 완료되면 **바꾸기**를 선택합니다.

### <a name="add-relationships"></a>관계 추가

**LVA Edge 게이트웨이** 디바이스 템플릿의 **모듈/LVA Edge 게이트웨이 모듈**에서 **관계**를 선택합니다. **+ 관계 추가**를 선택하고 다음 두 관계를 추가합니다.

|표시 이름               |속성          |대상 |
|-------------------------- |------------- |------ |
|LVA Edge 동작 감지기   |기본값 사용   |LVA Edge 동작 감지기 디바이스 |
|LVA Edge 개체 감지기   |기본값 사용   |LVA Edge 개체 감지기 디바이스 |

그런 다음 **저장**을 선택합니다.

:::image type="content" source="media/tutorial-video-analytics-create-app/relationships.png" alt-text="관계 추가":::

### <a name="add-views"></a>뷰 추가

**LVA Edge 게이트웨이** 디바이스 템플릿에는 보기 정의가 포함되지 않습니다.

디바이스 템플릿에 보기를 추가하려면 다음을 수행합니다.

1. **LVA Edge 게이트웨이** 디바이스 템플릿에서 **보기**로 이동하고, **디바이스 시각화** 타일을 선택합니다.

1. 보기 이름으로 *LVA Edge 게이트웨이 디바이스*를 입력합니다.

1. 다음 타일을 보기에 추가합니다.

    * **디바이스 정보** 속성을 포함하는 타일: **디바이스 모델**, **제조업체**, **운영 체제** **프로세서 아키텍처**, **소프트웨어 버전**, **총 메모리**, **총 스토리지**
    * **사용 가능한 메모리** 및 **시스템 하트비트** 원격 분석 값을 포함하는 꺾은선형 차트 타일
    * 다음 이벤트를 포함하는 이벤트 기록 타일: **Create Camera**, **Delete Camera**, **Module Restart**, **Module Started**, **Module Stopped**
    * **IoT Central 클라이언트 상태** 원격 분석을 표시하는 2x1 마지막으로 알려진 값 타일
    * **모듈 상태** 원격 분석을 표시하는 2x1 마지막으로 알려진 값 타일
    * **시스템 하트비트** 원격 분석을 표시하는 1x1 마지막으로 알려진 값 타일
    * **연결된 카메라** 원격 분석을 표시하는 1x1 마지막으로 알려진 값 타일

    :::image type="content" source="media/tutorial-video-analytics-create-app/gateway-dashboard.png" alt-text="대시보드":::

1. **저장**을 선택합니다.

### <a name="publish-the-device-template"></a>디바이스 템플릿 게시

애플리케이션에 디바이스를 추가하려면 먼저 디바이스 템플릿을 게시해야 합니다.

1. **LVA Edge 게이트웨이** 디바이스 템플릿에서 **게시**를 선택합니다.

1. **이 디바이스 템플릿을 애플리케이션에 게시** 페이지에서 **게시**를 선택합니다.

이제 애플리케이션의 **디바이스** 페이지에서 디바이스 유형으로 **LVA Edge 게이트웨이**를 사용할 수 있습니다.

## <a name="add-a-gateway-device"></a>게이트웨이 디바이스 추가

**LVA Edge 게이트웨이** 디바이스를 애플리케이션에 추가하는 방법은 다음과 같습니다.

1. **디바이스** 페이지로 이동하여 **LVA Edge 게이트웨이** 디바이스 템플릿을 선택합니다.

1. **+새로 만들기**를 선택합니다.

1. **새 디바이스 만들기** 대화 상자에서 디바이스 이름을 *LVA Gateway 001*로 변경하고 디바이스 ID를 *lva-gateway-001*로 변경합니다.

    > [!NOTE]
    > 디바이스 ID는 애플리케이션에서 고유해야 합니다.

1. **만들기**를 선택합니다.

디바이스가 상태가 **등록됨**입니다.

### <a name="get-the-device-credentials"></a>디바이스 자격 증명 가져오기

디바이스에서 IoT Central 애플리케이션에 연결할 수 있는 자격 증명이 필요합니다. 디바이스 자격 증명을 가져오려면 다음을 수행합니다.

1. **디바이스** 페이지에서, 앞에서 만든 **lva-gateway-001** 디바이스를 선택합니다.

1. **연결**을 선택합니다.

1. **디바이스 연결** 페이지에서 **ID 범위**, **디바이스 ID** 및 디바이스 **기본 키**를 *scratchpad.txt* 파일에 기록해 둡니다. 이러한 값은 나중에 사용합니다.

1. 연결 방법이 **공유 액세스 서명**으로 설정되어 있는지 확인합니다.

1. **닫기**를 선택합니다.

## <a name="next-steps"></a>다음 단계

**비디오 분석 - 개체 및 동작 감지** 애플리케이션 템플릿을 사용하여 IoT Central 애플리케이션을 만들고, 게이트웨이 디바이스에 대한 디바이스 템플릿을 만들고, 게이트웨이 디바이스를 애플리케이션에 추가했습니다.

시뮬레이션된 비디오 스트림을 사용하는 클라우드 VM을 실행하는 IoT Edge 모듈에서 비디오 분석 - 개체 및 동작 감지 애플리케이션을 사용해 보려면 다음을 수행합니다.

> [!div class="nextstepaction"]
> [비디오 분석용 IoT Edge 인스턴스 만들기(Linux VM)](./tutorial-video-analytics-iot-edge-vm.md)

실제 **ONVIF** 카메라를 사용하는 실제 디바이스를 실행하는 IoT Edge 모듈에서 비디오 분석 - 개체 및 동작 감지 애플리케이션을 사용해 보려면 다음을 수행합니다.

> [!div class="nextstepaction"]
> [비디오 분석용 IoT Edge 인스턴스 만들기(Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md)
