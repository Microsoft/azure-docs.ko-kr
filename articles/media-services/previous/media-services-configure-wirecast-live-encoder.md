---
title: 단일 비트 전송률 라이브 스트림을 보낼 Telestream Wirecast 인코더 구성 | Microsoft 문서
description: '이 항목에서는 Wirecast 라이브 인코더를 구성하여 라이브 인코딩에 대해 활성화된 AMS 채널에 단일 비트 전송률 스트림을 보내는 방법을 보여 줍니다. '
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: d0da69601bfc6fd09c10b30d45195722781d87d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61232093"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>단일 비트 전송률 라이브 스트림을 보낼 Wirecast 인코더 사용 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

이 문서에서는 [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) 라이브 인코더를 구성하여 라이브 인코딩에 대해 활성화된AMS 채널에 단일 비트 전송률 스트림을 보내는 방법을 보여 줍니다.  자세한 내용은 [Azure Media Services를 사용하여 Live Encoding을 수행할 수 있는 채널 작업](media-services-manage-live-encoder-enabled-channels.md)을 참조하세요.

이 자습서에서는 AMSE(Azure Media Services 탐색기) 도구를 사용하여 AMS(Azure Media Services)를 관리하는 방법을 보여 줍니다. 이 도구는 Windows PC에서만 실행됩니다. Mac 또는 Linux에서는 Azure Portal을 사용하여 [채널](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) 및 [프로그램](media-services-portal-creating-live-encoder-enabled-channel.md)을 만듭니다.

## <a name="prerequisites"></a>필수 조건
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

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. 채널 이름을 지정합니다. 설명 필드는 선택 사항입니다. [채널 설정]에서 [Live Encoding] 옵션에 대해 **표준**을 선택하고, [입력 프로토콜]을 **RTMP**.로 설정합니다. 다른 모든 설정은 그대로 유지할 수 있습니다.

    **새 채널 지금 시작** 이 선택되었는지 확인합니다.

3. **채널 만들기**를 클릭합니다.

   ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> 채널을 시작하는 데 20분 정도 걸릴 수 있습니다.
>
>

채널을 시작하는 동안 [인코더 구성](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp)을 수행할 수 있습니다.

