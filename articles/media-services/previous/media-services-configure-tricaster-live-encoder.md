---
title: NewTek TriCaster 인코더를 구성하여 단일 비트 전송률 라이브 스트림 보내기 | Microsoft 문서
description: 이 항목에서는 Tricaster 라이브 인코더를 구성하여 라이브 인코딩에 사용할 수 있는 AMS 채널에 단일 비트 전송률 스트림을 보내는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 7909fbb958a66d00616d4ed1b844d02bb47d997e
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152502"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>NewTek TriCaster 인코더를 사용하여 단일 비트 전송률 라이브 스트림 보내기  
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

이 문서에서는 [NewTek TriCaster](https://newtek.com/products/tricaster-40.html) 라이브 인코더를 구성하여 라이브 인코딩에 사용할 수 있는 AMS 채널에 단일 비트 전송률 스트림을 보내는 방법을 보여 줍니다. 자세한 내용은 [Azure Media Services를 사용하여 Live Encoding을 수행할 수 있는 채널 작업](media-services-manage-live-encoder-enabled-channels.md)을 참조하세요.

이 자습서에서는 AMSE(Azure Media Services 탐색기) 도구를 사용하여 AMS(Azure Media Services)를 관리하는 방법을 보여 줍니다. 이 도구는 Windows PC에서만 실행됩니다. Mac 또는 Linux에서는 Azure Portal을 사용하여 [채널](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) 및 [프로그램](media-services-portal-creating-live-encoder-enabled-channel.md)을 만듭니다.

기여 피드에서 라이브 인코딩에 사용할 수 있는 AMS 채널에 보내기 위해 Tricaster를 사용할 때 피드 간에 빠르게 잘라내기, 슬레이트 간 전환과 같은 Tricaster의 특정 기능을 사용하는 경우 라이브 이벤트에 비디오/오디오 결함이 있을 수 있습니다. AMS 팀에서 이러한 문제를 해결하기 위해 노력하고 있으며 그때까지 이 기능을 사용하지 않는 것이 좋습니다.

> [!NOTE]
>  Prefferred TLS 버전인 TLS 1.2로 이동 하는 것이 좋습니다.

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

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. 채널 이름을 지정합니다. 설명 필드는 선택 사항입니다. [채널 설정]에서 [Live Encoding] 옵션에 대해 **표준**을 선택하고, [입력 프로토콜]을 **RTMP**.로 설정합니다. 다른 모든 설정은 그대로 유지할 수 있습니다.

    **새 채널 지금 시작** 이 선택되었는지 확인합니다.

3. **채널 만들기**를 클릭합니다.

   ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> 채널을 시작하는 데 20분 정도 걸릴 수 있습니다.
>
>

채널을 시작하는 동안 [인코더 구성](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp)을 수행할 수 있습니다.

> [!IMPORTANT]
> 채널이 준비 상태가 되는 즉시 요금이 청구되기 시작합니다. 자세한 내용은 [채널 상태](media-services-manage-live-encoder-enabled-channels.md#states)를 참조하세요.
>
>

## <a name="a-idconfigure_tricaster_rtmpconfigure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>NewTek TriCaster 인코더 구성

이 자습서에서는 다음 출력 설정이 사용됩니다. 이 섹션의 나머지 부분에서 구성 단계를 더 자세히 설명합니다.

**비디오**:

* 코덱: H.264
* 프로필: 높음(수준 4.0)
* 비트 전송률: 5,000kbps
* 키 프레임: 2초(60초)
* 프레임 속도: 30

**오디오**:

* 코덱: AAC(LC)
* 비트 전송률: 192kbps
* 샘플 속도: 44.1khz

### <a name="configuration-steps"></a>구성 단계

1. 사용 중인 비디오 입력 원본에 따라 새 **NewTek TriCaster** 프로젝트를 만듭니다.
2. 해당 프로젝트 내에 있는 경우 **스트림** 단추를 찾고 그 옆의 기어 아이콘을 클릭하여 스트림 구성 메뉴에 액세스합니다.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. 메뉴가 열려 있으면 연결 제목 아래에서 **새로 만들기** 를 클릭합니다. 연결 형식에 대한 메시지가 표시되면 **Adobe Flash**를 선택합니다.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. **확인**을 클릭합니다.
5. 이제 **스트리밍 프로필** 아래에서 드롭다운 화살표를 클릭하고 **찾아보기**로 이동하여 FMLE 프로필을 가져올 수 있습니다.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. 구성한 FMLE 프로필이 저장된 위치로 이동합니다.
7. 선택한 다음 **확인**을 누릅니다.

    프로필이 업로드되면 다음 단계를 진행합니다.
8. Tricaster **RTMP 엔드포인트**에 할당하기 위해 채널의 입력 URL을 가져옵니다.

    AMSE 도구로 다시 이동하여 채널 완료 상태를 확인합니다. 상태가 **시작 중**에서 **실행 중**으로 변경되었으면 입력 URL을 가져올 수 있습니다.

    채널이 실행 중일 때 채널 이름을 마우스 오른쪽 단추로 클릭하고 아래로 이동하여 **입력 URL을 클립보드로 복사**를 가리킨 다음 **기본 입력 URL**을 선택합니다.  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Tricaster 프로젝트 내의 **플래시 서버** 아래의 **위치** 필드에서 이 정보를 붙여넣습니다. 또한 **스트림 ID** 필드에 스트림 이름을 할당합니다.

    또한 스트림 정보가 FMLE 프로필에 추가된 경우 **설정 가져오기**를 클릭하고 저장된 FMLE 프로필로 이동한 다음 **확인**을 클릭하여 이 섹션으로 가져올 수 있습니다. 관련 Flash Server(플래시 서버) 필드는 FMLE의 정보로 채워야 합니다.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. 완료되면 화면 아래쪽에 있는 **확인** 을 클릭합니다. 비디오 및 오디오를 Tricaster에 입력할 준비가 되면 **스트림** 단추를 클릭하여 AMS로 스트리밍을 시작합니다.

     ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> **스트림**을 클릭하기 전에 채널이 준비되었는지 **반드시** 확인합니다.
> 또한 15분을 초과할 때까지 입력 기여 피드 없이 채널을 준비 상태로 그대로 두지 않도록 해야 합니다.
>
>

## <a name="test-playback"></a>테스트 재생

AMSE 도구로 이동하고 테스트할 채널을 마우스 오른쪽 단추로 클릭합니다. 메뉴에서 **미리 보기 재생**을 가리키고 **Azure Media Player 사용**을 선택합니다.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

스트림이 플레이어에 나타나면 인코더가 AMS에 연결되도록 제대로 구성된 것입니다.

오류가 수신되면 채널을 다시 설정해서 인코더 설정을 조정해야 합니다. 지침에 대해서는 [문제 해결](media-services-troubleshooting-live-streaming.md) 문서를 참조하세요.  

## <a name="create-a-program"></a>프로그램 만들기

1. 채널 재생이 확인되면 프로그램을 만듭니다. AMSE 도구의 **라이브** 탭에서 프로그램 영역 안을 마우스 오른쪽 단추로 클릭하고 **새 프로그램 만들기**를 선택합니다.  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
2. 프로그램 이름을 지정하고 필요한 경우 **보관 창 길이**(기본값은 4시간)를 조정합니다. 또한 스토리지 위치를 지정하거나 기본값을 그대로 둘 수 있습니다.  
3. **지금 프로그램 시작** 상자를 선택합니다.
4. **프로그램 만들기**를 클릭합니다.  

    >[!NOTE]
    >프로그램 만들기는 채널 만들기보다 시간이 덜 걸립니다.
        
5. 프로그램이 실행되고 있으면 프로그램을 마우스 오른쪽 단추로 클릭하고 **프로그램 재생**으로 이동한 다음 **Azure Media Player 사용**을 선택하여 재생을 확인합니다.  
6. 확인되었으면 프로그램을 마우스 오른쪽 단추로 다시 클릭하고 **출력 URL을 클립보드로 복사**를 선택하거나 메뉴의 **프로그램 정보 및 설정** 옵션에서 이 정보를 검색합니다.

이제 스트림을 플레이어에 포함하거나 실시간 보기를 위해 대상 그룹에게 배포할 준비가 되었습니다.  

## <a name="troubleshooting"></a>문제 해결

지침에 대해서는 [문제 해결](media-services-troubleshooting-live-streaming.md) 문서를 참조하세요.

## <a name="next-step"></a>다음 단계

Media Services 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
