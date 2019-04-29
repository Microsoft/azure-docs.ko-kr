---
title: 고급 미디어 인코더 Premium 워크플로 자습서
description: 이 문서는 미디어 인코더 Premium 워크플로를 사용한 고급 작업을 수행하는 방법 및 워크플로 디자이너르 사용한 복잡한 워크플로를 만드는 방법을 보여주는 연습을 포함합니다.
services: media-services
documentationcenter: ''
author: xstof
manager: femila
editor: ''
ms.assetid: 1ba52865-b4a8-4ca0-ac96-920d55b9d15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: christoc;xpouyat;juliako
ms.openlocfilehash: d227e3618c138e6661cc4be7caa2b9a3ba1af3f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61241554"
---
# <a name="advanced-media-encoder-premium-workflow-tutorials"></a>고급 미디어 인코더 Premium 워크플로 자습서
## <a name="overview"></a>개요
이 문서는 **워크플로 디자이너**를 사용하여 워크플로를 사용자 지정하는 방법을 보여주는 연습을 포함하고 있습니다. [여기](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples)서 실제 워크플로 파일을 찾을 수 있습니다.  

## <a name="toc"></a>목차
다음 항목이 다루어집니다.

* [단일 비트 전송률 MP4로 MXF Encoding](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
  * [새 워크플로 시작](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new)
  * [미디어 파일 입력 사용](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
  * [미디어 스트림 검사](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
  * [MP4 파일 생성을 위해 비디오 인코더 추가](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
  * [오디오 스트림 인코딩](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
  * [MP4 컨테이너에 오디오 및 비디오 스트림 멀티플렉싱](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
  * [MP4 파일 작성](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
  * [출력 파일에서 Media Services 자산 만들기](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
  * [완료된 워크플로 로컬로 테스트](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
* [다중 비트 전송률 MP4로 MXF Encoding - 동적 패키징 사용](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
  * [하나 이상의 추가 MP4 출력 추가](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
  * [파일 출력 이름 구성](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
  * [별도 오디오 트랙 추가](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
  * ["ISM" SMIL 파일 추가](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
* [다중 비트 전송률 MP4로 MXF Encoding - 향상된 청사진](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
  * 강화할 워크플로 개요
  * [파일 명명 규칙](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
  * [워크플로 루트에 구성 요소 속성 게시](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
  * [게시된 속성 값을 사용하는 출력 파일 이름 생성](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
* [다중 비트 전송률 MP4 출력에 미리 보기 추가](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
  * 미리 보기를 추가하는 워크플로 개요
  * [JPG Encoding 추가](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
  * [색 공간 변환 처리](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
  * [미리 보기 작성](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
  * [워크플로에서 오류 감지](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
  * [완료된 워크플로](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
* [다중 비트 전송률 MP4 출력의 시간 기반 트리밍](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
  * [트리밍을 추가하기 시작하려는 워크플로 개요](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
  * [스트림 트리머 사용](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
  * [완료된 워크플로](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
* [스크립팅한 구성 요소 소개](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
  * [워크플로 내의 스크립팅: Hello World](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
* [다중 비트 전송률 MP4 출력의 프레임 기반 트리밍](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
  * [트리밍을 추가하기 시작하려는 청사진 개요](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
  * [클립 목록 XML 사용](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
  * [스크립팅한 구성 요소에서 클립 목록 수정](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
  * [ClippingEnabled 편의 속성 추가](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

## <a id="MXF_to_MP4"></a>단일 비트 전송률 MP4로 MXF 인코딩
이 섹션에서는 .MXF 입력 파일에서 AAC-HE 인코딩 오디오를 사용하여 단일 비트 전송률 MP4 파일을 만드는 방법을 보여줍니다.

### <a id="MXF_to_MP4_start_new"></a>새 워크플로 시작
워크플로 디자이너를 열고 파일 > 새 작업 영역 > Transcode Blueprint(청사진 트랜스코딩)를 선택합니다.

새 워크플로는 3개의 요소를 보여 줍니다.

* 기본 원본 파일
* 클립 목록 XML
* 출력 파일/자산  

![새 Encoding 워크플로](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*새 Encoding 워크플로*

### <a id="MXF_to_MP4_with_file_input"></a>미디어 파일 입력 사용
입력 미디어 파일을 허용하려면 먼저 미디어 파일 입력 구성 요소를 추가해야 합니다. 워크플로에 구성 요소를 추가하려면 리포지토리 검색 상자에서 구성 요소를 찾고 디자이너 창으로 원하는 항목을 끕니다. 미디어 파일 입력에 대한 작업을 반복하고 미디어 파일 입력에서 파일 이름 입력 핀에 기본 원본 파일 구성 요소를 연결합니다.

![연결된 미디어 파일 입력](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*연결된 미디어 파일 입력*

처음에는 사용자 지정 워크플로를 설계할 때 사용하기 적절한 샘플 파일을 식별합니다. 이렇게 하려면 디자이너 창 배경을 클릭하고 오른쪽 속성 창에서 기본 원본 파일 속성을 찾습니다. 폴더 아이콘을 클릭하고 워크플로를 테스트할 파일을 선택합니다. 미디어 파일 입력 구성 요소는 파일을 검사하고 검사한 샘플 파일의 세부 정보가 반영되도록 출력 핀을 채웁니다.

![채워진 미디어 파일 입력](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*채워진 미디어 파일 입력*

입력이 채워지면 다음 단계는 출력 인코딩 설정을 구성하는 것입니다. 이제 기본 원본 파일의 구성 방법과 유사하게 아래의 출력 폴더 변수 속성을 구성합니다.

![구성된 입력 및 출력 속성](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*구성된 입력 및 출력 속성*

### <a id="MXF_to_MP4_streams"></a>미디어 스트림 검사
종종 워크플로를 통해 흐르는 스트림의 모양을 알고자 합니다. 워크플로의 어떤 시점에서 스트림을 검사하려면 구성 요소에서 출력 또는 입력 핀을 클릭합니다. 이 경우에 미디어 파일 입력에서 압축되지 않은 비디오 출력 핀을 클릭해봅니다. 아웃 바운드 비디오를 검사할 수 있는 대화 상자가 열립니다.

![압축되지 않은 비디오 출력 핀 검사](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*압축되지 않은 비디오 출력 핀 검사*

이 경우 거의 2분인 비디오에 대한 4:2:2 샘플링에 초 당 24프레임의 1920x1080 입력이 포함되어 있는 것이 표시됩니다.

### <a id="MXF_to_MP4_file_generation"></a>MP4 파일 생성을 위해 비디오 인코더 추가
이제 압축되지 않은 비디오 및 여러 압축되지 않은 오디오 출력 핀을 미디어 파일 입력에 사용할 수 있습니다. 인바운드 비디오를 인코딩하려면 인코딩 구성 요소를 워크플로에 추가해야 하며, 이 경우에 .MP4 파일을 생성합니다.

비디오 스트림을 H.264로 인코딩하려면 AVC 비디오 인코더 구성 요소를 디자이너 화면에 추가합니다. 이 구성 요소는 압축되지 않은 비디오 스트림을 입력으로 사용하고 AVC 압축된 비디오 스트림을 해당 출력 핀에 제공합니다.

![연결되지 않은 AVC 인코더](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*연결되지 않은 AVC 인코더*

해당 속성은 인코딩이 정확하게 발생하는 방법을 결정합니다. 몇 가지 중요한 설정을 보도록 하겠습니다.

* 출력 너비 및 출력 높이: 인코딩된 비디오의 해상도를 결정합니다. 이 경우 640x360이 좋은 설정입니다.
* 프레임 속도: 통과로 설정하면 원본 프레임 속도를 채택하지만 재정의할 수 있습니다. 이러한 프레임 속도 변환은 동작 보정되지 않습니다.
* 프로필 및 수준: AVC 프로필 및 수준을 결정합니다. 다양한 수준 및 프로필에 대한 자세한 정보를 편리하게 가져오려면 AVC 비디오 인코더 구성 요소에서 물음표 아이콘을 클릭하고 도움말 페이지는 각 수준에 대한 자세한 정보를 표시합니다. 이 예제의 경우, 수준 3.2(기본)의 기본 프로필을 사용합니다.
* 전송률 제어 모드 및 비트 전송률(kbps): 이 시나리오에서는 1200kbps에서 상수 비트 전송률(CBR) 출력을 선택합니다.
* 비디오 형식: H.264 스트림에 기록되는 VUI(Video Usability Information)에 대한 정보를 제공합니다(디코더에서 디스플레이를 향상시키는 데 사용되지만 반드시 올바르게 디코딩하는 데 사용될 필요는 없는 부가 정보).
* NTSC(30fps를 사용하는 미국 또는 일본에 일반적임)
* PAL(25fps를 사용하는 유럽에 일반적임)
* GOP 크기 모드: 여기서는 닫힌 GOP로 키 간격이 2초인 고정된 GOP 크기를 설정합니다. 2초로 설정하면 Azure Media Services에서 제공하는 동적 패키지와의 호환성이 보장됩니다.

AVC 인코더를 공급하려면 미디어 파일 입력 구성 요소에서 AVC 인코더의 압축되지 않은 비디오 입력 핀에 압축되지 않은 비디오 출력 핀을 연결합니다.

![연결된 AVC 인코더](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*연결된 AVC 기본 인코더*

### <a id="MXF_to_MP4_audio"></a>오디오 스트림 인코딩
이 시점에서 원래 압축되지 않은 오디오 스트림은 압축되어야 합니다. 오디오 스트림을 압축하려면 AAC 인코더(Dolby) 구성 요소를 워크플로에 추가합니다.

![연결되지 않은 AVC 인코더](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*연결되지 않은 AAC 인코더*

비호환성: 미디어 파일 입력에서 두 개의 압축되지 않은 오디오 스트림(왼쪽 오디오 채널 및 오른쪽 오디오 채널에 각각 하나)을 사용할 수 있는 반면 AAC 인코더에 압축되지 않은 단일 오디오 입력 핀이 있습니다. (서라운드 사운드를 처리하는 경우 6채널입니다.) 따라서 미디어 파일 입력 원본에서 AAC 오디오 인코더에 오디오를 직접 연결할 수 없습니다. AAC 구성 요소는 소위 "인터리브" 오디오 스트림이 필요합니다: 서로 인터리브된 왼쪽 및 오른쪽 채널이 있는 단일 스트림입니다. 어떤 오디오 트랙이 원본에서 어떤 위치에 있는지 원본 미디어 파일에서 알게 되면 왼쪽 및 오른쪽에 올바르게 할당된 스피커 위치로 이러한 인터리브 오디오 스트림을 생성할 수 있습니다.

먼저 필요한 원본 오디오 채널에서 인터리브 스트림을 생성하려고 합니다. 오디오 스트림 인터리버 구성 요소가 이를 처리합니다. 워크플로에 추가하고 미디어 파일 입력에서 오디오 출력을 여기에 연결합니다.

![연결된 오디오 스트림 인터리버](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*연결된 오디오 스트림 인터리버*

인터리브 오디오 스트림이 있으므로 왼쪽 또는 오른쪽 스피커 위치를 할당할 위치를 지정하지 않습니다. 이 위치를 지정하기 위해 스피커 위치 할당자를 활용할 수 있습니다.

![스피커 위치 할당자 추가](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*스피커 위치 할당자 추가*

"사용자 지정"이라는 인코더 사전 설정 필터 및 "2.0 (L,R)"이라는 채널 사전 설정을 통해 스테레오 입력 스트림을 사용하도록 스피커 위치 할당자를 구성합니다. (채널1에 왼쪽 스피커 위치를 할당하고 채널2에 오른쪽 스피커 위치를 할당합니다.)

AAC 인코더의 입력에 스피커 위치 할당자의 출력을 연결합니다. 그런 다음 AAC 인코더가 "2.0 (L,R)" 채널 사전 설정으로 작업하므로 스테레오 오디오를 입력으로 처리하는 방법을 알게 됩니다.

### <a id="MXF_to_MP4_audio_and_fideo"></a>MP4 컨테이너에 오디오 및 비디오 스트림 멀티플렉싱
AVC 인코딩된 비디오 스트림 및 AAC 인코딩된 오디오 스트림을 지정하여 둘 모두를 .MP4 컨테이너에 캡처할 수 있습니다. 다양한 스트림을 하나로 혼합하는 프로세스는 "멀티플렉싱"(또는 "muxing")이라고 합니다. 이 경우에 일관된 단일 .MP4 패키지에서 오디오 및 비디오 스트림을 인터리브합니다. .MP4 컨테이너에 이 기능을 조정하는 구성 요소는 ISO MPEG-4 멀티플렉서라고 합니다. 이를 디자이너 화면에 추가하고 AVC 비디오 인코더 및 AAC 인코더를 입력에 연결합니다.

![연결된 MPEG4 멀티플렉서](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*연결된 MPEG4 멀티플렉서*

### <a id="MXF_to_MP4_writing_mp4"></a>MP4 파일 작성
출력 파일을 작성할 때 파일 출력 구성 요소를 사용합니다. ISO MPEG-4 멀티플렉서의 출력에 연결할 수 있으므로 해당 출력이 디스크에 작성됩니다. 이렇게 하려면 컨테이너(MPEG-4) 출력 핀을 파일 출력의 쓰기 입력 핀에 연결합니다.

![연결된 파일 출력](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*연결된 파일 출력*

사용되는 파일 이름은 파일 속성에 의해 결정됩니다. 해당 속성은 지정된 값으로 하드 코딩될 수 있지만 대부분 식을 통해 설정하려 할 가능성이 가장 높습니다.

워크플로가 식에서 출력 파일 이름 속성을 자동으로 결정하려면 파일 이름 옆에 있는 단추를 클릭합니다(폴더 아이콘 옆에 있음). 그런 다음, 드롭다운 메뉴에서 "식"을 선택합니다. 이렇게 하면 식 편집기가 표시됩니다. 편집기의 내용을 먼저 지웁니다.

![빈 식 편집기](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*빈 식 편집기*

식 편집기는 리터럴 값을 입력할 수 있게 하고 이는 하나 이상의 변수와 결합됩니다. 변수는 달러 기호로 시작합니다. $ 키를 누르면 사용 가능한 변수를 선택할 수 있는 드롭다운 상자가 편집기에 표시됩니다. 이 경우에 출력 디렉터리 변수 및 기본 입력 파일 이름 변수의 조합을 사용합니다.

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![채워진 식 편집기](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*채워진 식 편집기*

> [!NOTE]
> Azure에서 인코딩 작업의 출력 파일을 보려면 식 편집기에서 값을 제공해야 합니다.
>
>

확인을 눌러 식을 확인하는 경우 속성 창으로 이 시점에서 제 시간에 파일 속성이 확인하는 값을 미리 볼 수 있습니다.

![파일 식이 출력 dir 확인](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*파일 식이 출력 dir 확인*

### <a id="MXF_to_MP4_asset_from_output"></a>출력 파일에서 Media Services 자산 만들기
MP4 출력 파일을 작성했지만 이 워크플로를 실행한 결과로 미디어 서비스가 생성하는 출력 자산에 이 파일이 속해 있음을 나타낼 필요가 있습니다. 이 마지막에 워크플로 캔버스의 출력 파일/자산 노드를 사용합니다. 이 노드에 들어오는 파일은 모두 결과적으로 Azure Media Services 자산의 일부가 됩니다.

출력 파일/자산 구성 요소에 파일 출력 구성 요소를 연결하여 워크플로를 종료합니다.

![완료된 워크플로](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*완료된 워크플로*

### <a id="MXF_to_MP4_test"></a>완료된 워크플로 로컬로 테스트
로컬에서 워크플로를 테스트하려면 맨 위에 있는 도구 모음에서 재생 단추를 누릅니다. 워크플로가 실행을 마치면 구성된 출력 폴더에 생성된 출력을 검사합니다. MXF 입력 원본 파일에서 인코딩된 완료된 MP4 출력 파일이 표시됩니다.

## <a id="MXF_to_MP4_with_dyn_packaging"></a>MP4로 MXF Encoding - 다중 비트 전송률 동적 패키징 사용
이 연습에서는 단일 .MXF 입력 파일에서 AAC 인코딩 오디오를 사용하여 여러 비트 전송률 MP4 파일 집합을 만듭니다.

다중 비트 전송률 자산을 Azure Media Services에서 제공한 동적 패키징 기능과 함께 사용하려면 비트 전송률 및 해상도 각각에 여러 GOP로 정렬된 MP4 파일이 생성되어야 합니다. 이렇게 하려면 [단일 비트 전송률 MP4로 MXF Encoding](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) 연습으로 시작하는 것이 좋습니다.

![워크플로 시작](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*워크플로 시작*

### <a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>하나 이상의 추가 MP4 출력 추가
생성된 Azure Media Services 자산의 모든 MP4 파일은 서로 다른 비트 전송률 및 해상도를 지원합니다. 워크플로에 하나 이상의 MP4 출력 파일을 추가하겠습니다.

동일한 설정을 사용하여 만든 모든 비디오 인코더가 있는지 확인하는 작업은 기존 AVC 비디오 인코더를 복제하고 해상도 및 비트 전송률의 다른 조합을 구성하는 데 가장 편리합니다.(2,5Mbps로 초 당 25프레임에서 960x540를 추가해 보겠습니다.) 기존 인코더를 복제하려면 디자이너 화면에 복사하고 붙여 넣습니다.

새 AVC 구성 요소에 미디어 파일 입력의 압축되지 않은 비디오 출력 핀을 연결합니다.

![연결된 두 번째 AVC 인코더](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*연결된 두 번째 AVC 인코더*

이제 2,5Mbps로 960x540를 출력하는 새 AVC 인코더에 대한 구성을 적용합니다. (여기에 해당 속성 "출력 너비", "출력 높이" 및 "비트 전송률(kbps)"을 사용합니다.)

Azure Media Services의 동적 패키징과 함께 결과 자산을 사용하려 한다면 스트리밍 엔드포인트는 서로 정확하게 정렬된 MP4 파일 HLS/조각화된 MP4/DASH 조각에서 생성될 수 있어야 합니다. 생성되는 방법도 다른 비트 전송률 간에 전환하는 클라이언트가 부드러운 단일 연속 비디오 및 오디오 환경을 가져오는 방식이어야 합니다. 이렇게 하려면 두 AVC 인코더 GOP("사진의 그룹")의 속성에서 MP4 파일의 크기가 2초로 설정되어 있는지 확인해야 합니다. 이는 다음으로 수행할 수 있습니다.

* 고정 GOP 크기 모드를 GOP 크기로 설정하고
* 키 프레임 간격을 2초로 설정합니다.
* 또한 GOP IDR 컨트롤을 닫힌 GOP로 설정하여 모든 GOP가 종속성 없이 독립적으로 작동하도록 합니다.

워크플로를 이해하기 쉽도록 첫 번째 AVC 인코더의 이름을 "AVC 비디오 인코더 640x360 1200kbps"로 바꾸고 두 번째 AVC 인코더의 이름을 "AVC 비디오 인코더 960x540 2500kbps"로 바꿉니다.

이제 두 번째 ISO MPEG-4 멀티플렉서 및 두 번째 파일 출력을 추가합니다. 새 AVC 인코더에 멀티플렉서를 연결하고 해당 출력이 파일 출력에 전달되도록 합니다. 그런 다음 AAC 오디오 인코더 출력을 새 멀티플렉서의 입력에 연결합니다. 그러면 파일 출력은 출력 파일/자산 노드에 연결되어 생성되는 Media Services 자산에 추가될 수 있습니다.

![두 번째 Muxer 및 연결된 파일 출력](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*두 번째 Muxer 및 연결된 파일 출력*

Azure Media Services 동적 패키징과 호환성을 위해 멀티플렉서의 청크 모드를 GOP 개수나 기간으로 구성하고 청크 당 GOP를 1로 설정합니다. (기본값이어야 합니다.)

![Muxer 청크 모드](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Muxer 청크 모드*

참고: 사용자가 자산 출력에 추가하려는 다른 비트 전송률 및 해상도 조합에 대해 이 프로세스를 반복하고자 할 수 있습니다.

### <a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>파일 출력 이름 구성
출력 자산에 추가된 둘 이상의 단일 파일이 있습니다. 각 출력 파일에 대한 파일 이름이 서로 다르게 만들 필요를 제공하고 어쩌면 파일 명명 규칙을 제공하므로 파일 이름에서 다룰 내용이 명확해집니다.

파일 출력 이름 지정은 디자이너에서 식을 통해 제어될 수 있습니다. 파일 출력 구성 요소 중 하나에 대한 속성 창을 열고 파일 속성에 대한 식 편집기를 엽니다. 다음 식을 통해 첫 번째 출력 파일을 구성했습니다( [MXF에서 단일 비트 전송률 MP4 출력으로](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)이동하기 위한 자습서를 참조).

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

즉, 작성할 출력 디렉터리 및 원본 파일 기본 이름 등 두 개의 변수로 파일 이름을 결정합니다. 전자는 워크플로 루트에서 속성으로 노출되고 후자는 들어오는 파일에 의해 결정됩니다. 출력 디렉터리는 로컬 테스트에 사용합니다. 워크플로가 Azure Media Services에서 클라우드 기반 미디어 프로세서에 의해 실행되는 경우 워크플로 엔진이 이 속성을 재정의합니다.
출력 파일 모두에 일관된 출력 명명을 부여하려면 첫 번째 파일 명명 식을 다음과 같이 변경합니다.

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

그리고 두 번째를 다음과 같이 변경합니다.

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

중간 테스트 실행을 실행하여 MP4 출력 파일이 모두 제대로 생성되도록 합니다.

### <a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>별도 오디오 트랙 추가
나중에 살펴보겠지만 .ism 파일을 생성하여 MP4 출력 파일을 사용하는 경우 적응 스트리밍에 대한 오디오 트랙으로 오디오 전용 MP4 파일이 필요합니다. 이 파일을 만들려면 워크플로(ISO-MPEG-4 멀티플렉서)에 추가 muxer를 추가하고 트랙1에 해당 입력 핀으로 AAC 인코더의 출력 핀을 연결합니다.

![추가된 오디오 Muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*추가된 오디오 Muxer*

세 번째 출력 파일 구성 요소를 만들어서 muxer에서 아웃바운드 스트림을 출력하고 파일 명명 식을 다음과 같이 구성합니다.

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![파일 출력을 생성하는 오디오 Muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*파일 출력을 생성하는 오디오 Muxer*

### <a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>.ISM SMIL 파일 추가
또한 Media Services 자산에서 MP4 파일 모두(및 오디오 전용 MP4)와 함께 작업하는 동적 패키징의 경우 매니페스트 파일(“SMIL”(Synchronized Multimedia Integration Language) 파일이라고도 함)이 필요합니다. 이 파일은 동적 패키징에 사용 가능하고 오디오 스트리밍에 고려하는 MP4 파일을 Azure Media Services에 나타냅니다. 단일 오디오 스트림이 있는 MP4의 집합에 대한 일반적인 매니페스트 파일은 다음과 같습니다.

```xml
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="https://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>
```

.ism 파일은 전환 문 내에서 개별 MP4 비디오 파일 각각에 대한 참조 및 추가로 오디오를 포함하는 MP4에 하나(또는 이상)의 오디오 파일 참조를 포함합니다.

MP4의 집합에 매니페스트 파일을 생성하는 작업은 "AMS 매니페스트 기록기"라는 구성 요소를 통해 수행될 수 있습니다. 이를 사용하려면 화면으로 끌어서 세 개의 파일 출력 구성 요소에서 AMS 매니페스트 기록기 입력에 "쓰기 완료" 출력 핀을 연결합니다. 그런 다음 출력 파일/자산에 AMS 매니페스트 기록기의 출력을 연결해야 합니다.

다른 파일 출력 구성 요소와 마찬가지로 .ism 파일 출력 이름을 식으로 구성합니다.

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

완료된 워크플로는 아래와 같습니다.

![다중 비트 전송률 MP4 워크플로에 완료된 MXF](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*다중 비트 전송률 MP4 워크플로에 완료된 MXF*

## <a id="MXF_to__multibitrate_MP4"></a>다중 비트 전송률 MP4로 MXF Encoding - 향상된 청사진
[이전 워크플로 연습](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)에서 단일 MXF 입력 자산이 어떻게 Azure Media Services 동적 패키징과 함께 사용할 다중 비트 전송률 MP4 파일, 오디오 전용 MP4 파일, 매니페스트 파일을 가진 출력 자산으로 변환될 수 있는지를 살펴보았습니다.

이 연습에서는 일부의 측면이 강화되고 더 편리하게 만들어지는 방법을 보여줍니다.

### <a id="MXF_to_multibitrate_MP4_overview"></a>강화할 워크플로 개요
![강화할 다중 비트 전송률 MP4 워크플로](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*강화할 다중 비트 전송률 MP4 워크플로*

### <a id="MXF_to__multibitrate_MP4_file_naming"></a>파일 명명 규칙
이전 워크플로에서 출력 파일 이름을 생성하기 위한 기반으로 단일 식을 지정했습니다. 그러나 이러한 식을 지정한 각 개별 출력 파일 구성 요소의 경우 모두 일부 중복이 있습니다.

예를 들어 첫 번째 비디오 파일에 대한 파일 출력 구성 요소는 다음 식으로 구성됩니다.

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

반면 두 번째 출력 비디오의 경우 다음과 같은 식을 사용합니다.

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

대신 이러한 중복을 제거하고 더 구성 가능하게 된다면 간결하며 오류가 발생할 가능성이 적고 더 편리해질 것입니다. 다행히 다음과 같은 작업이 가능합니다. 워크플로 루트에서 사용자 지정 속성을 만드는 기능과 함께 디자이너의 식 기능이 추가된 편리한 계층을 제공합니다.

개별 MP4 파일의 비트 전송률에서 파일 이름 구성을 가져온다고 가정해 보겠습니다. (그래프의 루트의) 하나의 중앙 위치에서 구성하려는 이러한 비트 전송률은 해당 위치에서 파일 이름 생성을 구성하고 가져오도록 액세스됩니다. 이렇게 하려면 AVC 인코더 모두에서 워크플로 루트로 비트 전송률 속성을 게시하기 시작하므로 AVC 인코더와 마찬가지로 모두 루트에서도 액세스할 수 있습니다. (두 개의 서로 다른 위치에 표시되는 경우에도 기본 값은 하나입니다.)

### <a id="MXF_to__multibitrate_MP4_publishing"></a>워크플로 루트에 구성 요소 속성 게시
첫 번째 AVC 인코더를 열고 비트 전송률(kbps) 속성으로 이동하여 드롭다운에서 게시를 선택합니다.

![비트 전송률 속성 게시](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*비트 전송률 속성 게시*

게시 대화 상자를 구성하여 "video1bitrate"라는 게시된 이름 및 "비디오 1 비트 전송률"이라는 읽을 수 있는 표시 이름을 사용하여 워크플로 그래프의 루트에 게시합니다. "비트 전송률 스트리밍"이라는 사용자 지정 그룹 이름을 구성하고 게시를 누릅니다.

![비트 전송률 속성 게시](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*비트 전송률 속성에 게시 대화 상자*

두 번째 AVC 인코더의 비트 전송률 속성에서 동일하게 반복하고 동일한 "비트 전송률 스트리밍" 사용자 지정 그룹에서 "비디오 2 비트 전송률"이라는 표시 이름을 사용하여 "video2bitrate"이라고 명명합니다.

이제 워크플로 루트 속성을 검사하면 두 개의 게시된 속성이 있는 사용자 지정 그룹이 표시됩니다. 둘 모두 해당 AVC 인코더 비트 전송률의 값을 반영합니다.

![워크플로 루트에 게시된 비트 전송률 속성](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

코드 또는 식에서 이러한 속성에 액세스하려는 때마다 다음과 같이 수행할 수 있습니다.

* 루트 바로 아래 구성 요소의 인라인 코드에서: node.getPropertyAsString('../video1bitrate', null)
* 식 내에서: ${ROOT_video1bitrate}

여기에서도 오디오 트랙 비트 전송률을 게시하여 "비트 전송률 스트리밍" 그룹을 완료하겠습니다. AAC 인코더의 속성 내에서 비트 전송률 설정을 검색하고 옆에 있는 드롭다운에서 게시를 선택합니다. "비트 전송률 스트리밍" 사용자 지정 그룹 내에서 "audio1bitrate"이라는 이름과 "오디오 1 비트 전송률"이라는 표시 이름을 사용하여 그래프의 루트에 게시합니다.

![오디오 비트 전송률에 게시 대화 상자](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*오디오 비트 전송률에 게시 대화 상자*

![루트의 결과 비디오 및 오디오 속성](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*루트의 결과 비디오 및 오디오 속성*

또한 세 가지 해당 값의 변경이 연결된 (그리고 게시된) 해당 구성 요소의 값을 다시 구성하고 변경합니다.

### <a id="MXF_to__multibitrate_MP4_output_files"></a>게시된 속성 값을 사용하는 출력 파일 이름 생성
생성된 파일 이름을 하드 코딩하는 대신 각 그래프 루트에 게시한 비트 전송률 속성을 사용하는 각 출력 파일 구성 요소의 파일 이름 식을 변경할 수 있습니다. 첫 번째 파일 출력을 시작하고 파일 속성을 찾아서 다음과 같이 식을 편집합니다.

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

식 창에서 키보드에 달러 기호를 눌러서 이 식의 다른 매개 변수를 액세스하고 입력할 수 있습니다. 사용 가능한 매개 변수 중 하나는 이전에 게시한 video1bitrate 속성입니다.

![식 내에서 매개 변수 액세스](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*식 내에서 매개 변수 액세스*

두 번째 비디오에 대한 파일 출력에 동일한 작업을 수행합니다.

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

그리고 오디오 전용 파일 출력에도 수행합니다.

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

이제 비디오 또는 오디오 파일에 대한 비트 전송률을 변경하면 해당하는 인코더를 다시 구성하고 비트 전송률 기반 파일 이름 규칙을 모두 자동으로 적용합니다.

## <a id="thumbnails_to__multibitrate_MP4"></a>다중 비트 전송률 MP4 출력에 미리 보기 추가
[MXF 입력에서 다중 비트 전송률 MP4 출력](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)을 생성하는 워크플로를 시작하고 이제 출력에 미리 보기의 추가를 살펴봅니다.

### <a id="thumbnails_to__multibitrate_MP4_overview"></a>미리 보기를 추가하는 워크플로 개요
![시작할 다중 비트 전송률 MP4 워크플로](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*시작할 다중 비트 전송률 MP4 워크플로*

### <a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>JPG Encoding 추가
미리 보기 생성의 핵심은 JPG 파일을 출력할 수 있는 JPG 인코더 구성 요소입니다.

![JPG 인코더](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*JPG 인코더*

그러나 직접 미디어 파일 입력에서 JPG 인코더에 압축되지 않은 비디오 스트림을 연결할 수 없습니다. 대신 전달된 개별 프레임을 사용합니다. 이는 비디오 프레임 게이트 구성 요소를 통해 수행할 수 있습니다.

![JPG 인코더에 프레임 게이트 연결](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*JPG 인코더에 프레임 게이트 연결*

프레임 게이트는 여러 초 또는 여러 프레임에 한 번 비디오 프레임을 전달할 수 있습니다. 발생하는 간격 및 시간 오프셋은 속성에서 구성할 수 있습니다.

![비디오 프레임 게이트 속성](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*비디오 프레임 게이트 속성*

모드를 시간(초)으로 설정하고 간격을 60으로 설정하여 매 분 마다 미리 보기를 만들어보겠습니다.

### <a id="thumbnails_to__multibitrate_MP4_color_space"></a>색 공간 변환 처리
프레임 게이트 및 미디어 파일 입력의 압축되지 않은 비디오 핀은 모두 연결될 수 있다는 것이 논리적 보이는 반면 그렇게 할 경우 경고를 받게 됩니다.

![입력 색 공간 오류](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*입력 색 공간 오류*

색 정보가 원래 있는 그대로 압축되지 않은 비디오 스트림에서 보여지는 방식 때문에 MXF에서 가져오면 JPG 인코더가 예상하는 것과 다릅니다. 구체적으로 말하면 소위 "RGB" 또는 "회색"의 "색 공간"이 흘러들어 올 것이 예상됩니다. 즉, 비디오 프레임 게이트의 인바운드 비디오 스트림이 해당 색 공간을 고려하여 적용할 변환이 있어야 합니다.

워크플로인 색상 공간 변환기 - Intel에 끌어서 놓고 프레임 게이트에 연결합니다.

![색 공간 변환 연결](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*색 공간 변환 연결*

속성 창의 사전 설정 목록에서 BGR 24 항목을 선택합니다.

### <a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>미리 보기 작성
MP4 비디오와 다르게 JPG 인코더 구성 요소는 여러 개의 파일을 출력합니다. 이를 처리하기 위해 장면 검색 JPG 파일 기록기 구성 요소를 사용할 수 있습니다. 들어오는 JPG 미리 보기를 사용하고 이를 작성하며 각 파일 이름이 다른 숫자 뒤에 붙습니다. (일반적으로 미리 보기를 그린 스트림에서 초/단위의 수를 나타내는 숫자입니다.)

![장면 검색 JPG 파일 기록기 소개](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*장면 검색 JPG 파일 기록기 소개*

다음 식을 사용하여 출력 폴더 경로 속성을 구성합니다. ${ROOT_outputWriteDirectory}

그리고 다음으로 파일 이름 접두사 속성을 구성합니다.

    ${ROOT_sourceFileBaseName}_thumb_

접두사는 미리 보기 파일이 명명되는 방법을 결정합니다. 스트림에서 미리 보기의 위치를 나타내는 숫자가 뒤에 붙습니다.

![장면 검색 JPG 파일 기록기 속성](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*장면 검색 JPG 파일 기록기 속성*

출력 파일/자산 노드에 장면 검색 JPG 파일 기록기를 연결합니다.

### <a id="thumbnails_to__multibitrate_MP4_errors"></a>워크플로에서 오류 감지
색상 공간 변환기의 입력을 그대로 압축되지 않은 비디오 출력에 연결합니다. 이제 워크플로에 대한 로컬 테스트 실행을 수행합니다. 워크플로가 갑자기 실행을 중지하고 오류가 발생한 구성 요소에 빨간 윤곽선이 나타날 가능성이 있습니다.

![색 공간 변환기 오류](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*색 공간 변환기 오류*

색 공간 변환기 구성 요소의 오른쪽 상단에 있는 작은 빨간색 "E" 아이콘을 클릭하여 인코딩 시도가 실패한 이유를 확인합니다.

![색 공간 변환기 오류 대화 상자](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*색 공간 변환기 오류 대화 상자*

결국 확인할 수 있듯이 색 공간 변환기에 들어오는 색 공간 표준이 요청한 YUV에서 RGB로의 변환에 대해 rec601로 설정되어야 합니다. 스트림은 rec601을 나타내지 않습니다. (Rec 601은 디지털 비디오 형태로 인터레이스된 아날로그 비디오 신호를 인코딩하기 위한 표준입니다. 720 광도 샘플 및 줄 당 360 색차 샘플을 포함하는 활성 영역을 지정합니다. 색 인코딩 시스템은 YCbCr 4:2:2라고 알려져 있습니다.)

이를 해결하기 위해 rec601 콘텐츠를 다루는 스트림의 메타데이터를 나타냅니다. 이렇게 하려면 비디오 데이터 형식 업데이트를 구성 요소를 사용하며 이를 원시 원본 및 색 공간 변환 구성 요소 사이에 입력합니다. 이 데이터 형식 업데이트 프로그램을 사용하면 특정 비디오 데이터 형식 속성을 수동으로 업데이트할 수 있습니다. 이를 구성하여 색 공간 표준인 "Rec 601"을 나타냅니다. 이렇게 하면 색 공백이 없는 경우 비디오 데이터 형식 업데이트 프로그램이 "Rec 601" 색 공간을 사용하여 스트림의 태그를 지정합니다. (재정의 확인란을 선택하지 않으면 기존 메타데이터를 재정의하지 않습니다.)

![데이터 형식 업데이트 프로그램에서 색 공간 표준 업데이트](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*데이터 형식 업데이트 프로그램에서 색 공간 표준 업데이트*

### <a id="thumbnails_to__multibitrate_MP4_finish"></a>완료된 워크플로
이제 워크플로가 완료되면 다른 테스트를 실행하여 통과하는지 확인합니다.

![미리 보기로 다중 mp4 출력에 대해 완료된 워크플로](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*미리 보기로 다중 mp4 출력에 대해 완료된 워크플로*

## <a id="time_based_trim"></a>다중 비트 전송률 MP4 출력의 시간 기반 트리밍
[MXF 입력에서 다중 비트 전송률 MP4 출력](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)을 생성하는 워크플로를 시작하고 이제 타임 스탬프를 기반으로 하는 원본 비디오의 트리밍을 살펴봅니다.

### <a id="time_based_trim_start"></a>트리밍을 추가하기 시작하려는 워크플로 개요
![트리밍에 추가할 워크플로 시작](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*트리밍에 추가할 워크플로 시작*

### <a id="time_based_trim_use_stream_trimmer"></a>스트림 트리머 사용
스트림 트리머 구성 요소를 사용하면 시간 정보(초, 분,...)에 기반하는 입력 스트림의 시작 및 종료를 자를 수 있습니다. 트리머는 프레임 기반 자르기를 지원하지 않습니다.

![스트림 트리머](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*스트림 트리머*

AVC 인코더 및 스피커 위치 할당자를 미디어 파일 입력에 직접 연결하는 대신 해당 스트림 트리머 간에 입력합니다. (비디오 신호 및 인터리브 오디오 신호에 각각 하나.)

![사이 간 스트림 트리머 배치](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*사이 간 스트림 트리머 배치*

비디오에서 15초에서 60초 사이에 있는 비디오 및 오디오만 처리하도록 트리머를 구성하겠습니다.

비디오 스트림 트리머의 속성으로 이동하고 시작 시간(15초) 및 종료 시간(60초) 속성을 모두 구성합니다. 오디오 및 비디오 트리머를 모두 항상 동일한 시작 및 종료 값으로 구성하려면 해당 사항을 워크플로의 루트에 게시합니다.

![스트림 트리머에서 시작 시간 속성 게시](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*스트림 트리머에서 시작 시간 속성 게시*

![시작 시간에 속성 대화 상자 게시](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*시작 시간에 속성 대화 상자 게시*

![종료 시간에 속성 대화 상자 게시](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*종료 시간에 속성 대화 상자 게시*

이제 워크플로의 루트를 검사하면 속성이 모두 여기에 깔끔하게 표시되고 구성이 가능합니다.

![루트에 사용 가능한 게시된 속성](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*루트에 사용 가능한 게시된 속성*

이제 오디오 트리머에서 트리밍 속성을 열고 워크플로의 루트에 게시된 속성을 참조하는 식으로 시작 및 종료 시간을 모두 구성합니다.

오디오 트리밍 시작 시간을 다음과 같이 구성합니다.

    ${ROOT_TrimmingStartTime}

그리고 종료 시간을 다음과 같습니다.

    ${ROOT_TrimmingEndTime}

### <a id="time_based_trim_finish"></a>완료된 워크플로
![완료된 워크플로](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*완료된 워크플로*

## <a id="scripting"></a>스크립팅한 구성 요소 소개
스크립팅한 구성 요소는 워크플로의 실행 단계 중 임의의 스크립트를 실행할 수 있습니다. 실행될 수 있는 다른 4개의 스크립트가 있으며 각각 워크플로 수명 주기에서 특정한 특성 및 고유한 위치를 가집니다.

* **commandScript**
* **realizeScript**
* **processInputScript**
* **lifeCycleScript**

스크립팅된 구성 요소의 설명서는 위에서 각각에 대해 자세히 설명합니다. [다음 섹션](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)에서 **realizeScript** 스크립트 구성 요소는 워크플로가 시작할 때 즉석에서 cliplist xml을 생성하는 데 사용됩니다. 이 스크립트는 구성 요소를 설치하는 동안 호출되며 수명 주기에서 한 번만 발생합니다.

### <a id="scripting_hello_world"></a>워크플로 내의 스크립팅: Hello World
스크립트 구성 요소를 디자이너 화면으로 끌어오고 이름을 바꿉니다(예: "SetClipListXML").

![스크립팅된 구성 요소 추가](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*스크립팅된 구성 요소 추가*

스크립팅된 구성 요소의 속성을 검사할 때 네 개의 스크립트 형식이 표시되고 각각 다른 스크립트로 구성 가능합니다.

![스크립팅된 구성 요소 속성](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*스크립팅된 구성 요소 속성*

processInputScript의 선택을 취소하고 realizeScript에 대한 편집기를 엽니다. 이제 스크립트를 시작할 준비가 되었습니다.

스크립트는 Java와의 호환성을 유지하는 Java 플랫폼에 동적으로 컴파일된 스크립트 언어인 Groovy로 작성됩니다. 사실 대부분의 Java 코드는 유효한 Groovy 코드입니다.

realizeScript의 컨텍스트에서 간단한 Hello World Groovy 스크립트를 작성해 보겠습니다. 편집기에 다음을 입력합니다.

    node.log("hello world");

이제 로컬 테스트 실행을 실행합니다. 실행한 후에 로그 속성을 검사합니다(스크립팅된 구성 요소의 시스템 탭을 통해).

![Hello World 로그 출력](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Hello World 로그 출력*

로그 메서드를 호출하는 노드 개체는 현재 "노드" 또는 스크립트하는 구성 요소를 나타냅니다. 이와 같은 모든 구성 요소는 출력 로깅 데이터에 대한 기능을 가지며 시스템 탭을 통해 사용할 수 있습니다. 이 경우에 문자열 리터럴 "Hello World"를 출력합니다. 중요한 것은 스크립트가 실제로 수행하는 작업에 대한 통찰력을 제공하는 중요한 디버깅 도구라는 것을 증명할 수 있어야 합니다.

또한 스크립트 환경 내에서 다른 구성 요소의 속성에 액세스합니다. 다음을 실행해보세요.

```java
    //inspect current node:
    def nodepath = node.getNodePath();
    node.log("this node path: " + nodepath);

    //walking up to other nodes:
    def parentnode = node.getParentNode();
    def parentnodepath = parentnode.getNodePath();
    node.log("parent node path: " + parentnodepath);

    //read properties from a node:
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null );
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null);
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);
```

로그 창은 다음을 표시합니다.

![노드 경로에 액세스하기 위한 로그 출력](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*노드 경로에 액세스하기 위한 로그 출력*

## <a id="frame_based_trim"></a>다중 비트 전송률 MP4 출력의 프레임 기반 트리밍
[MXF 입력에서 다중 비트 전송률 MP4 출력](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)을 생성하는 워크플로를 시작하고 이제 프레임 개수를 기반으로 하는 원본 비디오의 트리밍을 살펴봅니다.

### <a id="frame_based_trim_start"></a>트리밍을 추가하기 시작하려는 청사진 개요
![트리밍을 추가하기 시작하려는 워크플로](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*트리밍을 추가하기 시작하려는 워크플로*

### <a id="frame_based_trim_clip_list"></a>클립 목록 XML 사용
이전의 모든 워크플로 자습서에서 미디어 파일 입력 구성 요소를 비디오 입력 원본으로 사용했습니다. 하지만 이 특정 시나리오에서 클립 목록 원본 구성 요소를 대신 사용합니다. 선호하는 작업 방법이 아니어야 합니다. 그렇게 해야 하는 실제 이유가 있을 때에만 클립 목록 원본을 사용합니다(아래와 같이 클립 목록 지우기 기능을 사용할 경우).

미디어 파일 입력에서 클립 목록 원본으로 전환하려면 클립 목록 원본 구성 요소를 디자인 화면으로 끌어서 클립 목록 XML 핀을 워크플로 디자이너의 클립 목록 XML 노드에 연결합니다. 입력 비디오에 따라 출력 핀으로 클립 목록 원본을 채웁니다. 이제 압축되지 않은 비디오 및 압축되지 않은 오디오 핀을 클립 목록 원본에서 해당 AVC 인코더 및 오디오 스트림 인터리버에 연결합니다. 이제 미디어 파일 입력을 제거합니다.

![클립 목록 원본으로 미디어 파일 입력 바꾸기](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*클립 목록 원본으로 미디어 파일 입력 바꾸기*

클립 목록 원본 구성 요소는 "클립 목록 XML"을 입력으로 사용합니다. 로컬로 테스트할 원본 파일을 선택한 경우 이 클립 목록 xml은 자동으로 채워집니다.

![자동으로 채워진 클립 목록 XML 속성](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*자동으로 채워진 클립 목록 XML 속성*

xml을 더 자세히 알아보려면 다음과 같습니다.

![클립 목록 대화 상자 편집](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*클립 목록 대화 상자 편집*

그러나 클립 목록 xml의 기능을 반영하지 않습니다. 한 가지 옵션은 비디오 및 오디오 원본에 다음과 같이 "트리밍" 요소를 추가하는 것입니다.

![클립 목록에 자르기 요소 추가](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*클립 목록에 자르기 요소 추가*

위와 같이 클립 목록 xml을 수정하고 로컬 테스트 실행을 수행하는 경우 비디오에서 10-20초 사이가 잘린 비디오를 확인하게 됩니다.

그러나 로컬로 실행할 때 발생한 것과 달리 동일한 cliplist xml은 Azure Media Services에서 실행되는 워크플로에 적용될 때 동일한 효과를 발생시킵니다. Azure Premium 인코더가 시작되면 cliplist xml은 인코딩 작업에서 지정한 입력 파일에 따라 매번 다시 생성됩니다. 즉, xml에서 수행하는 변경 내용이 아쉽게도 재정의됩니다.

인코딩 작업이 시작될 때 cliplist xml의 초기화를 방지하려면 워크플로가 시작된 후에 바로 즉석에서 다시 생성할 수 있습니다. "스크립팅된 구성 요소"라는 것을 통해 이러한 사용자 지정 작업을 수행할 수 있습니다. 자세한 내용은 [스크립팅된 구성 요소 소개](media-services-media-encoder-premium-workflow-tutorials.md#scripting)를 참조하세요.

스크립트 구성 요소를 디자이너 화면으로 끌어오고 이름을 "SetClipListXML"로 바꿉니다.

![스크립팅된 구성 요소 추가](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*스크립팅된 구성 요소 추가*

스크립팅된 구성 요소의 속성을 검사할 때 네 개의 스크립트 형식이 표시되고 각각 다른 스크립트로 구성 가능합니다.

![스크립팅된 구성 요소 속성](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*스크립팅된 구성 요소 속성*

### <a id="frame_based_trim_modify_clip_list"></a>스크립팅한 구성 요소에서 클립 목록 수정
워크플로가 시작하는 동안 생성되는 cliplist xml을 다시 작성할 수 있기 전에 cliplist xml 속성 및 내용에 액세스할 수 있도록 해야 합니다. 다음과 같은 작업을 수행할 수 있습니다.

```java
    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);
```

![기록된 들어오는 클립 목록](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*기록된 들어오는 클립 목록*

먼저 어떤 지점에서 어떤 지점까지 비디오를 트리밍하려는지 결정하는 방법이 필요합니다. 워크플로에 덜 숙련된 사용자에게 편리하도록 두 개의 속성을 그래프의 루트에 게시합니다. 이렇게 하려면 디자이너 화면을 마우스 오른쪽 단추로 클릭하고 "속성 추가"를 선택합니다.

* 첫 번째 속성: 다음 형식의 “ClippingTimeStart”: “TIMECODE”
* 두 번째 속성: 다음 형식의 “ClippingTimeEnd”: “TIMECODE”

![클리핑 시작 시간에 속성 대화 상자 추가](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*클리핑 시작 시간에 속성 대화 상자 추가*

![워크플로 루트에 게시된 클리핑 시간 속성](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*워크플로 루트에 게시된 클리핑 시간 속성*

두 속성을 모두 적절한 값으로 구성합니다.

![클리핑 시작 및 종료 속성 구성](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*클리핑 시작 및 종료 속성 구성*

이제 스크립트 내에서 다음과 같은 두 속성에 액세스할 수 있습니다.

```java
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);
```

![클리핑의 시작 및 종료를 보여주는 로그 창](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*클리핑의 시작 및 종료를 보여주는 로그 창*

간단한 정규식을 사용하여 양식을 보다 편리하게 사용하도록 시간 코드 문자열을 구문 분석해보겠습니다.

```java
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);
    node.log("framerate end is: " + endframerate);
```

![구문 분석된 시간 코드의 출력이 있는 로그 창](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*구문 분석된 시간 코드의 출력이 있는 로그 창*

이 정보가 있으므로 이제 cliplist xml를 수정하여 동영상의 원하는 정확한 프레임 클리핑에 대한 시작 및 종료 시간을 반영할 수 있습니다.

![자르기 요소를 추가하는 스크립트 코드](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*자르기 요소를 추가하는 스크립트 코드*

이 작업은 일반 문자열 조작 작업을 통해 수행되었습니다. 결과 수정된 클립 목록 xml은 워크플로 루트에서 "setProperty" 메서드를 통해 clipListXML 속성에 다시 기록됩니다. 다른 테스트를 실행한 후에 로그 창은 다음을 표시합니다.

![결과 클립 목록 로깅](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*결과 클립 목록 로깅*

비디오 및 오디오 스트림을 자르는 방법을 보려면 테스트 실행을 수행합니다. 하지만 트리밍 지점에 대한 값이 서로 다른 둘 이상의 테스트를 실행하면 고려하지 않은 점인 있음을 알 수 있습니다! 이유는 Azure 런타임과 달리 디자이너가 실행할 때 마다 cliplist xml을 재정의하지 않기 때문입니다. 이 즉, 처음으로 설정한 시작 및 종료 지점에만 하면 xml을 변환할 모든 다른 시간에 가드 절 (하는 경우 (`clipListXML.indexOf("<trim>") == -1`)) 워크플로에서 인스턴스가 이미 있는 경우 다른 자르기 요소를 추가 하지 못하게 됩니다.

워크플로를 편리하게 로컬로 테스트하려는 가장 좋은 방법은 자르기 요소가 이미 있는지 조사하는 정리 코드를 추가하는 것입니다. 그렇다면 새 값으로 xml을 수정하여 계속하기 전에 제거할 수 있습니다. 일반 문자열 조작을 사용하지 않고 구문 분석하는 실제 xml 개체 모델을 통해이 작업을 수행하는 것이 안전합니다.

하지만 이러한 코드를 추가할 수 있기 전에 먼저 스크립트의 시작 부분에 여러 개의 가져오기 문을 추가해야 합니다.

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;
```

그런 다음 필요한 정리 코드를 추가할 수 있습니다.

```java
    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes:
    elementsToDelete.each{
        e -> e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();
```

이 코드는 cliplist xml에 자르기 요소를 추가한 지점 바로 위에 사용됩니다.

이 시점에서 변경 사항을 적용하는 동안 원하는 만큼 워크플로를 실행하고 수정할 수 있습니다.    

### <a id="frame_based_trim_clippingenabled_prop"></a>ClippingEnabled 편의 속성 추가
항상 트리밍이 발생하기를 원하지 않으면 트리밍/클리핑을 사용할지 여부를 나타내는 편리한 부울 플래그를 추가하여 워크플로를 마무리하겠습니다.

이전과 마찬가지로 "부울" 형식의 "ClippingEnabled"라는 워크플로의 루트에 새 속성을 게시합니다.

![클리핑 설정에 게시된 속성 ](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*클리핑 설정에 게시된 속성*

간단한 가드 절 아래에서 트리밍해야 하는지를 확인하고 이와 같은 클립 목록을 수정할지를 결정합니다.

```java
    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }
```

### <a id="code"></a>코드 완료

```java
    import javax.xml.parsers.*;
    import org.xml.sax.*;
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*;
    import javax.xml.transform.*;
    import javax.xml.transform.stream.*;
    import javax.xml.transform.dom.*;

    // get cliplist xml:
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();

    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart);
    startregresult.matches();
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);

    //parse the end timing:
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches();
    def endtimecode = endregresult.group(1);
    node.log("timecode end is: " + endtimecode);
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);

    //for local testing: delete any pre-existing trim elements
    //from the clip list xml by parsing the xml into a DOM:

    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML));
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim";
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection
    Set<Element> elementsToDelete = new HashSet<Element>();
    for (int i = 0; i < trimelems.getLength(); i++) {
        Element e = (Element)trimelems.item(i);
        elementsToDelete.add(e);
    }

    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e ->
        e.getParentNode().removeChild(e);
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString());
    if(clippingrequired == null || clippingrequired == false)
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return;
    }

    //add trim elements to cliplist xml
    if ( clipListXML.indexOf("<trim>") == -1 )
    {
        //trim video
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode +
            " </outPoint>\n </trim> \n");
        //trim audio
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+
            startframerate +"\">" + starttimecode +
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" +
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML );
        node.setProperty("../clipListXml",clipListXML);
    }
```

## <a name="also-see"></a>참고 항목
[Azure Media Services의 프리미엄 Encoding 소개](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Azure Media Services의 프리미엄 Encoding 사용 방법](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Azure Media Services로 주문형 콘텐츠 인코딩](media-services-encode-asset.md#media-encoder-premium-workflow)

[미디어 인코더 Premium 워크플로 형식 및 코덱](media-services-premium-workflow-encoder-formats.md)

[샘플 워크플로 파일](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Azure Media Services 탐색기 도구](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
