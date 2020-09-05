---
title: 자습서 - Azure IoT Central 비디오 분석 - 개체 및 동작 감지 애플리케이션 템플릿을 사용하여 비디오 모니터링
description: 이 자습서에서는 비디오 분석 - 개체 및 동작 감지 애플리케이션 템플릿의 대시보드를 사용하여 카메라를 관리하고 비디오를 모니터링하는 방법을 보여줍니다.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 76e72e8fd134c65cc9334e635375cc25e9b09a75
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719073"
---
# <a name="tutorial-monitor-and-manage-a-video-analytics---object-and-motion-detection-application"></a>자습서: 비디오 분석 - 개체 및 동작 감지 애플리케이션 모니터링 및 관리

이 자습서에서는 다음을 수행하는 방법을 알아봅니다.
> [!div class="checklist"]
> * IoT Central 애플리케이션에 개체 및 동작 감지 카메라 추가
> * 흥미로운 이벤트가 발견될 때 비디오 스트림을 관리하고 재생

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에, 다음 항목을 먼저 완료해야 합니다.

* [Azure IoT Central에서 라이브 비디오 분석 애플리케이션 만들기](./tutorial-video-analytics-create-app.md) 자습서
* 이전 [라이브 비디오 분석용 IoT Edge 인스턴스 만들기(Linux VM)](tutorial-video-analytics-iot-edge-vm.md) 또는 [라이브 비디오 분석용 IoT Edge 인스턴스 만들기(Linux VM)](tutorial-video-analytics-iot-edge-nuc.md) 자습서 중 하나

