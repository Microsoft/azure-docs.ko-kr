---
title: '자습서: IoT 시각적 경고 샘플'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 IoT 디바이스에서 Custom Vision을 사용하여 카메라 비디오 피드의 시각적 상태를 인식하고 보고합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 5582056f1bae2dbeb69a7d05044f055ff1394bd5
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88244672"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>자습서: IoT 디바이스에서 Custom Vision을 사용하여 시각적 상태 보고

이 샘플 앱은 Custom Vision을 사용하여 시각적 상태를 검색하는 카메라를 사용하여 디바이스를 학습하는 방법을 보여 줍니다. 내보낸 ONNX 모델을 사용하여 IoT 디바이스에서 이 검색 시나리오를 실행할 수 있습니다.

시각적 상태는 빈 방 또는 사람이 있는 방, 빈 차도 또는 트럭이 있는 차도 등과 같이 이미지의 콘텐츠를 설명합니다. 아래 이미지에서 바나나 또는 사과가 카메라 앞에 배치된 경우 앱 검색을 확인할 수 있습니다.

![카메라 앞에 있는 과일의 UI 레이블 지정 애니메이션](./media/iot-visual-alerts-tutorial/scoring.gif)

이 자습서는 다음에 대한 방법을 보여 줍니다.
> [!div class="checklist"]
> * 사용자 고유의 Custom Vision 및 IoT Hub 리소스를 사용하도록 샘플 앱을 구성합니다.
> * 앱을 사용하여 Custom Vision 프로젝트를 학습합니다.
> * 앱을 사용하여 새 이미지를 실시간으로 점수를 매기고 결과를 Azure로 보냅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services)을 만듭니다. 

