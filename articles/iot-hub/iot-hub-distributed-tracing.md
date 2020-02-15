---
title: 분산 추적 (사전)을 통해 IoT 메시지에 상관 관계 Id 추가
description: 분산 추적 기능을 사용 하 여 솔루션에서 사용 하는 Azure 서비스 전체에서 IoT 메시지를 추적 하는 방법을 알아봅니다.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.openlocfilehash: ed477dddeb499023f4803929d9433ed37c302159
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212479"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>분산 추적(미리 보기)을 사용하여 Azure IoT 디바이스-클라우드 메시지 추적

Microsoft Azure IoT Hub는 현재, 분산 추적을 [미리 보기 기능](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)으로 지원합니다.

IoT Hub는 분산 추적을 지원하기 위한 첫 번째 Azure 서비스 중 하나입니다. 더 많은 Azure 서비스가 분산 추적을 지원하므로 솔루션에 관련된 전체 Azure 서비스에서 IoT 메시지를 추적할 수 있게 될 것입니다. 분산 추적에 대한 배경 정보를 보려면 [분산 추적](../azure-monitor/app/distributed-tracing.md)을 참조하세요.

IoT Hub의 분산 추적을 사용하도록 설정하면 다음과 같은 기능을 얻을 수 있습니다.

- [추적 컨텍스트](https://github.com/w3c/trace-context)를 사용하여 IoT Hub를 통과하는 각 메시지의 흐름을 정확하게 모니터링합니다. 이 추적 컨텍스트는 상관 관계 특정 구성 요소의 이벤트과 다른 구성 요소의 이벤트 간 상관 관계를 파악할 수 있는 상관 관계 ID를 포함합니다. 이 컨텍스트를 [디바이스 쌍](iot-hub-devguide-device-twins.md)을 사용하여 IoT 디바이스 메시지 일부 또는 전체에 적용할 수 있습니다.
- 추적 컨텍스트를 [Azure Monitor 진단 로그](iot-hub-monitor-resource-health.md)에 자동으로 기록합니다.
- 디바이스에서 IoT Hub 및 라우팅 엔드포인트로의 메시지 흐름과 대기 시간을 측정하고 이해합니다.
- 먼저 IoT 솔루션에서 비 Azure 서비스에 대해 분산 추적을 구현하는 방법을 고려합니다.

이 문서에서는 분산 추적과 함께 [C용 Azure IoT 디바이스 SDK](iot-hub-device-sdk-c-intro.md)를 사용합니다. 다른 SDK의 경우 분산 추적 지원이 아직 진행 중입니다.

## <a name="prerequisites"></a>사전 요구 사항

- 분산 추적의 미리 보기는 현재 다음 지역에서 만든 IoT Hub에 대해서만 지원됩니다.

  - **북유럽**
  - **동남아시아**
  - **미국 서부 2**

- 이 문서에서는 사용자가 IoT Hub로 원격 분석 메시지를 전송하는 방법을 잘 알고 있다고 가정합니다. [원격 분석 전송 C 빠른 시작](quickstart-send-telemetry-c.md)을 완료해야 합니다.

- IoT Hub에 디바이스를 등록하고(각 빠른 시작에서 단계 사용 가능) 연결 문자열을 기록해 둡니다.

- 최신 버전의 [Git](https://git-scm.com/download/)을 설치합니다.

## <a name="configure-iot-hub"></a>IoT Hub 구성

이 섹션에서는 분산 추적 특성(상관 관계 ID 및 타임스탬프)을 기록하도록 IoT Hub를 구성합니다.

1. [Azure Portal](https://portal.azure.com/)에서 IoT Hub로 이동합니다.

1. IoT Hub의 왼쪽 창에서 아래의 **모니터링** 섹션으로 스크롤하고 **진단 설정**을 클릭합니다.

1. 진단 설정을 아직 켜지 않은 경우 **진단 켜기**를 클릭합니다. 진단 설정을 이미 사용하도록 설정한 경우 **진단 설정 추가**를 클릭합니다.

1. **이름** 필드에 새 진단 설정의 이름을 입력합니다. 예를 들면 **DistributedTracingSettings**와 같습니다.

1. 로깅 전송 위치를 결정하는 다음 옵션 중 하나 이상을 선택합니다.

    - **저장소 계정에 보관**: 로깅 정보를 포함 하도록 저장소 계정을 구성 합니다.
    - **이벤트 허브로 스트림**: 로깅 정보를 포함 하도록 이벤트 허브를 구성 합니다.
    - **Log Analytics 보내기**: 로깅 정보를 포함 하도록 Log Analytics 작업 영역을 구성 합니다.

1. **로그** 섹션에서 로깅 정보를 사용하려는 작업을 선택합니다.

    **DistributedTracing**을 포함하고 **보존**에서 로깅을 보존하려는 기간(일)을 구성합니다. 로그 보존은 스토리지 비용에 영향을 주지 않습니다.

    ![IoT 진단 설정에 대해 DistributedTracing 범주가 지정된 것을 보여 주는 스크린샷](./media/iot-hub-distributed-tracing/diag-logs.png)

1. 새 설정에 대해 **저장**을 클릭합니다.

1. (선택 사항) 다른 위치에 대한 메시지 흐름을 보려면 [두 개 이상의 다른 엔드포인트에 대한 라우팅 규칙](iot-hub-devguide-messages-d2c.md)을 설정합니다.

로깅을 켜지면 IoT Hub는 다음과 같은 상황에서 유효한 추적 속성을 포함하는 메시지가 발생할 경우 로그를 기록합니다.

- 메시지가 IoT Hub 게이트웨이에 도착합니다.
- 메시지가 IoT Hub에서 처리됩니다.
- 메시지가 사용자 지정 엔드포인트로 라우팅됩니다. 라우팅을 사용하도록 설정해야 합니다.

이러한 로그 및 해당 스키마에 대한 자세한 내용은 [IoT Hub 진단 로그에 분산 추적](iot-hub-monitor-resource-health.md#distributed-tracing-preview)을 참조하세요.

## <a name="set-up-device"></a>디바이스 설정

이 섹션에서는 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)를 빌드하는 데 사용할 개발 환경을 준비합니다. 그런 다음, 샘플 중 하나를 수정하여 디바이스의 원격 분석 메시지에서 분산 추적을 사용하도록 설정합니다.

이러한 지침은 Windows에서 샘플을 빌드하기 위한 것입니다. 다른 환경에 대해서는 [C SDK 컴파일](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) 또는 [플랫폼 특정 개발용 사전 패키지 C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development)를 참조하세요.

### <a name="clone-the-source-code-and-initialize"></a>소스 코드를 복제 및 초기화

1. Visual Studio 2019에 대 한 ["데스크톱 개발 C++" 워크 로드](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) 를 설치 합니다. Visual Studio 2017 및 2015도 지원 됩니다.

1. [CMake](https://cmake.org/)를 설치합니다. 명령 프롬프트에서 `PATH`을 입력하여 `cmake -version`에 있는지 확인합니다.

1. 명령 프롬프트 또는 Git Bash 셸을 엽니다. 다음 명령을 실행 하 여 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 리포지토리의 최신 릴리스를 복제 합니다.

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    이 작업을 완료하는 데 몇 분 정도가 걸립니다.

1. Git 리포지토리의 루트 디렉터리에서 `cmake` 하위 디렉터리를 만들고 해당 폴더로 이동합니다. `azure-iot-sdk-c` 디렉터리에서 다음 명령을 실행합니다.

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    `cmake`에서 C++ 컴파일러를 찾을 수 없으면 위의 명령을 실행하는 동안 빌드 오류가 발생할 수 있습니다. 이 경우에는 [Visual Studio 명령 프롬프트](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)에서 이 명령을 실행합니다. 

    빌드가 성공되면 마지막 몇몇 출력 줄은 다음 출력과 유사하게 표시됩니다.

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>분산 추적을 사용하도록 원격 분석 보내기 샘플 편집

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">Github의 샘플 가져오기</a>

1. 편집기를 사용하여 `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` 소스 파일을 엽니다.

1. `connectionString` 상수의 선언을 찾습니다.

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    `connectionString` 상수 값을 [원격 분석 보내기 C 빠른 시작](./quickstart-send-telemetry-c.md#register-a-device)의 [디바이스 등록](./quickstart-send-telemetry-c.md) 섹션에서 기록해 둔 디바이스 연결 문자열로 바꿉니다.

1. `MESSAGE_COUNT` 정의를 `5000`으로 변경합니다.

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. `IoTHubDeviceClient_LL_SetConnectionStatusCallback`을 호출하는 코드의 줄을 찾아 메시지 보내기 루프 이전에 연결 상태 콜백 함수를 등록합니다. 해당 줄 아래에 아래와 같이 코드를 추가하여 디바이스의 분산 추적을 사용하도록 설정하는 `IoTHubDeviceClient_LL_EnablePolicyConfiguration`을 호출합니다.

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    `IoTHubDeviceClient_LL_EnablePolicyConfiguration` 함수를 사용하여 [디바이스 쌍](./iot-hub-devguide-device-twins.md)을 통해 구성된 특정 IoT Hub 기능에 대한 정책을 사용하도록 설정합니다. 위 코드 줄을 사용하여 `POLICY_CONFIGURATION_DISTRIBUTED_TRACING`을 사용하도록 설정하면, 디바이스의 추적 동작이 디바이스 쌍에 대해 수행된 분산 추적 변경 내용을 반영합니다.

1. 할당량을 다 사용하지는 않으면서 샘플 앱을 계속 실행하려면 메시지 보내기 루프 끝에 1초의 지연을 추가합니다.

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>컴파일 및 실행

1. 이전에 만든 CMake 디렉터리( *)에서* iothub_ll_telemetry_sample`azure-iot-sdk-c/cmake` 프로젝트 디렉터리로 이동한 후 샘플을 컴파일합니다.

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. 애플리케이션을 실행합니다. 디바이스는 분산 추적을 지원하는 원격 분석을 보냅니다.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. 앱이 계속 실행되게 합니다. 필요에 따라 콘솔 창을 확인하여 IoT Hub로 보내는 메시지를 관찰합니다.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>타사 클라이언트에 대 한 해결 방법

C SDK를 사용 하지 않고도 분산 추적 **기능을 미리 볼 수 있습니다** . 따라서이 방법은 사용 하지 않는 것이 좋습니다.

먼저 개발자 가이드 [IoT Hub 메시지 만들기 및 읽기](iot-hub-devguide-messages-construct.md)를 따라 메시지의 모든 IoT Hub 프로토콜 기본 형식을 구현 해야 합니다. 그런 다음 MQTT/AMQP 메시지의 프로토콜 속성을 편집 하 여 `tracestate`를 **시스템 속성**으로 추가 합니다. 특히,

* MQTT의 경우 메시지 항목에 `%24.tracestate=timestamp%3d1539243209`를 추가 합니다. 여기서 `1539243209`는 unix 타임 스탬프 형식으로 메시지를 만든 시간으로 바꾸어야 합니다. 예를 들어 [C SDK의](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761) 구현을 참조 하세요.
* AMQP의 경우 `key("tracestate")` 및 `value("timestamp=1539243209")`를 메시지 주석으로 추가 합니다. 참조 구현은 [여기](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527)를 참조 하십시오.

이 속성을 포함하는 메시지의 비율을 제어하려는 경우 쌍 업데이트와 같은 클라우드 시작 이벤트를 수신 대기하도록 논리를 구현합니다.

## <a name="update-sampling-options"></a>샘플링 옵션 업데이트 

클라우드에서 추적할 메시지의 비율을 변경하려면 디바이스 쌍을 업데이트해야 합니다. 포털의 JSON 편집기 및 IoT Hub 서비스 SDK를 포함하는 다양한 방법으로 이 작업을 수행할 수 있습니다. 다음 하위 섹션에서 예제를 제공합니다.

### <a name="update-using-the-portal"></a>포털을 사용하여 업데이트

1. [Azure Portal](https://portal.azure.com/)에서 IoT Hub로 이동한 후 **IoT 디바이스**를 클릭합니다.

1. 디바이스를 클릭합니다.

1. **분산 추적(미리 보기) 사용**을 찾은 후 **사용**을 선택합니다.

    ![Azure Portal에서 분산 추적 사용](./media/iot-hub-distributed-tracing/azure-portal.png)

1. 0%과 100% 사이의 **샘플링 주기**를 선택합니다.

1. **저장**을 클릭합니다.

1. 몇 초 정도 기다렸다가 **새로 고침**을 누릅니다. 디바이스에서 성공적으로 승인하면 확인 표시가 있는 동기화 아이콘이 나타납니다.

1. 원격 분석 메시지 앱에 대한 콘솔 창으로 돌아갑니다. 애플리케이션 속성에서 `tracestate`와 함께 전송된 메시지를 볼 수 있습니다.

    ![추적 상태](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (선택 사항) 샘플링 비율을 다른 값으로 변경하고 애플리케이션 속성에서 메시지가 `tracestate`를 포함하는 빈도의 변화를 확인합니다.

### <a name="update-using-azure-iot-hub-for-vs-code"></a>VS Code에 대 한 Azure IoT Hub를 사용 하 여 업데이트

1. VS Code를 설치 하 고 [여기](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)에서 VS Code에 대 한 Azure IoT Hub 최신 버전을 설치 합니다.

1. VS Code를 열고 [IoT Hub 연결 문자열을 설정](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites)합니다.

1. 디바이스를 확장하고 **분산 추적 설정(미리 보기)** 을 찾습니다. 이 설정 아래에서 하위 노드의 **분산 추적 설정(미리 보기) 업데이트**를 클릭합니다.

    ![Azure IoT Hub 확장에서 분산 추적 사용](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. 팝업 창에서 **사용**을 선택한 후 Enter 키를 눌러 샘플링 비율이 100인지 확인합니다.

    ![샘플링 모드 업데이트](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![샘플링 속도 업데이트](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>여러 디바이스에 대해 대량 업데이트

여러 디바이스에 대해 분산 추적 샘플링 구성을 업데이트하려면 [자동 디바이스 구성](iot-hub-auto-device-config.md)을 사용합니다. 다음과 같은 쌍 스키마를 따라야 합니다.

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| 요소 이름 | 필수 | Type | Description |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | yes | 정수 | 샘플링을 켜고 끄기 위해 현재 두 가지 모드 값이 지원됩니다. `1`은 켜짐이고 `2`는 꺼짐입니다. |
| `sampling_rate` | yes | 정수 | 이 값은 백분율입니다. `0`~`100`(경계값 포함) 사이의 값만 허용됩니다.  |

## <a name="query-and-visualize"></a>쿼리 및 시각화

IoT Hub에서 기록된 모든 추적을 보려면 진단 설정에서 선택한 로그 저장소를 쿼리합니다. 이 섹션에서는 몇 가지 다른 옵션을 설명합니다.

### <a name="query-using-log-analytics"></a>Log Analytics를 사용한 쿼리

[진단 로그를 사용하여 Log Analytics](../azure-monitor/platform/resource-logs-collect-storage.md)를 설정한 경우 `DistributedTracing` 범주에서 로그를 찾아 쿼리합니다. 예를 들어 이 쿼리는 기록된 모든 추적을 보여 줍니다.

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Log Analytics에 표시된 예제 로그:

| TimeGenerated | OperationName | Category | Level | CorrelationId | DurationMs | 속성 |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | DistributedTracing | 정보 제공 | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress | DistributedTracing | 정보 제공 | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | DistributedTracing | 정보 제공 | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

다양한 유형의 로그를 이해하려면 [Azure IoT Hub 진단 로그](iot-hub-monitor-resource-health.md#distributed-tracing-preview)를 참조하세요.

### <a name="application-map"></a>애플리케이션 맵

IoT 메시지의 흐름을 시각화하기 위해 애플리케이션 맵 샘플 앱을 설정합니다. 샘플 앱은 Azure Function 및 Event Hub를 사용하여 분산 추적 로그를 [애플리케이션 맵](../application-insights/app-insights-app-map.md)으로 보냅니다.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Github의 샘플 가져오기</a>

아래 이미지는 다음과 같은 3개의 라우팅 엔드포인트가 있는 앱 지도의 분산 추적을 보여 줍니다.

![앱 지도의 IoT 분산 추적](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Azure IoT 분산 추적 이해

### <a name="context"></a>Context

고유한 [참조 아키텍처](https://aka.ms/iotrefarchitecture)(영문만 지원)를 포함하는 많은 IoT 솔루션은 일반적으로 [마이크로 서비스 아키텍처](https://docs.microsoft.com/azure/architecture/microservices/)의 변형을 따릅니다. IoT 솔루션이 좀 더 복잡해지면서 결과적으로 수십 개가 넘는 마이크로 서비스를 사용하게 됩니다. 이러한 마이크로 서비스는 Azure에서 제공된 것일 수도 있고 그렇지 않을 수도 있습니다. IoT 메시지가 삭제되거나 느려지는 지점을 찾아내는 일은 어려울 수 있습니다. 예를 들어, 5개의 다른 Azure 서비스와 1,500개의 활성 디바이스를 사용하는 IoT 솔루션이 있을 수 있습니다. 각 디바이스는 초당 10개의 디바이스-클라우드 메시지(초당 총 15,000개 메시지)를 보내지만, 웹앱에는 초당 10,000개의 메시지가 표시되는 것으로 확인되었습니다. 문제가 무엇인가요? 원인은 어떻게 찾을 수 있을까요?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>마이크로 서비스 아키텍처의 분산 추적 패턴

여러 서비스에서 IoT 메시지의 흐름을 다시 구성하려면 각 서비스가 메시지를 고유하게 식별하는 *상관 관계 ID*를 전파해야 합니다. 중앙 집중식 시스템에서 수집된 상관 관계 ID를 사용하면 메시지 흐름을 볼 수 있습니다. 이 방법을 [분산 추적 패턴](https://docs.microsoft.com/azure/architecture/microservices/logging-monitoring#distributed-tracing)이라고 합니다.

분산 추적이 보다 광범위하게 채택되도록 하기 위해 Microsoft는 [분산 추적에 대한 W3C 표준 제안](https://w3c.github.io/trace-context/)에 참가하고 있습니다.

### <a name="iot-hub-support"></a>IoT Hub 지원

설정된 IoT Hub에 대한 분산 추적 지원은 다음 흐름을 따릅니다.

1. 메시지가 IoT 디바이스에서 생성됩니다.
1. IoT 디바이스가 클라우드의 지원을 받아, 이 메시지에 추적 컨텍스트를 할당해야 하는지 결정합니다.
1. SDK가 메시지 애플리케이션 속성에 메시지 생성 타임스탬프를 포함하는 `tracestate`를 추가합니다.
1. IoT 디바이스는 IoT Hub에 메시지를 보냅니다.
1. 메시지가 IoT Hub 게이트웨이에 도착합니다.
1. IoT Hub가 메시지 애플리케이션 속성에서 `tracestate`를 찾고 올바른 형식인지 확인합니다.
1. 형식이 올바르면 IoT Hub는 `trace-id` 및 `span-id`를 생성한 후 Azure Monitor 진단 로그의 `DiagnosticIoTHubD2C` 범주 아래에 기록합니다.
1. 메시지 처리가 완료되면 IoT Hub는 다른 `span-id`를 생성하고 `trace-id` 범주 아래에 기존 `DiagnosticIoTHubIngress`와 함께 기록합니다.
1. 메시지에 대해 라우팅을 사용하도록 설정하면, IoT Hub는 사용자 지정 엔드포인트에 쓰고, `span-id` 범주 아래에 동일한 `trace-id`를 사용하여 다른 `DiagnosticIoTHubEgress`를 기록합니다.
1. 위 단계는 생성된 각 메시지에 대해 반복됩니다.

## <a name="public-preview-limits-and-considerations"></a>공개 미리 보기 제한 및 고려 사항

- W3C 추적 컨텍스트 표준에 대한 제안은 현재 초안 작업 중입니다.
- 현재, 클라이언트 SDK에서 지원하는 유일한 개발 언어는 C입니다.
- [IoT Hub 기본 계층](iot-hub-scaling.md#basic-and-standard-tiers)에서는 클라우드-디바이스 쌍 기능을 사용할 수 없습니다. 그러나 IoT Hub는 적절히 구성된 추적 컨텍스트 헤더가 있으면 Azure Monitor에 기록됩니다.
- 효율적인 작업을 위해 IoT Hub는 분산 추적의 일부로 발생할 수 있는 로깅 속도를 제한합니다.

## <a name="next-steps"></a>다음 단계

- 마이크로 서비스의 일반 분산 추적 패턴에 대한 자세한 내용은 [마이크로 서비스 아키텍처 패턴: 분산 추적](https://microservices.io/patterns/observability/distributed-tracing.html)을 참조하세요.
- 많은 수의 디바이스에 분산 추적 설정을 적용하는 구성을 설정하려면 [대규모로 IoT 디바이스 구성 및 모니터링](iot-hub-auto-device-config.md)을 참조하세요.
- Azure Monitor에 대한 자세한 내용은 [Azure Monitor 란?](../azure-monitor/overview.md)을 참조하세요.
