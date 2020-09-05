---
title: 엔드투엔드 솔루션 연결
titleSuffix: Azure Digital Twins
description: 디바이스 데이터를 기반으로 하는 엔드투엔드 Azure Digital Twins 솔루션을 구축하는 자습서입니다.
author: baanders
ms.author: baanders
ms.date: 4/15/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 0407046dcafb0dcc1872d5083669e09b378a75cd
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87827346"
---
# <a name="build-out-an-end-to-end-solution"></a>엔드투엔드 솔루션 빌드

환경의 라이브 데이터를 기반으로 전체 엔드투엔드 솔루션을 설정하기 위해 디바이스 및 데이터 관리를 위해 다른 Azure 서비스에 Azure Digital Twins 인스턴스를 연결할 수 있습니다.

이 자습서에서는 다음을 수행합니다.
* Azure Digital Twins 인스턴스 설정
* 샘플 빌딩 시나리오에 대해 알아보고 미리 작성된 구성 요소를 인스턴스화합니다.
* [Azure Functions](../azure-functions/functions-overview.md) 앱을 사용하여 [IoT Hub](../iot-hub/about-iot-hub.md) 디바이스에서 시뮬레이션된 원격 분석을 디지털 트윈 속성으로 라우팅합니다.
* Azure Functions, 엔드포인트 및 경로를 사용하여 디지털 트윈 알림을 처리하여 **트윈 그래프**를 통해 변경 내용을 전파합니다.

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell 세션 설정
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="get-started-with-the-building-scenario"></a>빌딩 시나리오 시작

이 자습서에서 사용되는 샘플 프로젝트는 층, 방 및 자동 온도 조절기 디바이스를 포함하는 실제 **빌딩 시나리오**를 나타냅니다. 이러한 구성 요소는 Azure Digital Twins 인스턴스에 디지털로 표시되며, 이 인스턴스는 [IoT Hub](../iot-hub/about-iot-hub.md), [Event Grid](../event-grid/overview.md) 및 2개의 [Azure 함수](../azure-functions/functions-overview.md)에 연결하여 데이터 이동을 용이하게 합니다.

다음은 전체 시나리오를 나타내는 다이어그램입니다. 

먼저 Azure Digital Twins 인스턴스(다이어그램의 **섹션 A**)를 만들고, 디지털 트윈(**화살표 B**)으로 원격 측정 데이터 흐름을 설정한 다음, 트윈 그래프를 통한 데이터 전파(**화살표 C**)를 설정합니다.

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="전체 빌딩 시나리오의 그래픽. 다바이스에서 IoT Hub로, Azure 함수(화살표 B)를 통해 Azure Digital Twins 인스턴스(섹션 A)로, 다시 Event Grid를 통해 또 다른 Azure 함수(화살표 C)로 흐르는 데이터를 묘사합니다.":::

이 시나리오를 진행하기 위해 이전에 다운로드한 미리 작성된 샘플 앱의 구성 요소와 상호 작용합니다.

