---
title: IoT Edge 장치에 라이브 비디오 분석 배포-Azure
description: 이 문서에서는 IoT Edge 장치에 라이브 비디오 분석을 배포 하는 데 도움이 되는 단계를 나열 합니다. 예를 들어 로컬 Linux 컴퓨터에 대 한 액세스 권한이 있거나 이전에 Azure Media Services 계정을 만든 경우이 작업을 수행할 수 있습니다.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: 774fdb440307d0df92e9735a8bdf055687f450a2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684102"
---
# <a name="deploy-live-video-analytics-on-an-iot-edge-device"></a>IoT Edge 장치에 라이브 비디오 분석 배포

이 문서에서는 IoT Edge 장치에 라이브 비디오 분석을 배포 하는 데 도움이 되는 단계를 나열 합니다. 예를 들어 로컬 Linux 컴퓨터에 대 한 액세스 권한이 있거나 이전에 Azure Media Services 계정을 만든 경우이 작업을 수행할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* 라이브 비디오 분석의 HW/SW 제약 조건을 충족 하는 Linux 컴퓨터
* [소유자 권한이](../../role-based-access-control/built-in-roles.md#owner) 있는 Azure 구독
* [IoT Hub 만들기 및 설정](../../iot-hub/iot-hub-create-through-portal.md)
* [IoT Edge 장치 등록](../../iot-edge/how-to-register-device.md)
* [Debian 기반 Linux 시스템에서 Azure IoT Edge 런타임 설치](../../iot-edge/how-to-install-iot-edge-linux.md)
* [Azure Media Services 계정 만들기](../latest/create-account-howto.md)

    * 미국 동부 2, 미국 중부, 미국 중 북부, 일본 동부, 미국 서 부 2, 미국 중부, 캐나다 동부, 영국 남부, 프랑스 중부, 프랑스 남부, 스위스 북부, 스위스 서부, 일본 서 부 지역 중 하나를 사용 합니다.
    * 범용 v2 (GPv2) 저장소 계정을 사용 하는 것이 좋습니다.

## <a name="configuring-azure-resources-for-using-live-video-analytics"></a>Live Video Analytics를 사용 하 여 Azure 리소스 구성

### <a name="create-custom-azure-resource-manager-role"></a>사용자 지정 Azure Resource Manager 역할 만들기

[사용자 지정 Azure Resource Manager 역할 만들기](create-custom-azure-resource-manager-role-how-to.md) 및 Live Video Analytics 사용을 위한 서비스 주체에 할당을 참조 하세요.

### <a name="set-up-a-premium-streaming-endpoint"></a>프리미엄 스트리밍 끝점 설정

라이브 비디오 분석을 사용 하 여 계속 해 서 클라우드에 비디오를 기록 하 고 나중에 [쿼리 api](playback-recordings-how-to.md#query-api) 를 사용 하려는 경우에는 [프리미엄 스트리밍 끝점](../latest/streaming-endpoint-concept.md#types)을 사용 하도록 미디어 서비스를 업데이트 하는 것이 좋습니다.  

선택적 단계입니다. 이 Azure CLI 명령을 사용 하 여이 작업을 수행할 수 있습니다.

```azure-cli
az ams streaming-endpoint scale --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --scale-units 1
```

이 명령을 사용 하 여 스트리밍 끝점을 시작할 수 있습니다. 

> [!IMPORTANT]
> 이 시점에서 구독이 청구 되기 시작 합니다.

```azure-cli
az ams streaming-endpoint start --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --no-wait
```

미디어 서비스 api에 액세스 하기 위한 자격 증명을 가져오려면이 문서의 단계를 따르세요. [미디어 서비스 api에 액세스](../latest/access-api-howto.md?tabs=portal) 하 고 포털 탭을 선택 합니다.

## <a name="create-and-use-local-user-account-for-deployment"></a>배포를 위한 로컬 사용자 계정 만들기 및 사용
IoT Edge 모듈에서 Live Video Analytics를 실행 하려면 가능한 적은 수의 권한으로 로컬 사용자 계정을 만듭니다. 예를 들어 Linux 컴퓨터에서 다음 명령을 실행 합니다.

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

## <a name="granting-permissions-to-device-storage"></a>장치 저장소에 대 한 사용 권한 부여

이제 로컬 사용자 계정을 만들었습니다. 

* 응용 프로그램 구성 데이터를 저장할 로컬 폴더가 필요 합니다. 폴더를 만들고 localuser 계정에 다음 명령을 사용 하 여 해당 폴더에 대 한 쓰기 권한을 부여 합니다.

```
sudo mkdir /var/lib/azuremediaservices
sudo chown -R edgeuser /var/lib/azuremediaservices
```

* 또한 [로컬 파일에 비디오를 기록](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources)하는 폴더가 필요 합니다. 다음 명령을 사용 하 여 동일한에 대 한 로컬 폴더를 만듭니다.

```
sudo mkdir /var/media
sudo chown -R edgeuser /var/media
```

## <a name="deploy-live-video-analytics-edge-module"></a>Live Video Analytics Edge 모듈 배포

<!-- (To JuliaKo: this is similar to https://docs.microsoft.com/azure/iot-edge/how-to-deploy-blob)-->
IoT Edge의 Live Video Analytics는 [모듈 쌍 구성 스키마](module-twin-configuration-schema.md)에 설명 된 모듈 쌍 속성을 노출 합니다. 

### <a name="deploy-using-the-azure-portal"></a>Azure Portal을 사용하여 배포

Azure Portal 배포 매니페스트를 만들고 배포를 IoT Edge 장치로 푸시하는 과정을 안내 합니다.  
#### <a name="select-your-device-and-set-modules"></a>장치를 선택 하 고 모듈을 설정 합니다.

1. [Azure Portal](https://ms.portal.azure.com/)에 로그인하고 IoT Hub로 이동합니다.
1. 메뉴에서 **IoT Edge**를 선택합니다.
1. 디바이스 목록에서 대상 디바이스의 ID를 클릭합니다.
1. **모듈 설정**을 선택 합니다.

#### <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성을 설명하는 JSON 문서입니다. Azure Portal에는 배포 매니페스트를 만드는 과정을 안내 하는 마법사가 있습니다. 이 클래스는 **모듈**, **경로**및 **검토 + 만들기**의 세 단계로 구성 됩니다.

#### <a name="add-modules"></a>모듈 추가

1. 페이지의 **IoT Edge 모듈** 섹션에서 **추가** 드롭다운을 클릭 하 고 **IoT Edge 모듈** 을 선택 하 여 **IoT Edge 모듈 추가** 페이지를 표시 합니다.
1. **모듈 설정** 탭에서 모듈의 이름을 입력 한 다음 컨테이너 이미지 URI를 지정 합니다.   
    예:
    
    * **IoT Edge 모듈 이름**: lvaEdge
    * **이미지 URI**: mcr.microsoft.com/media/live-video-analytics:1.0    
    
    ![추가](./media/deploy-iot-edge-device/add.png)
    
    > [!TIP]
    > 이 절차에 설명 된 대로 **모듈 설정**, **컨테이너 만들기 옵션**및 **모듈 쌍 설정** 탭에서 값을 지정할 때까지 **추가** 를 선택 하지 마세요.
    
    > [!WARNING]
    > 모듈에 대 한 호출을 수행할 때 Azure IoT Edge는 대/소문자를 구분 합니다. 모듈 이름으로 사용 하는 정확한 문자열을 기록해 둡니다.

1. **환경 변수** 탭을 엽니다.
   
   환경 변수가 표시 되는 입력 상자에 다음 값을 추가 합니다. ![](./media/deploy-iot-edge-device/environment-variables.png) 

1. **컨테이너 만들기 옵션** 탭을 엽니다.

    ![컨테이너 만들기 옵션](./media/deploy-iot-edge-device/container-create-options.png)
 
    다음 JSON을 복사 하 여 상자에 붙여넣어 모듈에서 생성 되는 로그 파일의 크기를 제한 합니다.
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
               "/var/media:/var/media"
            ]
        }
    }
    ````
   
   JSON의 "바인드" 섹션에는 두 개의 항목이 있습니다.
   1. "/var/lib/azuremediaservices:/var/lib/azuremediaservices": 컨테이너에서 영구 응용 프로그램 구성 데이터를 바인딩하고에 지 장치에 저장 하는 데 사용 됩니다.
   1. "/var/sermedia:/var/sermedia": edge 장치와 컨테이너 사이에 미디어 폴더를 바인딩합니다. Edge 장치에 비디오 클립을 저장할 수 있도록 지 원하는 미디어 그래프 토폴로지를 실행할 때 비디오 기록을 저장 하는 데 사용 됩니다.
   
1. 모듈 쌍 **설정** 탭에서 다음 JSON을 복사 하 여 상자에 붙여넣습니다.
 
    ![쌍 설정](./media/deploy-iot-edge-device/twin-settings.png)

    IoT Edge의 라이브 비디오 분석에는 [모듈 쌍 구성 스키마](module-twin-configuration-schema.md)에 나열 된 대로 실행 하기 위해 필수 쌍 속성 집합이 필요 합니다.  

    모듈 쌍 설정 편집 상자에 입력 해야 하는 JSON은 다음과 같습니다.    
    ```
    {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{AMS-account-name}",
        "aadTenantId": "{the-ID-of-your-tenant}",
        "aadServicePrincipalAppId": "{the-ID-of-the-service-principal-app-for-ams-account}",
        "aadServicePrincipalSecret": "{secret}"
    }
    ```
    이러한 속성은 위의 JSON에 대 한 **필수** 속성입니다.  
    * {subscriptionID}-Azure 구독 ID입니다.
    * {resourceGroupName}-미디어 서비스 계정이 속한 리소스 그룹
    * {AMS-account-name}-Media Services 계정의 이름입니다.
    
    다른 값을 얻으려면 [액세스 AZURE MEDIA SERVICES API](../latest/access-api-howto.md?tabs=portal) 를 참조 하 고 포털 탭을 선택 합니다.  
    * aadTenantId-테 넌 트의 ID 이며 위의 링크에서 "AadTenantId"와 동일 합니다.
    * aadServicePrincipalAppId-미디어 서비스 계정에 대 한 서비스 주체의 앱 ID 이며 위 링크의 "AadClientId"와 동일 합니다.
    * aadServicePrincipalSecret-서비스 사용자의 암호 이며 위의 링크에서 "AadSecret"와 동일 합니다.

    다음은 JSON에 추가할 수 있는 몇 가지 추가 **권장** 속성 이며 모듈을 모니터링 하는 데 도움이 됩니다. 자세한 내용은 [모니터링 및 로깅](monitoring-logging.md)을 참조 하세요.
    
    ```
    "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
    "OperationalEventsOutputName": "lvaEdgeOperational",
    "logLevel": "Information",
    "logCategories": "Application,Events"
    ```
    
    다음은 JSON에 추가할 수 있는 몇 가지 **선택적** 속성입니다.
    
    ```
    "aadEndpoint": "https://login.microsoftonline.com",
    "aadResourceId": "https://management.core.windows.net/",
    "armEndpoint": "https://management.azure.com/",
    "allowUnsecuredEndpoints": true
    ```

   > [!Note]
   > 쌍 속성 **allowUnsecuredEndpoints** 는 자습서 및 빠른 시작의 목적으로 true로 설정 됩니다.   
   프로덕션 환경에서 실행 하는 경우이 속성을 **false** 로 설정 해야 합니다. 이렇게 하면 응용 프로그램이 보안 되지 않은 모든 끝점을 차단 하 고 그래프 토폴로지를 실행 하기 위해 유효한 연결 자격 증명이 필요 합니다.  
   
    추가를 선택 하 여 모듈 쌍 속성을 추가 합니다.
1. **다음:** 경로를 선택 하 여 경로 섹션으로 이동 합니다.
    경로를 지정 하십시오.

기본 경로를 유지 하 고 다음을 선택 합니다 **. 검토 + 만들기** 를 선택 하 여 검토 섹션으로 이동 합니다.

#### <a name="review-deployment"></a>배포 검토

검토 섹션에서는 이전 두 개의 섹션에서 선택한 항목에 따라 생성된 JSON 배포 매니페스트를 보여줍니다. 또한 $edgeAgent 및 $edgeHub를 추가 하지 않은 두 개의 모듈도 선언 됩니다. 이 두 개의 모듈은 IoT Edge 런타임을 구성하며 모든 배포에서 필수 기본값입니다.

배포 정보를 검토 한 다음 만들기를 선택 합니다.

### <a name="verify-your-deployment"></a>배포 확인

배포를 만든 후에는 IoT hub의 IoT Edge 페이지로 돌아갑니다.

1. 해당 세부 정보를 열려면 배포할 대상으로 지정한 IoT Edge 디바이스를 선택합니다.
2. 디바이스 세부 정보에서 Blob Storage 모듈이 **배포에 지정됨 및 디바이스에서 보고됨** 모두로 나열되어 있는지를 확인합니다.

모듈을 디바이스에서 시작한 다음, IoT Hub에 다시 보고하려면 몇 분 정도 걸릴 수 있습니다. 업데이트된 상태를 보려면 페이지를 새로 고칩니다.
상태 코드: 200 – OK는 [IoT Edge 런타임이](../../iot-edge/iot-edge-runtime.md) 정상 이며 제대로 작동 하 고 있음을 의미 합니다.

![상태](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>직접 메서드 호출

다음으로, 직접 메서드를 호출 하 여 샘플을 테스트할 수 있습니다. LvaEdge 모듈에서 제공 하는 모든 직접 메서드를 이해 하기 위해 [IoT Edge의 Live Video Analytics에 대 한 직접 메서드](direct-methods.md) 를 읽어 보세요.

1. 사용자가 만든 edge 모듈을 클릭 하면 해당 구성 페이지로 이동 합니다.  

    ![모듈](./media/deploy-iot-edge-device/modules.png)
1. 직접 메서드 메뉴 옵션을 클릭 합니다.

    > [!NOTE] 
    > 현재 페이지에서 볼 수 있는 것 처럼 연결 문자열 섹션에 값을 추가 해야 합니다. **설정 이름** 섹션에서 아무것도 숨기 거 나 변경할 필요가 없습니다. 공개하는 것도 괜찮습니다.

    ![직접 메서드](./media/deploy-iot-edge-device/module-details.png)
1. 그런 다음 메서드 이름 상자에 "GraphTopologyList"를 입력 합니다.
1. 다음으로 페이로드 상자에 아래 JSON 페이로드를 복사 하 여 붙여넣습니다.
    
    ```
    {
        "@apiVersion" : "1.0"
    }
    ```
1. 페이지 맨 위에 있는 "메서드 호출" 옵션을 클릭 합니다.

    ![직접 메서드](./media/deploy-iot-edge-device/direct-method.png)
1. 결과 상자에 상태 200 메시지가 표시 됩니다.

    ![상태 200 메시지](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>다음 단계

[빠른 시작: IoT Edge 시작 하기-라이브 비디오 분석을](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device) 수행 합니다.

> [!TIP]
> 명령에서 다음을 실행 하 고 기본값 대신을 사용 `device-id` `lva-sample-device` 합니다.
