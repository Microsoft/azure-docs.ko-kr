---
title: OBS Studio를 사용하여 Azure Media Services 라이브 스트림 만들기
description: 포털과 OBS Studio를 사용하여 Azure Media Services 라이브 스트림을 만드는 방법을 알아봅니다.
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 08/31/2020
ms.openlocfilehash: 04b0ef0d6a480270b1f0a3546319b2608d638677
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265510"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>OBS를 사용하여 Azure Media Services 라이브 스트림 만들기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 빠른 시작은 Azure Portal 및 OBS(Open Broadcasting Studio)를 사용하여 Azure Media Services 라이브 스트림을 만드는 데 도움이 됩니다. 여기에서는 Azure 구독이 있고 Media Services 계정을 만들었다고 가정합니다.

이 빠른 시작에서 설명하는 내용은 다음과 같습니다.

- OBS를 사용하여 온-프레미스 인코더 설정
- 라이브 스트림 설정
- 라이브 스트림 출력 설정
- 기본 스트리밍 엔드포인트 실행
- Azure Media Player를 사용하여 라이브 스트림 및 주문형 출력 보기

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

먼저 웹 브라우저를 열고 [Microsoft Azure Portal](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>OBS를 사용하여 온-프레미스 인코더 설정

1. [Open Broadcaster Software 웹 사이트](https://obsproject.com/)에서 운영 체제에 맞는 OBS를 다운로드하여 설치합니다.
1. 애플리케이션을 시작하고 열어 둡니다.

## <a name="run-the-default-streaming-endpoint"></a>기본 스트리밍 엔드포인트 실행

1. Media Services 목록에서 **스트리밍 엔드포인트**를 선택합니다.

   ![스트리밍 엔드포인트 메뉴 항목](media/live-events-obs-quickstart/streaming-endpoints.png)
1. 기본 스트리밍 엔드포인트 상태가 중지됨이면 선택합니다. 그러면 해당 엔드포인트의 페이지로 이동합니다.
1. **시작**을 선택합니다.

   ![스트리밍 엔드포인트의 시작 단추](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Azure Media Services 라이브 스트림 설정

1. 포털 내의 Azure Media Services 계정으로 이동한 후 **Media Services** 목록에서 **라이브 스트리밍**을 선택합니다.

   ![라이브 스트리밍 링크](media/live-events-obs-quickstart/select-live-streaming.png)
1. **라이브 이벤트 추가**를 선택하여 새 라이브 스트리밍 이벤트를 만듭니다.

   ![라이브 이벤트 추가 아이콘](media/live-events-obs-quickstart/add-live-event.png)
1. **라이브 이벤트 이름** 상자에 새 이벤트의 이름(예: *TestLiveEvent*)을 입력합니다.

   ![라이브 이벤트 이름 상자](media/live-events-obs-quickstart/live-event-name.png)
1. **설명** 상자에 이벤트에 대한 설명(선택 사항)을 입력합니다.
1. **통과 – 클라우드 인코딩 없음** 옵션을 선택합니다.

   ![클라우드 인코딩 옵션](media/live-events-obs-quickstart/cloud-encoding.png)
1. **RTMP** 옵션을 선택합니다.
1. 라이브 이벤트가 준비되기 전에 이 이벤트에 대한 요금이 청구되지 않도록 **시작 라이브 이벤트**에 대해 **아니요** 옵션을 선택해야 합니다. (라이브 이벤트가 시작되면 청구가 시작됩니다.)

   ![라이브 이벤트 시작 옵션](media/live-events-obs-quickstart/start-live-event-no.png)
1. **검토 + 만들기** 단추를 선택하여 설정을 검토합니다.
1. **만들기** 단추를 선택하여 라이브 이벤트를 만듭니다. 그러면 라이브 이벤트 목록으로 돌아갑니다.
1. 방금 만든 라이브 이벤트의 링크를 선택합니다. 이벤트가 중지되었습니다.
1. 브라우저에서 이 페이지를 열어 둡니다. 나중에 다시 살펴보겠습니다.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>OBS Studio를 사용하여 라이브 스트림 설정

OBS는 기본 장면으로 시작하지만 입력이 선택되지 않았습니다.

   ![OBS 기본 화면](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>비디오 원본 추가

1. **원본** 패널에서 **추가** 아이콘을 클릭하여 새 원본 디바이스를 선택합니다. **원본** 메뉴가 열립니다.

1. 원본 디바이스 메뉴에서 **비디오 캡처 디바이스**를 선택합니다. **원본 만들기/선택** 메뉴가 열립니다.

   ![비디오 디바이스가 선택된 OBS 원본 메뉴](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. **기존 항목 추가** 라디오 단추를 선택한 다음, **확인**을 클릭합니다. **비디오 디바이스의 속성** 메뉴가 열립니다.

   ![기존 항목 추가가 선택된 OBS 새 비디오 원본 메뉴](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. **디바이스** 드롭다운 목록에서 브로드캐스트에 사용하려는 비디오 입력을 선택합니다. 지금은 나머지 설정만 그대로 두고 **확인**을 클릭합니다. 입력 원본이 **원본** 패널에 추가되고, 비디오 입력 보기가 **미리 보기** 영역에 표시됩니다.

   ![OBS 카메라 설정](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>오디오 원본 추가

1. **원본** 패널에서 **추가** 아이콘을 클릭하여 새 원본 디바이스를 선택합니다. [원본 디바이스] 메뉴가 열립니다.

1. 원본 디바이스 메뉴에서 **오디오 입력 캡처**를 선택합니다. **원본 만들기/선택** 메뉴가 열립니다.

   ![오디오 디바이스가 선택된 OBS 원본 메뉴](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. **기존 항목 추가** 라디오 단추를 선택한 다음, **확인**을 클릭합니다. **오디오 입력 캡처의 속성** 메뉴가 열립니다.

   ![기존 항목 추가가 선택된 OBS 오디오 원본 ](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. **디바이스** 드롭다운 목록에서 브로드캐스트에 사용하려는 오디오 캡처 디바이스를 선택합니다. 지금은 나머지 설정만 그대로 두고 [확인]을 클릭합니다. 오디오 캡처 디바이스가 오디오 믹서 패널에 추가됩니다.

   ![OBS 오디오 디바이스 선택 드롭다운 목록](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-in-obs"></a>OBS에서 스트리밍 설정

다음 절차에서는 브라우저의 Azure Media Services로 돌아가서 입력 URL을 복사하여 출력 설정에 입력합니다.

1. 포털의 Azure Media Services 페이지에서 **시작**을 선택하여 라이브 스트림 이벤트를 시작합니다. (이제 청구가 시작됩니다.)

   ![시작 아이콘](media/live-events-obs-quickstart/start.png)
1. **RTMP** 토글을 **RTMPS**로 설정합니다.
1. **입력 URL** 상자에서 URL을 클립보드에 복사합니다.

   ![입력 URL](media/live-events-obs-quickstart/input-url.png)

1. OBS 애플리케이션으로 전환합니다.

1. **컨트롤** 패널에서 **설정** 단추를 클릭합니다. [설정] 옵션이 열립니다.

   ![설정이 선택된 OBS 컨트롤 패널](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. **설정** 메뉴에서 **스트림**을 선택합니다.

1. **서비스** 드롭다운 목록에서 [모두 표시]를 선택한 다음, **사용자 지정...** 을 선택합니다.

1. **서버** 필드에서 복사한 RTMPS URL을 클립보드에 붙여넣습니다.

1. **스트림 키** 필드에 임의로 입력합니다.  실제로 중요하지는 않지만 값이 있어야 합니다.

    ![OBS 스트림 설정](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. **설정** 메뉴에서 **출력**을 선택합니다.

1. **키프레임 간격** 필드에서 *2*를 입력합니다. 그러면 조각 길이가 2초로 설정됩니다. 대기 시간이 짧은 라이브 전송의 경우 1초의 값을 사용합니다.

1. 선택 사항: 처리 성능이 낮은 컴퓨터를 사용하는 경우 **CPU 사용량 미리 설정**을 *veryfast*로 설정합니다. 원하지 않는 네트워크 조건이 있는 경우 필요에 따라 Kbps를 더 낮게 설정할 수 있습니다.

   ![OBS 출력 설정](media/live-events-obs-quickstart/live-event-obs-advanced-output-settings.png)

1. 나머지 설정은 변경하지 않은 상태로 두고 **확인**을 클릭합니다.

### <a name="start-streaming"></a>스트리밍 시작

1. **컨트롤** 패널에서 **스트리밍 시작**을 클릭합니다.

    ![OBS 스트리밍 시작 단추](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. 브라우저에서 Azure Media Services 라이브 이벤트 화면으로 전환하고, **플레이어 다시 로드** 링크를 클릭합니다. 이제 스트림이 [미리 보기] 플레이어에 표시됩니다.

## <a name="set-up-outputs"></a>출력 설정

여기서는 출력을 설정하고, 라이브 스트림의 기록을 저장할 수 있도록 합니다.  

> [!NOTE]
> 이 출력을 스트리밍하려면 스트리밍 엔드포인트가 실행되고 있어야 합니다. 나중에 [기본 스트리밍 엔드포인트 실행](#run-the-default-streaming-endpoint) 섹션을 참조하세요.

1. **출력** 비디오 뷰어의 아래에 있는 **출력 만들기** 링크를 선택합니다.
1. 원하는 경우 나중에 쉽게 찾을 수 있도록 **이름** 상자에서 출력 이름을 사용자에게 더 친숙한 이름으로 편집합니다.

   ![출력 이름 상자](media/live-events-wirecast-quickstart/output-name.png)
1. 지금은 나머지 상자를 모두 그대로 둡니다.
1. **다음**을 선택하여 스트리밍 로케이터를 추가합니다.
1. 원하는 경우 로케이터 이름을 사용자에게 더 친숙한 이름으로 변경합니다.

   ![로케이터 이름 필드](media/live-events-wirecast-quickstart/live-event-locator.png)
1. 지금은 이 화면의 다른 모든 항목을 그대로 둡니다.
1. **만들기**를 선택합니다.

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>Azure Media Player를 사용하여 출력 브로드캐스트 재생

1. **출력** 비디오 플레이어 아래에 스트리밍 URL을 복사합니다.
1. 웹 브라우저에서 [Azure Media Player 데모](https://ampdemo.azureedge.net/azuremediaplayer.html)를 엽니다.
1. 스트리밍 URL을 Azure Media Player의 **URL** 상자에 붙여넣습니다.
1. **플레이어 업데이트** 단추를 선택합니다.
1. 비디오에서 **재생** 아이콘을 선택하여 라이브 스트림을 봅니다.

## <a name="stop-the-broadcast"></a>브로드캐스트 중지

콘텐츠를 충분히 스트리밍했다고 생각되면 브로드캐스트를 중지합니다.

1. 포털에서 **중지**를 선택합니다.

1. OBS의 **컨트롤** 패널에서 **스트리밍 중지** 단추를 선택합니다. 이 단계에서는 OBS에서 브로드캐스트가 중지됩니다.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>Azure Media Player를 사용하여 주문형 출력 재생

이제 스트리밍 엔드포인트가 실행되는 동안에는 사용자가 만든 출력을 주문형 스트리밍에 사용할 수 있습니다.

1. Media Services 목록으로 이동하여 **자산**을 선택합니다.
1. 이전에 만든 이벤트 출력을 찾아서 자산에 대한 링크를 선택합니다. 자산 출력 페이지가 열립니다.
1. 자산에 대한 비디오 플레이어 아래에 스트리밍 URL을 복사합니다.
1. 브라우저에서 Azure Media Player로 돌아가서 스트리밍 URL을 URL 상자에 붙여넣습니다.
1. **플레이어 업데이트**를 선택합니다.
1. 비디오에서 **재생** 아이콘을 선택하여 주문형 자산을 봅니다.

## <a name="clean-up-resources"></a>리소스 정리

> [!IMPORTANT]
> 서비스를 중지하세요! 이 빠른 시작 단계를 완료한 후에는 라이브 이벤트 및 스트리밍 엔드포인트를 중지해야 합니다. 그렇지 않으면 계속 실행되는 시간에 대한 요금이 청구됩니다. 라이브 이벤트를 중지하려면 [브로드캐스트 중지](#stop-the-broadcast) 절차의 2단계와 3단계를 참조하세요.

스트리밍 엔드포인트를 중지하려면:

1. Media Services 목록에서 **스트리밍 엔드포인트**를 선택합니다.
2. 이전에 시작한 기본 스트리밍 엔드포인트를 선택합니다. 그러면 엔드포인트 페이지가 열립니다.
3. **중지**를 선택합니다.

> [!TIP]
> 이 이벤트의 자산을 유지하지 않으려면 스토리지에 대한 요금이 청구되지 않도록 해당 자산을 삭제해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Media Services의 라이브 이벤트 및 라이브 출력](./live-events-outputs-concept.md)