> [!IMPORTANT]
> 채널이 준비 상태가 되는 즉시 요금이 청구되기 시작합니다. 자세한 내용은 [채널 상태](media-services-manage-live-encoder-enabled-channels.md#states)를 참조하세요.
>
>

## <a name="a-idconfigurewirecastrtmp-configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Telestream Wirecast 인코더 구성
이 자습서에서는 다음 출력 설정이 사용됩니다. 이 섹션의 나머지 부분에서 구성 단계를 더 자세히 설명합니다.

**비디오**:

* 코덱: H.264
* 프로필 높음(수준 4.0)
* Bitrate: 5000kbps
* 키 프레임: 2초(60초)
* 프레임 속도: 30

**오디오**:

* 코덱: AAC(LC)
* Bitrate: 192kbps
* 샘플링 속도: 44.1kHz

### <a name="configuration-steps"></a>구성 단계
1. 사용 중인 컴퓨터에서 Telestream Wirecast 애플리케이션을 열고 RTMP 스트리밍을 사용하도록 설정합니다.
2. **출력** 탭으로 이동하고 **출력 설정...** 을 선택하여 출력을 구성합니다.

    **출력 대상**을 **RTMP 서버**로 설정해야 합니다.
3. **확인**을 클릭합니다.
4. 설정 페이지에서 **대상** 필드를 **Azure Media Services**로 설정합니다.

    Encoding 프로필은 **Azure H.264 720p 16:9(1280x720)** 로 미리 선택되어 있습니다. 이 설정을 사용자 지정하려면 드롭다운 오른쪽에 있는 기어 아이콘을 선택한 다음 **새 미리 설정**을 선택합니다.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. 인코더 기본 설정을 구성합니다.

    기본 설정의 이름을 지정하고 다음 권장 설정을 확인합니다.

    **비디오**

   * 인코더: MainConcept H.264
   * 초당 프레임 수: 30
   * 평균 비트 전송률: 5000kbits/sec(네트워크 제한 사항에 따라 조정 가능)
   * 프로필 기본
   * 키 프레임 간격: 60프레임

     **오디오**

   * 대상 비트 전송률: 192kbits/sec
   * 샘플링 속도: 44.100kHz

     ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. **저장**을 누릅니다.

    새로 만들어진 프로필을 이제 인코딩 필드에서 선택할 수 있게 됩니다.

    새 프로필이 선택되었는지 확인합니다.
7. Wirecast **RTMP 엔드포인트**에 할당하기 위해 채널 입력 URL을 가져옵니다.

    AMSE 도구로 다시 이동하여 채널 완료 상태를 확인합니다. 상태가 **시작 중**에서 **실행 중**으로 변경되었으면 입력 URL을 가져올 수 있습니다.

    채널이 실행 중일 때 채널 이름을 마우스 오른쪽 단추로 클릭하고 아래로 이동하여 **입력 URL을 클립보드로 복사**를 가리킨 다음 **기본 입력 URL**을 선택합니다.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. Wirecast **출력 설정** 창에서 출력 섹션의 **주소** 필드에 이 정보를 붙여넣고 스트림 이름을 할당합니다.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. **확인**을 선택합니다.
2. 기본 **Wirecast** 화면에서 비디오 및 오디오용 입력 원본이 준비되었는지 확인한 다음 왼쪽 위 모서리에 있는 **스트림**을 누릅니다.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> **스트림**을 클릭하기 전에 채널이 준비되었는지 **반드시** 확인합니다.
> 또한 15분을 초과할 때까지 입력 기여 피드 없이 채널을 준비 상태로 그대로 두지 않도록 해야 합니다.
>
>

## <a name="test-playback"></a>테스트 재생

AMSE 도구로 이동하고 테스트할 채널을 마우스 오른쪽 단추로 클릭합니다. 메뉴에서 **미리 보기 재생**을 가리키고 **Azure Media Player 사용**을 선택합니다.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

스트림이 플레이어에 나타나면 인코더가 AMS에 연결되도록 제대로 구성된 것입니다.

오류가 수신되면 채널을 다시 설정해서 인코더 설정을 조정해야 합니다. 지침에 대해서는 [문제 해결](media-services-troubleshooting-live-streaming.md) 문서를 참조하세요.  

## <a name="create-a-program"></a>프로그램 만들기
1. 채널 재생이 확인되면 프로그램을 만듭니다. AMSE 도구의 **라이브** 탭에서 프로그램 영역 안을 마우스 오른쪽 단추로 클릭하고 **새 프로그램 만들기**를 선택합니다.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. 프로그램 이름을 지정하고 필요한 경우 **보관 창 길이**(기본값은 4시간)를 조정합니다. 또한 저장소 위치를 지정하거나 기본값을 그대로 둘 수 있습니다.  
3. **지금 프로그램 시작** 상자를 선택합니다.
4. **프로그램 만들기**를 클릭합니다.  

   >[!NOTE]
   >프로그램 만들기는 채널 만들기보다 시간이 덜 걸립니다.
       
5. 프로그램이 실행되고 있으면 프로그램을 마우스 오른쪽 단추로 클릭하고 **프로그램 재생**으로 이동한 다음 **Azure Media Player 사용**을 선택하여 재생을 확인합니다.  
6. 확인되었으면 프로그램을 마우스 오른쪽 단추로 다시 클릭하고 **출력 URL을 클립보드로 복사**를 선택하거나 메뉴의 **프로그램 정보 및 설정** 옵션에서 이 정보를 검색합니다.

이제 스트림을 플레이어에 포함하거나 실시간 보기를 위해 대상 그룹에게 배포할 준비가 되었습니다.  

## <a name="troubleshooting"></a>문제 해결
지침에 대해서는 [문제 해결](media-services-troubleshooting-live-streaming.md) 문서를 참조하세요.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