비디오 뷰어 애플리케이션을 실행하려면 로컬 머신에 [Docker](https://www.docker.com/products/docker-desktop)가 설치되어 있어야 합니다.

## <a name="add-an-object-detection-camera"></a>개체 감지 카메라 추가

IoT Central 애플리케이션에서 이전에 만든 **LVA Gateway 001** 디바이스로 이동합니다. 그런 다음, **명령** 탭을 선택합니다.

다음 표의 값을 **카메라 요청 추가** 명령의 매개 변수 값으로 사용합니다. 표에 나와 있는 값은 Azure VM에서 시뮬레이션된 카메라를 사용 중이라고 가정하므로, 실제 카메라를 사용하는 경우 값을 적절하게 조정해야 합니다.

| 필드| Description| 샘플 값|
|---------|---------|---------|
| 카메라 ID      | 프로비저닝할 디바이스 ID | camera-003 |
| 카메라 이름    | 식별 이름           | 개체 감지 카메라 |
| RTSP URL       | 스트림 주소   | RTSP://10.0.0.4:554/media/camera-300s.mkv|
| RTSP 사용자 이름  |                         | 사용자    |
| RTSP 암호  |                         | password    |
| 검색 유형 | 드롭다운                | 개체 감지       |

다음과 같이 **실행**을 선택하여 카메라 디바이스를 추가합니다.

:::image type="content" source="media/tutorial-video-analytics-manage/add-camera.png" alt-text="카메라 추가":::

> [!NOTE]
> **LVA Edge 개체 감지기** 디바이스 템플릿은 이미 애플리케이션에 있습니다.

## <a name="add-a-motion-detection-camera-optional"></a>동작 감지 카메라 추가(선택 사항)

IoT Edge 게이트웨이 디바이스에 카메라 두 대를 연결한 경우 이전 단계를 반복하여 애플리케이션에 동작 감지 카메라를 추가합니다. **카메라 ID**, **카메라 이름** 및 **RTSP URL** 매개 변수에 다른 값을 사용합니다.

## <a name="view-the-downstream-devices"></a>다운스트림 디바이스 보기

다음과 같이 **LVA Gateway 001** 디바이스의 **다운스트림 디바이스** 탭을 선택하여 방금 추가한 카메라 디바이스를 확인합니다.

:::image type="content" source="media/tutorial-video-analytics-manage/inspect-downstream.png" alt-text="검사":::

카메라 디바이스는 애플리케이션의 **디바이스** 페이지에 있는 목록에도 표시됩니다.

## <a name="configure-and-manage-the-camera"></a>카메라 구성 및 관리

**camera-003** 디바이스로 이동하여 **관리** 탭을 선택합니다.

다음과 같은 기본값을 사용하거나, 디바이스 속성을 사용자 지정해야 하는 경우 기본값을 수정합니다.

**카메라 설정**

| 속성 | 설명 | 제안 값 |
|-|-|-|
| 비디오 재생 호스트 | Azure Media Player 뷰어의 호스트 | http://localhost:8094 |

**LVA 운영 및 진단**

| 속성 | 설명 | 제안 값 |
|-|-|-|
| 자동 시작 | LVA 게이트웨이가 다시 시작될 때 개체 감지 시작 | 선택됨 |
| 원격 분석 디버깅 | 이벤트 추적 | 옵션 |
|유추 시간 제한(초)| 추론이 중지된 것을 확인하는 데 사용된 시간입니다. | 20 |

**AI 개체 감지**

| 속성 | 설명 | 제안 값 |
|-|-|-|
| 최대 유추 캡처 시간(초) | 최대 캡처 시간 | 15 |
| 감지 클래스 | 감지 태그가 지정된, 쉼표로 구분된 문자열입니다. 자세한 내용은 [지원되는 태그 목록](https://github.com/Azure/live-video-analytics/blob/master/utilities/video-analysis/yolov3-onnx/tags.txt)을 참조하세요. | 트럭, 버스 |
| 신뢰도 임계값 | 개체 감지가 유효한지 확인하는 자격 백분율 | 70 |
| 유추 프레임 샘플 속도(fps) | [여기에 설명] | 2 |

**저장**을 선택합니다.

몇 초 후에 다음과 같이 각 설정에 대한 **수락됨** 확인 메시지가 표시됩니다.

:::image type="content" source="media/tutorial-video-analytics-manage/object-detection.png" alt-text="개체 감지":::

## <a name="start-lva-processing"></a>LVA 처리 시작

**camera-003** 디바이스로 이동하여 **명령** 탭을 선택합니다.

**LVA 처리 시작** 명령을 실행합니다.

명령이 완료되면 명령 기록을 살펴보고 오류가 없는지 확인합니다.

:::image type="content" source="media/tutorial-video-analytics-manage/start-processing.png" alt-text="LVA 처리 시작 명령":::

## <a name="monitor-the-cameras"></a>카메라 모니터링

**camera-003** 디바이스로 이동하여 **대시보드** 탭을 선택합니다.

:::image type="content" source="media/tutorial-video-analytics-manage/camera-dashboard.png" alt-text="카메라 대시보드":::

**감지 횟수** 타일에는 1초 감지 간격 동안 선택한 각 감지 클래스 개체의 평균 감지 횟수가 표시됩니다.

**감지 클래스** 원형 차트는 각 클래스 형식의 감지 비율을 보여줍니다.

**유추 이벤트 비디오**는 감지를 포함하는 Azure Media Services 자산에 대한 링크 목록입니다. 이 링크는 다음 섹션에 설명된 호스트 플레이어를 사용합니다.

## <a name="start-the-streaming-endpoint"></a>스트리밍 엔드포인트 시작

기록된 비디오를 클라이언트 Media Player 애플리케이션에서 스트리밍할 수 있도록 Media Services 엔드포인트를 시작합니다.

* Azure Portal에서 **lva-rg** 리소스 그룹으로 이동합니다.
* **스트리밍 엔드포인트**를 엽니다.
* **스트리밍 엔드포인트 세부 정보** 페이지에서 **시작**을 선택합니다. 엔드포인트가 시작되면 대금 청구가 시작된다는 경고가 표시됩니다.

## <a name="view-stored-video"></a>저장된 비디오 보기

카메라를 감시하다가 수상한 이미지에 대응하는 시대는 끝났습니다. 유추 감지를 사용하는 자동 이벤트 태깅 및 저장된 비디오에 대한 직접 링크를 이용하면 보안 운영자는 목록에서 관심 있는 이벤트를 찾아 링크를 따라 이동하여 비디오를 볼 수 있습니다.

[AMP 비디오 플레이어](https://github.com/Azure/live-video-analytics/tree/master/utilities/amp-viewer)를 사용하여 Azure Media Services 계정에 저장된 비디오를 볼 수 있습니다.

IoT Central 애플리케이션은 Azure Media Services에 비디오를 저장하며, 여기서 비디오를 스트리밍할 수 있습니다. Azure Media Services에 저장된 비디오를 재생하려면 비디오 플레이어가 필요합니다.

**Docker**가 로컬 머신에서 실행되고 있는지 확인합니다.

명령 프롬프트를 열고 다음 명령을 사용하여 로컬 머신의 Docker 컨테이너에서 비디오 플레이어를 실행합니다. 명령의 자리 표시자를 앞에서 *scratchpad.txt* 파일에 기록해 둔 값으로 바꿉니다. 앞에서 Media Services 계정의 서비스 주체를 만들 때 `amsAadClientId`, `amsAadSecret`, `amsAadTenantId`, `amsSubscriptionId` 및 `amsAccountName`을 기록해 두었습니다.

```bash
docker run -it --rm -e amsAadClientId="<FROM_AZURE_PORTAL>" -e amsAadSecret="<FROM_AZURE_PORTAL>" -e amsAadTenantId="<FROM_AZURE_PORTAL>" -e amsArmAadAudience="https://management.core.windows.net" -e amsArmEndpoint="https://management.azure.com" -e amsAadEndpoint="https://login.microsoftonline.com" -e amsSubscriptionId="<FROM_AZURE_PORTAL>" -e amsResourceGroup="<FROM_AZURE_PORTAL>" -e amsAccountName="<FROM_AZURE_PORTAL>" -p 8094:8094 mcr.microsoft.com/lva-utilities/amp-viewer:1.0-amd64
```

|Docker 매개 변수 | AMS API 액세스(JSON)|
|-|-|
|amsAadClientId| AadClientId|
|amsAadSecret| AadSecret |
|amsAadTenantId| AadTenantId |
|amsSubscriptionId| SubscriptionId|
|amsResourceGroup| ResourceGroup |
|amsAccountName| AccountName|

**camera-003** 디바이스로 이동하여 **대시보드** 탭을 선택합니다. 그런 다음, **유추 이벤트 비디오** 타일에서 캡처된 개체 감지 하이퍼링크 중 하나를 클릭합니다. 로컬 비디오 플레이어가 표시하는 페이지에 비디오가 나타납니다.

:::image type="content" source="media/tutorial-video-analytics-manage/video-snippet.png" alt-text="비디오 코드 조각":::

## <a name="change-the-simulated-devices-in-application-dashboards"></a>애플리케이션 대시보드에서 시뮬레이션된 디바이스 변경

애플리케이션 대시보드는 원래 IoT Central 시뮬레이션된 디바이스에서 생성된 원격 분석 및 속성으로 채워집니다. 실제 카메라 또는 Live555 시뮬레이터에서 원격 분석에 대한 타일을 구성하려면 다음 단계를 수행합니다.

1. **(샘플) 실제 카메라 모니터** 애플리케이션 대시보드로 이동합니다.
1. **편집**을 선택합니다.
1. **메모** 타일을 선택하고 삭제합니다.
1. 대시보드 제목을 *실제 카메라 모니터*로 변경합니다.
1. **유추 횟수** 타일에서 구성 아이콘을 선택합니다.
1. **차트 구성** 섹션의 **LVA Edge 개체 감지기** 디바이스 그룹에서 실제 카메라를 하나 이상 선택합니다.
1. `AI Inference Interface/Inference Count` 원격 분석 필드를 선택합니다.
1. **업데이트**를 선택합니다.

1. 다음 타일에 대한 단계를 반복합니다.
    1. **감지** 원형 차트는 `AI Inference Interface/Inference/entity/tag/value` 원격 분석 유형을 사용합니다.
    1. **유추**는 마지막으로 알려진 `AI Inference Interface/Inference/entity/tag/value` 값을 사용합니다.
    1. **신뢰도 %** 는 마지막으로 알려진 `AI Inference Interface/Inference/entity/tag/confidence` 값을 사용합니다.
    1. **스냅샷**은 이미지로 표시된 `AI Inference Interface/Inference Image`를 사용합니다.
    1. **유추 이벤트 비디오**는 링크로 표시된 `AI Inference Interface/Inference Event Video`를 사용합니다.
1. **저장**을 선택합니다.

이제 **실제 카메라 모니터링** 대시보드는 다음과 같이 실제 카메라 디바이스의 값을 표시합니다.

:::image type="content" source="media/tutorial-video-analytics-manage/update-real-cameras.png" alt-text="실제 카메라 애플리케이션 대시보드":::

## <a name="pause-processing"></a>처리 일시 중지

다음과 같이 애플리케이션에서 라이브 비디오 분석 처리를 일시 중지할 수 있습니다.

1. 애플리케이션에서 개체 감지 카메라의 **명령** 페이지로 이동합니다. **LVA 처리 중지** 명령을 실행합니다.

1. 다음과 같이 Media Services 계정의 스트리밍 엔드포인트를 중지합니다.
    * Azure Portal에서 **lva-rg** 리소스 그룹으로 이동합니다.
    * **스트리밍 엔드포인트** 리소스를 클릭합니다.
    * **스트리밍 엔드포인트 세부 정보** 페이지에서 **중지**를 선택합니다.

## <a name="tidy-up"></a>정리

애플리케이션 사용을 마친 후에는 앞에서 만든 모든 리소스를 다음과 같이 제거할 수 있습니다.

1. IoT Central 애플리케이션의 **관리** 섹션에서 **사용자의 애플리케이션** 페이지로 이동합니다. 그런 다음, **삭제**를 선택합니다.
1. Azure Portal에서 **lva-rg** 리소스 그룹을 삭제합니다.
1. 로컬 머신에서 **amp-viewer** Docker 컨테이너를 중지합니다.

## <a name="next-steps"></a>다음 단계

IoT Central 애플리케이션에 카메라를 추가하고 개체 및 동작 감지가 가능하도록 구성하는 방법을 배웠습니다.

IoT Edge 모듈의 소스 코드를 사용자 지정하는 방법은 아래 문서를 참조하세요.

> [!div class="nextstepaction"]
> [라이브 비디오 분석 게이트웨이 모듈 수정 및 빌드](./tutorial-video-analytics-build-module.md)