## <a name="prerequisites"></a>사전 요구 사항

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > 나중에 모델을 ONNX로 내보낼 예정이므로 이 프로젝트는 **Compact** 이미지 분류 프로젝트여야 합니다.
* 또한 Azure에서 [IoT Hub 리소스 만들기](https://ms.portal.azure.com/#create/Microsoft.IotHub)를 수행해야 합니다.
* [Visual Studio 2015 이상](https://www.visualstudio.com/downloads/)
* 필요에 따라 Windows 10 IoT Core 버전 17763 이상을 실행하는 IoT 디바이스가 있어야 합니다. 사용자 PC에서 직접 앱을 실행할 수도 있습니다.
   * Raspberry Pi 2 및 3의 경우 IoT 대시보드 앱에서 직접 Windows 10을 설정할 수 있습니다. DrangonBoard와 같은 다른 디바이스의 경우 [eMMC 메서드](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices)를 사용하여 이를 플래시해야 합니다. 새 디바이스를 설정하는 데 도움이 필요한 경우 Windows IoT 설명서의 [디바이스 설정](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup)을 참조하세요.

## <a name="about-the-visual-alerts-app"></a>시각적 경고 앱 정보

IoT 시각적 경고 앱은 연속 루프에서 실행되며, 다음과 같은 네 가지 다른 상태를 적절하게 전환합니다.

* **모델 없음**: no-op 상태입니다. 앱이 계속해서 1초 동안 대기하고 카메라를 확인합니다.
* **학습 이미지 캡처**: 이 상태에서 앱은 그림을 캡처하여 대상 Custom Vision 프로젝트에 학습 이미지로 업로드합니다. 그러면 앱이 500밀리초 동안 대기하고 설정된 대상 이미지 수가 캡처될 때까지 작업을 반복합니다. 그런 다음 Custom Vision 모델의 학습을 트리거합니다.
* **학습된 모델 대기**: 이 상태에서 앱은 매초 Custom Vision API를 호출하여 대상 프로젝트에 학습된 반복이 포함되어 있는지 여부를 확인합니다. 학습된 반복을 찾으면 해당 ONNX 모델을 로컬 파일에 다운로드하고 **점수 매기기** 상태로 전환합니다.
* **점수 매기기**: 이 상태에서 앱은 Windows ML을 사용하여 로컬 ONNX 모델에 대해 카메라에서 단일 프레임을 평가합니다. 결과 이미지 분류가 화면에 표시되고 IoT Hub에 메시지로 전송됩니다. 그러면 앱은 새 이미지의 점수를 매기기 전에 1초 동안 대기합니다.

## <a name="understand-the-code-structure"></a>코드 구조 이해

다음 파일은 앱의 주요 기능을 처리합니다.

| 파일 | Description |
|-------------|-------------|
| [MainPage.xaml](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | 이 파일은 XAML 사용자 인터페이스를 정의합니다. 웹 카메라 컨트롤을 호스트하고 상태 업데이트에 사용되는 레이블을 포함합니다.|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | 이 코드는 XAML UI의 동작을 제어합니다. 상태 시스템 처리 코드를 포함합니다.|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | 이 클래스는 Custom Vision Service와의 통합을 처리하는 래퍼입니다.|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | 이 클래스는 ONNX 모델을 로드하고 해당 모델에 대해 이미지 점수를 매길 수 있도록 Windows ML과의 통합을 처리하는 래퍼입니다.|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IoTHubWrapper.cs) | 이 클래스는 점수 매기기 결과를 Azure에 업로드할 수 있도록 IoT Hub와의 통합을 처리하는 래퍼입니다.|

## <a name="set-up-the-visual-alerts-app"></a>시각적 경고 앱 설정

다음 단계를 수행하여 PC 또는 IoT 디바이스에서 실행되는 IoT 시각적 경고 앱을 가져옵니다.

1. GitHub에서 [IoTVisualAlerts 샘플](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)을 복제 또는 다운로드합니다.
1. Visual Studio에서 _IoTVisualAlerts.sln_ 솔루션을 엽니다.
1. Custom Vision 프로젝트를 통합합니다.
    1. _CustomVision\CustomVisionServiceWrapper.cs_ 스크립트에서 학습 키로 `ApiKey` 변수를 업데이트합니다.
    1. 그런 다음 키와 연결된 엔드포인트 URL을 사용하여 `Endpoint` 변수를 업데이트합니다.
    1. `targetCVSProjectGuid` 변수를 사용하려는 Custom Vision 프로젝트의 해당 ID로 업데이트합니다. 
1. IoT Hub 리소스를 설정합니다.
    1. _IoTHub\IotHubWrapper.cs_ 스크립트에서 디바이스에 대한 적절한 연결 문자열로 `s_connectionString` 변수를 업데이트합니다. 
    1. Azure Portal에서 IoT Hub 인스턴스를 로드하고 **탐색기**에서 **IoT 디바이스**를 클릭하고 대상 디바이스를 선택(또는 필요한 경우 디바이스 만들기)하고 **기본 연결 문자열**에서 연결 문자열을 찾습니다. 문자열에는 IoT Hub 이름, 디바이스 ID 및 공유 액세스 키가 포함되며 형식은 다음과 같습니다. `{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}`

## <a name="run-the-app"></a>앱 실행

PC에서 앱을 실행하는 경우 Visual Studio에서 대상 디바이스에 대해 **로컬 컴퓨터**를 선택하고, 대상 플랫폼에 대해 **x64** 또는 **x86**을 선택합니다. 그런 다음 F5 키를 눌러 프로그램을 실행합니다. 앱이 시작되고 카메라의 라이브 피드와 상태 메시지가 표시되어야 합니다.

ARM 프로세서를 사용하여 IoT 디바이스에 배포하는 경우 대상 플랫폼으로 **ARM**을 선택하고 대상 디바이스로 **원격 컴퓨터**를 선택해야 합니다. 메시지가 표시되면 디바이스의 IP 주소를 입력합니다(PC와 동일한 네트워크에 있어야 함). 디바이스를 부팅하고 네트워크에 연결하면 Windows IoT 기본 앱에서 IP 주소를 가져올 수 있습니다. F5 키를 눌러 프로그램을 실행합니다.

앱을 처음 실행하는 경우 시각적 상태에 대한 지식이 없습니다. 사용할 수 있는 모델이 없다는 상태 메시지가 표시됩니다. 

## <a name="capture-training-images"></a>학습 이미지 캡처

모델을 설정하려면 앱이 **학습 이미지 캡처** 상태여야 합니다. 다음 단계 중 하나를 수행합니다.
* PC에서 앱을 실행하는 경우 UI의 오른쪽 위 모서리에 있는 단추를 사용합니다.
* IoT 디바이스에서 앱을 실행하는 경우 IoT Hub를 통해 디바이스에서 `EnterLearningMode` 메서드를 호출합니다. 메서드는 Azure Portal에서 IoT Hub 메뉴의 디바이스 항목 또는 [IoT Hub Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp) 등의 도구를 통해 호출할 수 있습니다.
 
앱이 **학습 이미지 캡처** 상태가 되면 대상 이미지 수에 도달할 때까지 매초마다 약 두 개의 이미지를 캡처합니다. 기본적으로 대상은 30개의 이미지이지만 원하는 수를 `EnterLearningMode` IoT Hub 메서드에 인수로 전달하여 이 매개 변수를 설정할 수 있습니다. 

앱에서 이미지를 캡처하는 동안 검색하려는 시각적 상태 유형(예: 빈 방, 사람이 있는 방, 빈 책상, 장난감 트럭이 있는 책상 등)에 카메라를 노출해야 합니다.

## <a name="train-the-custom-vision-model"></a>Custom Vision 모델 학습

앱은 이미지 캡처를 완료한 후 해당 이미지를 업로드한 다음 **학습된 모델 대기** 상태로 전환합니다. 이제 [Custom Vision 포털](https://www.customvision.ai/)로 이동하여 새 학습 이미지를 기반으로 모델을 작성해야 합니다. 다음 애니메이션은 이 프로세스의 예를 보여 줍니다.

![애니메이션: 바나나의 여러 이미지에 태그 지정](./media/iot-visual-alerts-tutorial/labeling.gif)

사용자 고유의 시나리오를 사용하여 이 프로세스를 반복하려면 다음을 수행합니다.

1. [Custom Vision 포털](http://customvision.ai)에 로그인합니다.
1. 이제 앱에서 업로드한 모든 학습 이미지를 포함하는 대상 프로젝트를 찾습니다.
1. 식별하려는 각 시각적 상태에 대해 적절한 이미지를 선택하고 태그를 수동으로 적용합니다.
    * 예를 들어, 빈 방과 사람이 있는 방을 구분하려면 사람이 있는 5개 이상의 이미지에 새 클래스 **People**로 태그를 지정하고 사람이 없는 5개 이상의 이미지에 **Negative** 태그를 지정하는 것이 좋습니다. 이렇게 하면 모델을 두 상태로 구분할 수 있습니다.
    * 또 다른 예로, 선반이 채워진 정도를 가늠하려면 **EmptyShelf**, **PartiallyFullShelf** 및 **FullShelf**와 같은 태그를 사용할 수 있습니다.
1. 작업이 완료되면 **학습** 단추를 선택합니다.
1. 학습이 완료되면 앱은 학습된 반복을 사용할 수 있음을 감지합니다. 학습된 모델을 ONNX로 내보내고 디바이스로 다운로드하는 프로세스가 시작됩니다.

## <a name="use-the-trained-model"></a>학습된 모델 사용

앱이 학습된 모델을 다운로드한 후에는 **점수 매기기** 상태로 전환되고 카메라의 이미지 점수 매기기가 연속 루프로 시작됩니다.

캡처된 각 이미지에 대해 앱은 맨 위 태그를 화면에 표시합니다. 시각적 상태를 인식하지 못하는 경우 **일치 항목 없음**이 표시됩니다. 또한 앱은 이 메시지를 IoT Hub로 보내며 검색되는 클래스가 있는 경우 메시지에는 레이블, 신뢰도 점수 및 `detectedClassAlert`라는 속성이 포함됩니다 .이 속성은 속성 기반 빠른 메시지 라우팅을 수행하는 데 관심이 있는 IoT Hub 클라이언트가 사용할 수 있습니다.

또한 이 샘플에서는 [Sense HAT 라이브러리](https://github.com/emmellsoft/RPi.SenseHat)를 사용하여 Sense HAT 단위를 사용하여 Raspberry Pi에서 실행되는 경우를 검색하므로 이를 출력 표시로 사용하려면 클래스가 검색될 때마다 모든 표시 광원을 빨간색으로 설정하고 아무 것도 검색되지 않을 경우 빈 칸으로 설정하면 됩니다.

## <a name="reuse-the-app"></a>앱 다시 사용

앱을 원래 상태로 다시 설정하려면 UI의 오른쪽 위 모서리에 있는 단추를 클릭하거나 IoT Hub를 통해 `DeleteCurrentModel` 메서드를 호출하여 이 작업을 수행할 수 있습니다.

언제든지 오른쪽 위에 있는 UI 단추를 클릭하거나 `EnterLearningMode` 메서드를 다시 호출하여 학습 이미지를 업로드하는 단계를 반복할 수 있습니다.

디바이스에서 앱을 실행하고 IP 주소를 다시 검색해야 하는 경우(예를 들어 [Windows IoT 원격 클라이언트](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab)를 통해 원격 연결을 설정하려면) IoT Hub를 통해 `GetIpAddress` 메서드를 호출하면 됩니다.

## <a name="clean-up-resources"></a>리소스 정리

Custom Vision 프로젝트를 더 이상 유지 관리하지 않으려면 삭제합니다. [Custom Vision 웹 사이트](https://customvision.ai)에서 **프로젝트**로 이동하여 새 프로젝트 아래에서 휴지통을 선택합니다.

![내 새 프로젝트라는 레이블과 휴지통 아이콘이 있는 패널의 스크린샷](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT 디바이스에서 시각적 상태 정보를 검색하고 그 결과를 IoT Hub에 보내는 애플리케이션을 설정하고 실행했습니다. 다음으로 소스 코드를 추가로 탐색하거나 아래 제안된 수정 사항 중 하나를 수행합니다.

> [!div class="nextstepaction"]
> [IoTVisualAlerts 샘플(GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* IoT Hub 메서드를 추가하여 앱을 **학습된 모델 대기** 상태로 직접 전환합니다. 이러한 방식으로 디바이스 자체에서 캡처되지 않은 이미지를 사용하여 모델을 학습한 다음 명령에서 디바이스에 새 모델을 푸시할 수 있습니다.
* [실시간 센서 데이터 시각화](https://docs.microsoft.com/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi) 자습서에 따라 샘플에서 전송한 IoT Hub 경고를 시각화하는 Power BI 대시보드를 만듭니다.
* [IoT 원격 모니터링](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps) 자습서에 따라 시각적 상태가 검색되면 IoT Hub 경고에 응답하는 논리 앱을 만듭니다.