다음은 빌딩 시나리오 *AdtSampleApp* 샘플 앱에서 구현하는 구성 요소입니다.
* 디바이스 인증 
* [.NET(C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) 사용 예(*CommandLoop.cs*에 있음)
* Azure Digital Twins API를 호출하는 콘솔 인터페이스
* *SampleClientApp* - 샘플 Azure Digital Twins 솔루션
* *SampleFunctionsApp* - IoT Hub 및 Azure Digital Twins 이벤트에서 원격 분석의 결과로 Azure Digital Twins 그래프를 업데이트하는 Azure Functions 앱

샘플 프로젝트에는 대화형 권한 부여 구성 요소도 포함되어 있습니다. 프로젝트를 시작할 때마다 Azure 계정으로 로그인하라는 메시지가 표시된 브라우저 창이 열립니다.

### <a name="instantiate-the-pre-created-twin-graph"></a>미리 만든 트윈 그래프 인스턴스화

먼저 샘플 프로젝트의 *AdtSampleApp* 솔루션을 사용하여 엔드투엔드 시나리오(**섹션 A**)의 Azure Digital Twins 부분을 빌드합니다.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-a.png" alt-text="섹션 A, Azure Digital Twins 인스턴스를 강조 표시한 전체 빌딩 시나리오 그래픽에서 발췌":::

_**AdtE2ESample**_ 프로젝트가 열려 있는 Visual Studio 창에서 도구 모음의 이 단추를 사용하여 프로젝트를 실행합니다.

:::image type="content" source="media/tutorial-end-to-end/start-button-sample.png" alt-text="Visual Studio 시작 단추(SampleClientApp 프로젝트)":::

콘솔 창이 열리고 인증을 수행하고 명령을 기다립니다. 이 콘솔에서 다음 명령을 실행하여 샘플 Azure Digital Twins 솔루션을 인스턴스화합니다.

> [!IMPORTANT]
> 이 명령을 실행하면 Azure Digital Twins 인스턴스에 이미 있는 디지털 트윈과 관계가 삭제되고 샘플 시나리오의 트윈과 관계로 대체됩니다.

```cmd/sh
SetupBuildingScenario
```

Azure Digital Twins에서 이름이 각각 *floor1*, *room21*, *thermostat67*인 층, 방, 온도 센서, 이렇게 3개의 [**디지털 트윈**](concepts-twins-graph.md)이 만들어지고 연결됨에 따라 일련의 확인 메시지가 출력됩니다. 이러한 디지털 트윈은 실제 환경에 존재하는 엔터티를 나타냅니다.

이들 엔터티는 관계를 통해 다음과 같은 [**트윈 그래프**](concepts-twins-graph.md)에 연결됩니다. 트윈 그래프는 엔터티가 서로 상호 작용하고 관련되는 방식을 포함하여 환경 전체를 나타냅니다.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-graph.png" alt-text="floor1에 room21이 있고, room21에 thermostat67이 있음을 보여주는 그래프" border="false":::

포함된 모든 디지털 트윈에 대해 연결된 Azure Digital Twins 인스턴스를 쿼리하는 다음 명령을 실행하여 만들어진 트윈을 확인할 수 있습니다.

```cmd/sh
Query
```

그런 다음, 프로젝트 실행을 중지할 수 있습니다. 자습서 전체에서 솔루션을 계속 사용하므로 Visual Studio에서 솔루션을 열어 둡니다.

## <a name="set-up-the-sample-function-app"></a>샘플 함수 앱 설정

다음 단계는 이 자습서 전체에서 데이터를 처리하는 데 사용할 [Azure Functions 앱](../azure-functions/functions-overview.md)을 설정하는 것입니다. 함수 앱인 *SampleFunctionsApp*에는 2개의 함수가 들어 있습니다.
* *ProcessHubToDTEvents*: 들어오는 IoT Hub 데이터를 처리하고 그에 따라 Azure Digital Twins를 업데이트합니다.
* *ProcessDTRoutedData*: 디지털 트윈의 데이터를 처리하고 그에 따라 Azure Digital Twins의 부모 트윈을 업데이트합니다.

이 섹션에서는 미리 작성된 함수 앱을 게시하고 Azure AD(Azure Active Directory) ID를 할당하여 함수 앱이 Azure Digital Twins에 액세스할 수 있는지 확인합니다. 이 단계를 완료하면 자습서의 나머지 부분에서 함수 앱 내의 함수를 사용할 수 있습니다. 

_**AdtE2ESample**_ 프로젝트가 열려 있는 Visual Studio 창으로 돌아갑니다. 함수 앱은 _**SampleFunctionsApp**_ 프로젝트 파일에 있습니다. *솔루션 탐색기* 창에서 볼 수 있습니다.

### <a name="update-dependencies"></a>종속성 업데이트

앱을 게시하기 전에 종속성이 최신 버전인지 확인하고 포함된 모든 패키지의 최신 버전을 보유하고 있는지 확인하는 것이 좋습니다.

*솔루션 탐색기* 창에서 *SampleFunctionsApp > 종속성*을 확장합니다. *패키지*를 마우스 오른쪽 단추로 클릭하고 *NuGet 패키지 관리...* 를 선택합니다.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-1.png" alt-text="Visual Studio: SampleFunctionsApp 프로젝트에 대한 NuGet 패키지 관리" border="false":::

NuGet 패키지 관리자가 열립니다. *업데이트* 탭을 선택하고 업데이트할 패키지가 있는 경우 *모든 패키지 선택* 확인란을 선택합니다. 그런 다음, *업데이트*를 누릅니다.

:::image type="content" source="media/tutorial-end-to-end/update-dependencies-2.png" alt-text="Visual Studio: NuGet 패키지 관리자에서 모든 패키지를 업데이트하도록 선택":::

### <a name="publish-the-app"></a>앱 게시

_**AdtE2ESample**_ 프로젝트가 열려 있는 Visual Studio 창으로 돌아가서 *솔루션 탐색기* 창에서 _**SampleFunctionsApp**_ 프로젝트 파일을 마우스 오른쪽 단추로 선택하고 **게시**를 누릅니다.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Visual Studio: 프로젝트 게시":::

다음에 나오는 *게시* 페이지에서 기본 대상 선택 항목인 **Azure**를 그대로 두고 *다음*을 누릅니다. 

특정 대상에 대해 **Azure 함수 앱(Windows)** 을 선택하고 *다음*을 누릅니다.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Visual Studio에서 Azure 함수 게시: 특정 대상":::

*Functions 인스턴스* 페이지에서 구독을 선택합니다. 그러면 구독의 *리소스 그룹*으로 상자가 채워집니다.

인스턴스의 리소스 그룹을 선택하고 *+ 새 Azure 함수 만들기...* 를 누릅니다.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Visual Studio에서 Azure 함수 게시: Functions 인스턴스(함수 앱 전)":::

*함수 앱(Windows) - 새 만들기* 창에서 다음과 같이 필드를 입력합니다.
* **이름**은 Azure에서 Azure Functions 앱을 호스트하는 데 사용하는 소비 계획의 이름입니다. 이는 실제 함수를 보유하는 함수 앱의 이름도 됩니다. 고유 값을 선택하거나 기본 제안을 그대로 둘 수 있습니다.
* **구독**이 사용하려는 구독과 일치하는지 확인합니다. 
* **리소스 그룹**이 사용하려는 리소스 그룹과 일치하는지 확인합니다.
* **계획 유형**을 *소비*로 둡니다.
* 리소스 그룹의 위치와 일치하는 **위치**를 선택합니다.
* *새로 만들기...* 링크를 사용하여 새 **Azure Storage**리소스를 만듭니다. 리소스 그룹에 맞게 위치를 설정하고, 다른 기본값을 사용한 다음, "확인"을 누릅니다.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Visual Studio에서 Azure 함수 게시: 함수 앱(Windows) - 새로 만들기":::

그런 다음 **만들기**를 선택합니다.

그러면 이제 리소스 그룹 아래에 새 함수 앱이 표시되는 *Functions 인스턴스* 페이지로 돌아갑니다. *마침*을 누릅니다.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Visual Studio에서 Azure 함수 게시: Functions 인스턴스(함수 앱 후)":::

주 Visual Studio 창에서 다시 열리는 *게시* 창에서 모든 정보가 올바른지 확인하고 **게시**를 선택합니다.

:::image type="content" source="media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Visual Studio에서 Azure 함수 게시: 게시":::

> [!NOTE]
> 다음과 같은 팝업이 표시되는 경우: :::image type="content" source="media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Visual Studio에서 Azure 함수 게시: 자격 증명 게시" border="false":::
> **Azure에서 자격 증명 검색 시도**와 **저장**을 선택합니다.
>
> *Azure에서 Functions 버전 업그레이드* 또는 *함수 런타임 버전이 Azure에서 실행되는 버전과 일치하지 않음* 경고가 표시되는 경우:
>
> 프롬프트에 따라 최신 Azure Functions 런타임 버전으로 업그레이드합니다. 이 문제는 이 자습서의 시작 부분에 있는 *필수 구성 요소* 섹션에서 권장하는 것보다 이전 버전의 Visual Studio를 사용하는 경우 발생할 수 있습니다.

### <a name="assign-permissions-to-the-function-app"></a>함수 앱에 사용 권한 할당

함수 앱이 Azure Digital Twins에 액세스할 수 있게 다음 단계에서는 앱 설정을 구성하고, 앱에 시스템 관리형 Azure AD ID를 할당하고, 이 ID를 Azure Digital Twins 인스턴스의 *Azure Digital Twins 소유자(미리 보기)* 역할에 부여합니다. 이 역할은 인스턴스에서 여러 데이터 평면 활동을 수행하려는 모든 사용자 또는 함수에 필요합니다. 보안 및 역할 할당에 대해 [*개념: Azure Digital Twins 솔루션 보안*](concepts-security.md)에서 자세히 알아보세요.

Azure Cloud Shell에서 다음 명령을 사용하여 함수 앱에서 Azure Digital Twins 인스턴스를 참조하는 데 사용할 애플리케이션 설정을 지정합니다.

```azurecli-interactive
az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "ADT_SERVICE_URL=<your-Azure-Digital-Twins-instance-URL>"
```

다음 명령을 사용하여 시스템 관리 ID를 만듭니다. 출력에서 *principalId* 필드를 기록해 둡니다.

```azurecli-interactive
az functionapp identity assign -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

다음 명령에서 출력의 *principalId* 값을 사용하여 함수 앱의 ID를 Azure Digital Twins 인스턴스의 *Azure Digital Twins 소유자(미리 보기)* 역할에 할당합니다.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Owner (Preview)"
```

이 명령을 실행하면 사용자가 만든 역할 할당에 대한 정보가 출력됩니다. 이제 함수 앱에는 Azure Digital Twins 인스턴스에 액세스할 수 있는 사용 권한이 있습니다.

## <a name="process-simulated-telemetry-from-an-iot-hub-device"></a>IoT Hub 디바이스에서 시뮬레이션된 원격 분석 처리

Azure Digital Twins 그래프는 실제 디바이스의 원격 분석에 의해 구동됩니다. 

이 단계에서는 [IoT Hub](../iot-hub/about-iot-hub.md)에 등록된 시뮬레이션된 자동 온도 조절기 디바이스를 Azure Digital Twins에서 이를 나타내는 디지털 트윈에 연결합니다. 시뮬레이션된 디바이스가 원격 분석을 내보내면 디지털 트윈에서 해당 업데이트를 트리거하는 *ProcessHubToDTEvents* Azure 함수를 통해 데이터가 전달됩니다. 이러한 방식으로 디지털 트윈은 실제 디바이스의 데이터를 사용하여 최신 상태로 유지됩니다. Azure Digital Twins에서 이벤트 데이터를 한 곳에서 다른 곳으로 전달하는 프로세스를 [**라우팅 이벤트**](concepts-route-events.md)라고 합니다.

이 이벤트는 엔드투엔드 시나리오의 다음 부분(**화살표 B**)에서 발생합니다.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-b.png" alt-text="화살표 B, Azure Digital Twins 전의 요소(디바이스, IoT Hub 및 첫 번째 Azure 함수)를 강조 표시한 전체 빌딩 시나리오 그래픽에서 발췌":::

다음은 이 디바이스 연결 설정을 위해 완료해야 하는 작업입니다.
1. 시뮬레이션된 디바이스를 관리할 IoT 허브 만들기
2. 이벤트 구독을 설정하여 적절한 Azure 함수에 IoT 허브 연결
3. IoT 허브에 시뮬레이션된 디바이스 등록
4. 시뮬레이션 디바이스 실행 및 원격 분석 생성
5. Azure Digital Twins를 쿼리하여 라이브 결과 보기

### <a name="create-an-iot-hub-instance"></a>IoT Hub 인스턴스 만들기

Azure Digital Twins는 디바이스와 해당 데이터를 관리하는 Azure 서비스인 [IoT Hub](../iot-hub/about-iot-hub.md)와 함께 작동하도록 설계되었습니다. 이 단계에서는 이 자습서의 샘플 디바이스를 관리할 IoT 허브를 설정합니다.

Azure Cloud Shell에서 다음 명령을 사용하여 새 IoT 허브를 만듭니다.

```azurecli-interactive
az iot hub create --name <name-for-your-IoT-hub> -g <your-resource-group> --sku S1
```

이 명령을 실행하면 만들어진 IoT 허브에 대한 정보가 출력됩니다.

IoT 허브에 지정한 이름을 저장합니다. 나중에 필요합니다.

### <a name="connect-the-iot-hub-to-the-azure-function"></a>Azure 함수에 IoT 허브 연결

그런 다음, 데이터가 Azure Digital Twins를 업데이트하는 함수를 통해 IoT Hub의 디바이스에서 흐를 수 있게 앞에서 게시한 함수 앱의 *ProcessHubToDTEvents* Azure 함수에 IoT 허브를 연결합니다.

이를 위해 IoT Hub에 Azure 함수를 엔드포인트로 사용하는 **이벤트 구독**을 만듭니다. 그러면 IoT Hub에서 발생하는 이벤트에 함수가 "구독"됩니다.

[Azure Portal](https://portal.azure.com/)의 위쪽 검색 창에서 해당 이름을 검색하여 새로 만든 IoT 허브로 이동합니다. 허브 메뉴에서 *이벤트*를 선택하고 *+ 이벤트 구독*을 선택합니다.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1.png" alt-text="Azure Portal: IoT Hub 이벤트 구독":::

*이벤트 구독 만들기 페이지*가 표시됩니다.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-2.png" alt-text="Azure Portal: 이벤트 구독 만들기":::

다음과 같이 필드를 입력합니다(기본적으로 채워진 필드는 언급되지 않음).
* *이벤트 구독 정보* > **이름**: 이벤트 구독 이름을 지정합니다.
* *항목 세부 정보* > **시스템 항목 이름**: 시스템 항목에 사용할 이름을 지정합니다. 
* *이벤트 유형* > **이벤트 유형 필터**: 메뉴 옵션에서 *디바이스 원격 분석*을 선택합니다.
* *엔드포인트 정보* > **엔드포인트 유형**: 메뉴 옵션에서 *Azure 함수*를 선택합니다.
* *엔드포인트 정보* > **엔드포인트**: *엔드포인트 선택* 링크를 누릅니다. 그러면 *Azure 함수 선택* 창이 열립니다. :::image type="content" source="media/tutorial-end-to-end/event-subscription-3.png" alt-text="Azure Portal 이벤트 구독: Azure 함수 선택" border="false":::
    - **구독**, **리소스 그룹**, **함수 앱** 및 **함수**(*ProcessHubToDTEvents*)를 입력합니다. 구독을 선택하면 이 중 일부가 자동으로 채워질 수 있습니다.
    - **선택 확인**을 누릅니다.

*이벤트 구독 만들기* 페이지에서 **만들기**를 누릅니다.

### <a name="register-the-simulated-device-with-iot-hub"></a>IoT Hub에 시뮬레이션된 디바이스 등록 

이 섹션에서는 ID *thermostat67*을 사용하여 IoT Hub에서 디바이스 표현을 만듭니다. 시뮬레이션된 디바이스가 이에 연결됩니다. 이를 통해 원격 분석 이벤트가 허브에서 IoT Hub로 전달되며, 여기에서 이전 단계에서 구독된 Azure 함수가 수신 대기하고, 이벤트를 선택할 준비가 되고, 처리를 계속합니다.

Azure Cloud Shell에서 다음 명령을 사용하여 IoT Hub에 디바이스를 만듭니다.

```azurecli-interactive
az iot hub device-identity create --device-id thermostat67 --hub-name <your-IoT-hub-name> -g <your-resource-group>
```

만든 디바이스에 대한 정보가 출력됩니다.

### <a name="configure-and-run-the-simulation"></a>시뮬레이션 구성 및 실행

다음으로, IoT Hub 인스턴스에 데이터를 보내도록 디바이스 시뮬레이터를 구성합니다.

먼저 다음 명령을 사용하여 *IoT 허브 연결 문자열*을 가져옵니다.

```azurecli
az iot hub show-connection-string -n <your-IoT-hub-name>
```

그리고 다음 명령을 사용하여 *디바이스 연결 문자열*을 가져옵니다.

```azurecli
az iot hub device-identity show-connection-string --device-id thermostat67 --hub-name <your-IoT-hub-name>
```

이 IoT 허브와 IoT 허브 디바이스에 시뮬레이터를 연결하기 위해 이러한 값을 로컬 프로젝트의 디바이스 시뮬레이터 코드에 삽입합니다.

새 Visual Studio 창의 다운로드한 솔루션 폴더에서 _디바이스 시뮬레이터 > **DeviceSimulator.sln**_ 을 엽니다.

>[!NOTE]
> 이제 각각 _**DeviceSimulator.sln**_ 과 _**AdtE2ESample.sln**_ 이 표시된 2개의 Visual Studio 창이 있어야 합니다.

이 새 Visual Studio 창의 *솔루션 탐색기* 창에서 _DeviceSimulator/**AzureIoTHub.cs**_ 를 선택하여 편집 창에서 엽니다. 다음 연결 문자열 값을 위에서 수집한 값으로 변경합니다.

```csharp
connectionString = <Iot-hub-connection-string>
deviceConnectionString = <device-connection-string>
```

파일을 저장합니다.

이제 설정한 데이터 시뮬레이션의 결과를 보려면 도구 모음에서 이 단추를 사용하여 **DeviceSimulator** 프로젝트를 실행합니다.

:::image type="content" source="media/tutorial-end-to-end/start-button-simulator.png" alt-text="Visual Studio 시작 단추(DeviceSimulator 프로젝트)":::

콘솔 창이 열리고 시뮬레이션된 온도 원격 분석 메시지가 표시됩니다. 이 메시지는 IoT Hub에 전송되며, 여기에서 Azure 함수에 의해 선택되고 처리됩니다.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="보내고 있는 온도 원격 분석을 보여주는 디바이스 시뮬레이터의 콘솔 출력":::

이 콘솔에서 다른 작업을 수행할 필요는 없지만 다음 단계를 완료하는 동안 콘솔을 실행 상태로 둡니다.

### <a name="see-the-results-in-azure-digital-twins"></a>Azure Digital Twins에서 결과 보기

이전에 게시한 *ProcessHubToDTEvents* 함수는 IoT Hub 데이터를 수신 대기하고 Azure Digital Twins API를 호출하여 *thermostat67* 트윈의 *온도* 속성을 업데이트합니다.

Azure Digital Twins 쪽에서 데이터를 보려면 _**AdtE2ESample**_ 프로젝트가 열려 있는 Visual Studio 창으로 이동하고 프로젝트를 실행합니다.

열리는 프로젝트 콘솔 창에서 다음 명령을 실행하여 디지털 트윈 *thermostat67*에서 보고하는 온도를 가져옵니다.

```cmd
ObserveProperties thermostat67 Temperature
```

10초마다 *Azure Digital Twins 인스턴스*에서 라이브 업데이트된 온도가 콘솔에 기록되는 것을 확인할 수 있습니다.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry.png" alt-text="디지털 트윈 thermostat67의 온도 메시지 로그를 보여주는 콘솔 출력":::

이 작업이 성공적으로 수행되는 것을 확인한 후에는 두 프로젝트의 실행을 모두 중지할 수 있습니다. 자습서의 나머지 부분에서 계속 사용할 것이므로 Visual Studio 창을 열어 둡니다.

## <a name="propagate-azure-digital-twins-events-through-the-graph"></a>그래프를 통해 Azure Digital Twins 이벤트 전파

지금까지 이 자습서에서 외부 디바이스 데이터에서 Azure Digital Twins를 업데이트하는 방법을 살펴보았습니다. 다음으로, 한 디지털 트윈의 변경 내용이 Azure Digital Twins 그래프를 통해 어떻게 전파 될 수 있는지 즉, 서비스 내부 데이터에서 트윈을 업데이트하는 방법을 살펴보겠습니다.

이를 위해 *ProcessDTRoutedData* Azure 함수를 사용하여 연결된 *Thermostat* 트윈이 업데이트될 때 *Room* 트윈을 업데이트합니다. 이 이벤트는 엔드투엔드 시나리오의 다음 부분(**화살표 C**)에서 발생합니다.

:::image type="content" source="media/tutorial-end-to-end/building-scenario-c.png" alt-text="화살표 C, Azure Digital Twins 후의 요소(Event Grid와 두 번째 Azure 함수)를 강조 표시한 전체 빌딩 시나리오 그래픽에서 발췌":::

다음은 이 데이터 흐름 설정을 위해 완료해야 하는 작업입니다.
1. Event Grid에 인스턴스를 연결하는 Azure Digital Twins 엔드포인트를 만듭니다.
2. Azure Digital Twins 내에서 경로를 설정하여 엔드포인트에 트윈 속성 변경 이벤트를 보냅니다.
3. 엔드포인트에서 [Event Grid](../event-grid/overview.md)를 통해 수신 대기하고 그에 따라 다른 트윈을 업데이트하는 Azure Functions 앱을 배포합니다.
4. 시뮬레이션된 디바이스를 실행하고 Azure Digital Twins를 쿼리하여 라이브 결과를 봅니다.

### <a name="set-up-endpoint"></a>엔드포인트 설정

[Event Grid](../event-grid/overview.md)는 Azure 서비스에서 Azure 내의 다른 장소로 오는 이벤트를 라우팅하고 전송하는 데 도움이 되는 Azure 서비스입니다. [이벤트 그리드 토픽](../event-grid/concepts.md)을 만들어 원본에서 특정 이벤트를 수집할 수 있습니다. 그러면 구독자가 토픽에서 수신 대기하여 이벤트가 올 때 해당 이벤트를 받을 수 있습니다.

이 섹션에서는 이벤트 그리드 토픽을 만든 다음, Azure Digital Twins 내에 해당 토픽을 가리키는(이벤트를 보냄) 엔드포인트를 만듭니다. 

Azure Cloud Shell에서 다음 명령을 실행하여 이벤트 그리드 토픽을 만듭니다.

```azurecli-interactive
az eventgrid topic create -g <your-resource-group> --name <name-for-your-event-grid-topic> -l <region>
```

> [!TIP]
> Azure CLI 명령으로 전달할 수 있는 Azure 지역 이름 목록을 출력하려면 다음 명령을 실행합니다.
> ```azurecli
> az account list-locations -o table
> ```

이 명령을 실행하면 사용자가 만든 이벤트 그리드 토픽에 대한 정보가 출력됩니다.

다음으로, 이벤트 그리드 토픽을 가리키는 Azure Digital Twins 엔드포인트를 만듭니다. 아래 명령을 사용하여 필요에 따라 자리 표시자 필드를 작성합니다.

```azurecli
az dt endpoint create eventgrid --dt-name <your-Azure-Digital-Twins-instance> --eventgrid-resource-group <your-resource-group> --eventgrid-topic <your-event-grid-topic> --endpoint-name <name-for-your-Azure-Digital-Twins-endpoint>
```

이 명령을 실행하면 만든 엔드포인트에 대한 정보가 출력됩니다.

다음 명령으로 이 엔드포인트에 대해 Azure Digital Twins 인스턴스를 쿼리하여 엔드포인트 만들기에 성공했는지 확인할 수도 있습니다.

```azurecli
az dt endpoint show --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> 
```

출력에서 `provisioningState` 필드를 찾고 해당 값이 "성공"인지 확인합니다. "프로비저닝"을 나타낼 수도 있습니다. 즉, 엔드포인트가 아직 생성되고 있음을 의미합니다. 이 경우 몇 초 정도 기다린 후 명령을 다시 실행하여 성공적으로 완료되었는지 확인합니다.

:::image type="content" source="media/tutorial-end-to-end/output-endpoints.png" alt-text="provisioningState가 성공인 엔드포인트를 보여주는 엔드포인트 쿼리의 결과":::

이벤트 그리드 토픽에 지정한 이름과 Azure Digital Twins 엔드포인트를 저장합니다. 나중에 사용합니다.

### <a name="set-up-route"></a>경로 설정

그런 다음, 방금 만든 Azure Digital Twins 엔드포인트에 이벤트를 보내는 Azure Digital Twins 경로를 만듭니다.

```azurecli
az dt route create --dt-name <your-Azure-Digital-Twins-instance> --endpoint-name <your-Azure-Digital-Twins-endpoint> --route-name <name-for-your-Azure-Digital-Twins-route>
```

이 명령을 실행하면 만든 경로에 대한 일부 정보가 출력됩니다.

>[!NOTE]
>엔드포인트(이전 단계에서)는 프로비저닝을 완료해야 해당 엔드포인트를 사용하는 이벤트 경로를 설정할 수 있습니다. 엔드포인트가 준비되지 않았기 때문에 경로를 만들지 못한 경우 몇 분 정도 기다린 다음, 다시 시도하세요.

#### <a name="connect-the-function-to-event-grid"></a>Event Grid에 함수 연결

다음으로, 원격 분석 데이터가 이벤트 그리드 토픽을 통해 *thermostat67* 트윈에서 함수로 흐를 수 있게 *ProcessDTRoutedData* Azure 함수를 앞에서 만든 이벤트 그리드 토픽에 구독합니다. 이 함수는 Azure Digital Twins로 돌아가고 그에 따라 *room21* 트윈을 업데이트합니다.

이를 위해 이벤트 그리드 토픽에서 *ProcessDTRoutedData* Azure 함수로의 **Event Grid 구독**을 엔드포인트로 만듭니다.

[Azure Portal](https://portal.azure.com/)의 위쪽 검색 창에서 해당 이름을 검색하여 이벤트 그리드 토픽으로 이동합니다. *+ 이벤트 구독*을 선택합니다.

:::image type="content" source="media/tutorial-end-to-end/event-subscription-1b.png" alt-text="Azure Portal: Event Grid 이벤트 구독":::

이 이벤트 구독을 만드는 단계는 이 자습서의 앞부분에서 IoT Hub에 첫 번째 Azure 함수를 구독할 때와 비슷합니다. 이번에는 수신 대기할 이벤트 유형으로 *디바이스 원격 분석*을 지정할 필요가 없으며 다른 Azure 함수에 연결합니다.

*이벤트 구독 만들기* 페이지에서 다음과 같이 필드를 입력합니다(기본적으로 채워진 필드는 언급되지 않음).
* *이벤트 구독 정보* > **이름**: 이벤트 구독 이름을 지정합니다.
* *엔드포인트 정보* > **엔드포인트 유형**: 메뉴 옵션에서 *Azure 함수*를 선택합니다.
* *엔드포인트 정보* > **엔드포인트**: *엔드포인트 선택* 링크를 누릅니다. 그러면 *Azure 함수 선택* 창이 열립니다.
    - **구독**, **리소스 그룹**, **함수 앱** 및 **함수**(*ProcessDTRoutedData*)를 입력합니다. 구독을 선택하면 이 중 일부가 자동으로 채워질 수 있습니다.
    - **선택 확인**을 누릅니다.

*이벤트 구독 만들기* 페이지에서 **만들기**를 누릅니다.

### <a name="run-the-simulation-and-see-the-results"></a>시뮬레이션을 실행하고 결과를 확인합니다.

이제 디바이스 시뮬레이터를 실행하여 설정한 새 이벤트 흐름을 시작할 수 있습니다. _**DeviceSimulator**_ 프로젝트가 열려 있는 Visual Studio 창으로 이동하고 프로젝트를 실행합니다.

앞에서 디바이스 시뮬레이터를 실행했을 때와 같이 콘솔 창이 열리고 시뮬레이션된 온도 원격 분석 메시지가 표시됩니다. 이러한 이벤트는 이전에 설정한 흐름을 통과하여 *thermostat67* 트윈을 업데이트한 다음, 최근에 설정한 흐름을 통과하여 그에 맞게 *room21* 트윈을 업데이트합니다.

:::image type="content" source="media/tutorial-end-to-end/console-simulator-telemetry.png" alt-text="보내고 있는 온도 원격 분석을 보여주는 디바이스 시뮬레이터의 콘솔 출력":::

이 콘솔에서 다른 작업을 수행할 필요는 없지만 다음 단계를 완료하는 동안 콘솔을 실행 상태로 둡니다.

Azure Digital Twins 쪽에서 데이터를 보려면 _**AdtE2ESample**_ 프로젝트가 열려 있는 Visual Studio 창으로 이동하고 프로젝트를 실행합니다.

열리는 프로젝트 콘솔 창에서 다음 명령을 실행하여 디지털 트윈 *thermostat67*과 디지털 트윈 *room21*에서 **모두** 보고하는 온도를 가져옵니다.

```cmd
ObserveProperties thermostat67 Temperature room21 Temperature
```

10초마다 *Azure Digital Twins 인스턴스*에서 라이브 업데이트된 온도가 콘솔에 기록되는 것을 확인할 수 있습니다. *thermostat67*의 업데이트 내용에 맞게 *room21*의 온도가 업데이트되고 있음을 확인합니다.

:::image type="content" source="media/tutorial-end-to-end/console-digital-twins-telemetry-b.png" alt-text="자동 온도 조절기와 방의 온도 메시지 로그를 보여주는 콘솔 출력":::

이 작업이 성공적으로 수행되는 것을 확인한 후에는 두 프로젝트의 실행을 모두 중지할 수 있습니다. 이제 자습서를 마쳤으므로 Visual Studio 창도 닫을 수 있습니다.

## <a name="review"></a>검토

다음은 이 자습서에서 작성한 시나리오에 대한 검토 사항입니다.

1. Azure Digital Twins 인스턴스는 층, 방 및 자동 온도 조절기를 디지털 방식으로 표시합니다(아래 다이어그램에서**섹션 A**로 표시됨).
2. 시뮬레이션된 디바이스 원격 분석이 IoT Hub에 전송되며, 여기에서 *ProcessHubToDTEvents* Azure 함수가 원격 분석 이벤트를 수신 대기합니다. *ProcessHubToDTEvents* Azure 함수는 이러한 이벤트의 정보를 사용하여 *thermostat67*에 *온도* 속성을 설정합니다(다이어그램의 **화살표 B**).
3. Azure Digital Twins의 속성 변경 이벤트는 이벤트 그리드 토픽으로 라우팅되며, 여기에서 *ProcessDTRoutedData* Azure 함수가 이벤트를 수신 대기합니다. *ProcessDTRoutedData* Azure 함수는 이러한 이벤트의 정보를 사용하여 *room21*에 *온도* 속성을 설정합니다(다이어그램의 **화살표 C**).

:::image type="content" source="media/tutorial-end-to-end/building-scenario.png" alt-text="전체 빌딩 시나리오의 그래픽. 다바이스에서 IoT Hub로, Azure 함수(화살표 B)를 통해 Azure Digital Twins 인스턴스(섹션 A)로, 다시 Event Grid를 통해 또 다른 Azure 함수(화살표 C)로 흐르는 데이터를 묘사합니다.":::

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스가 더 이상 필요하지 않은 경우 다음 절차에 따라 삭제합니다. 

Azure Cloud Shell을 사용하면 [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) 명령으로 리소스 그룹의 모든 Azure 리소스를 삭제할 수 있습니다. 그러면 Azure Digital Twins 인스턴스, IoT 허브와 허브 디바이스 등록, 이벤트 그리드 토픽과 관련 구독, 스토리지와 같은 관련 리소스를 포함한 두 Azure Functions 앱을 비롯한 리소스 그룹이 제거됩니다.

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 다시 되돌릴 수 없습니다. 리소스 그룹 및 그 안에 포함된 모든 리소스가 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

다음으로, 이 명령을 사용하여 클라이언트 앱용으로 만든 Azure AD 앱 등록을 삭제합니다.

```azurecli
az ad app delete --id <your-application-ID>
```

마지막으로, 로컬 컴퓨터에서 다운로드한 프로젝트 샘플 폴더를 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 라이브 디바이스 데이터로 구동되는 Azure Digital Twins를 보여주는 엔드투엔드 시나리오를 만들었습니다.

다음으로, 개념 설명서를 통해 자습서에서 작업한 요소에 대해 자세히 알아보세요.
* [*개념: 사용자 지정 모델*](concepts-models.md)

또는 방법 문서로 시작하여 이 자습서의 프로세스에 대해 더 자세히 알아보세요.
* [*방법: Azure Digital Twins CLI 사용*](how-to-use-cli.md)
