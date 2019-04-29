---
title: Haivision KB 인코더를 구성하여 Azure에 단일 비트 전송률 라이브 스트림 보내기 | Microsoft Docs
description: 이 항목에서는 Havision KB 라이브 인코더를 구성하여 라이브 인코딩에 사용할 수 있는 AMS 채널에 단일 비트 전송률 스트림을 보내는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: dbgeorge
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;dbgeorge
ms.openlocfilehash: 058a1f964eb14d89628c92cbadd80511b7a27bae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230574"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Haivision KB 라이브 인코더를 사용하여 단일 비트 전송률 라이브 스트림 보내기  
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

이 항목에서는 [Havision KB 라이브 인코더](https://www.haivision.com/products/kb-series/)를 구성하여 라이브 인코딩에 사용할 수 있는 AMS 채널에 단일 비트 전송률 스트림을 보내는 방법을 보여 줍니다. 자세한 내용은 [Azure Media Services를 사용하여 Live Encoding을 수행할 수 있는 채널 작업](media-services-manage-live-encoder-enabled-channels.md)을 참조하세요.

이 자습서에서는 AMSE(Azure Media Services 탐색기) 도구를 사용하여 AMS(Azure Media Services)를 관리하는 방법을 보여 줍니다. 이 도구는 Windows PC에서만 실행됩니다. Mac 또는 Linux에서는 Azure Portal을 사용하여 [채널](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) 및 [프로그램](media-services-portal-creating-live-encoder-enabled-channel.md)을 만듭니다.

## <a name="prerequisites"></a>필수 조건
*   SSW v5.01 이상을 실행하여 Haivision KB 인코더에 액세스합니다.
* [Azure Media Services 계정 만들기](media-services-portal-create-account.md)
* 실행 중인 스트리밍 엔드포인트가 있는지 확인합니다. 자세한 내용은 [Media Services 계정에서 스트리밍 엔드포인트 관리](media-services-portal-manage-streaming-endpoints.md)
* 최신 버전의 [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) 도구를 설치합니다.
* 이 도구를 시작하고 AMS 계정에 연결합니다.

## <a name="tips"></a>팁
* 가능하면 하드웨어에 내장된 인터넷 연결을 사용합니다.
* 대역폭 요구 사항을 결정할 때에 가장 좋은 방법은 스트리밍 비트 전송률을 두 배로 하는 것입니다. 이는 필수 요구 사항은 아니지만 네트워크 정체로 인한 영향을 줄이는 데 도움이 됩니다.
* 소프트웨어 기반 인코더를 사용하는 경우 불필요한 프로그램을 모두 닫습니다.

## <a name="create-a-channel"></a>채널 만들기
1. AMSE 도구에서 **라이브** 탭으로 이동하고 채널 영역 안을 마우스 오른쪽 단추로 클릭합니다. 메뉴에서 **Create channel...** (채널 만들기...)을 선택합니다.
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. 채널 이름을 지정합니다. 설명 필드는 선택 사항입니다. [채널 설정]에서 [Live Encoding] 옵션에 대해 **표준**을 선택하고, [입력 프로토콜]을 **RTMP**.로 설정합니다. 다른 모든 설정은 그대로 유지할 수 있습니다. **새 채널 지금 시작** 이 선택되었는지 확인합니다.
3. **채널 만들기**를 클릭합니다.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> 채널을 시작하는 데 20분 정도 걸릴 수 있습니다.

## <a name="configure-the-haivision-kb-encoder"></a>Haivision KB 인코더 구성
이 자습서에서는 다음 출력 설정이 사용됩니다. 이 섹션의 나머지 부분에서 구성 단계를 더 자세히 설명합니다.

비디오:
-   코덱: H.264
-   프로필 높음(수준 4.0)
-   Bitrate: 5000kbps
-   키 프레임: 2초(60프레임)
-   프레임 속도: 30

Audio:
-   코덱: AAC(LC)
-   Bitrate: 192kbps
-   샘플링 속도: 44.1kHz

## <a name="configuration-steps"></a>구성 단계
1.  Haivision KB 사용자 인터페이스에 로그인합니다.
2.  채널 제어 센터에서 **메뉴 단추**를 클릭하고 **채널 추가**를 선택합니다.  
    ![스크린샷 2017-08-14, 오전 9.15.09](./media/media-services-configure-kb-live-encoder/step2.png)
3.  이름 필드에 **채널 이름**을 입력하고 다음을 클릭합니다.  
    ![스크린샷 2017-08-14, 오전 9.19.07](./media/media-services-configure-kb-live-encoder/step3.png)
4.  **입력 소스** 드롭 다운에서 **채널 입력 소스**를 선택하고 다음을 클릭합니다.
    ![스크린샷 2017-08-14, 오전 9.20.44](./media/media-services-configure-kb-live-encoder/step4.png)
5.  **Encoder Template** 드롭다운에서 **H264-720-AAC-192**를 선택하고 다음을 클릭합니다.
    ![스크린샷 2017-08-14, 오전 9.23.15](./media/media-services-configure-kb-live-encoder/step5.png)
6.  **새 출력 선택** 드롭다운에서 **RTMP**를 선택하고 다음을 클릭합니다.  
    ![스크린샷 2017-08-14, 오전 9.27.51](./media/media-services-configure-kb-live-encoder/step6.png)
7.  **채널 출력** 창에서 Azure 스트림 정보를 채웁니다. **서버** 영역에 있는 초기 채널 설치 프로그램에서 **RTMP** 링크를 붙여 넣습니다. **출력 이름** 영역에 채널의 이름을 입력합니다. Stream Name Template 영역에서 스트림에 이름을 지정하려면 템플릿 RTMPStreamName_%video_bitrate%를 사용합니다.
    ![스크린샷 2017-08-14, 오전 9.33.17](./media/media-services-configure-kb-live-encoder/step7.png)
8.  다음을 클릭한 후 마침을 클릭합니다.
9.  인코더가 채널을 시작하려면 **재생 단추**를 클릭합니다.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>테스트 재생
AMSE 도구로 이동하고 테스트할 채널을 마우스 오른쪽 단추로 클릭합니다. 메뉴에서 미리 보기 재생을 가리키고 Azure Media Player 사용을 선택합니다.

스트림이 플레이어에 나타나면 인코더가 AMS에 연결되도록 제대로 구성된 것입니다.

오류가 수신되면 채널을 다시 설정해서 인코더 설정을 조정해야 합니다. 지침에 대해서는 문제 해결 문서를 참조하세요.

## <a name="create-a-program"></a>프로그램 만들기
1.  채널 재생이 확인되면 프로그램을 만듭니다. AMSE 도구의 라이브 탭에서 프로그램 영역 안을 마우스 오른쪽 단추로 클릭하고 새 프로그램 만들기를 선택합니다.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  AMSE 도구의 라이브 탭에서 프로그램 영역 안을 마우스 오른쪽 단추로 클릭하고 새 프로그램 만들기를 선택합니다. 또한 저장소 위치를 지정하거나 기본값을 그대로 둘 수 있습니다.
2.  지금 프로그램 시작 상자를 선택합니다.
3.  프로그램 만들기를 클릭합니다.
4.  프로그램이 실행되고 있으면 프로그램을 마우스 오른쪽 단추로 클릭하고 프로그램 재생으로 이동한 다음 Azure Media Player 사용을 선택하여 재생을 확인합니다.
5.  확인되었으면 프로그램을 마우스 오른쪽 단추로 다시 클릭하고 출력 URL을 클립보드로 복사를 선택하거나 메뉴의 프로그램 정보 및 설정 옵션에서 이 정보를 검색합니다.

이제 스트림을 플레이어에 포함하거나 실시간 보기를 위해 대상 그룹에게 배포할 준비가 되었습니다.

> [!NOTE]
> 프로그램 만들기는 채널 만들기보다 시간이 덜 걸립니다.
