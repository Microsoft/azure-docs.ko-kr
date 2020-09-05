---
title: Azure Media Services v3 입력 메타 데이터 스키마
description: 이 문서에서는 Azure Media Services v3 입력 메타 데이터 스키마에 대 한 개요를 제공 합니다.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: a9b8fec7e69b6ede15f99cae01e89c962996e280
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269151"
---
# <a name="input-metadata"></a>입력 메타데이터

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

인코딩 작업은 일부 인코딩 태스크를 수행할 입력 자산(또는 자산)과 연결됩니다.  태스크가 완료되는 즉시 출력 자산이 생성됩니다. 출력 자산에는 비디오, 오디오, 미리 보기, 매니페스트 및 기타 파일이 포함 되어 있습니다. 

출력 자산에는 입력된 자산에 대한 메타데이터가 있는 파일도 포함됩니다. 메타 데이터 JSON 파일의 이름에 임의의 ID가 있으며,이 파일을 사용 하 여 출력 자산이 속한 입력 자산을 식별 하지 않습니다. 자신이 속한 입력 자산을 식별 하려면 필드를 사용 `Uri` 합니다. 자세한 내용은 [다른 자식 요소](#other-child-elements)를 참조 하세요.  

Media Services는 입력 자산을 미리 검색 하 여 메타 데이터를 생성 하지 않습니다. 입력 메타 데이터는 입력 자산이 작업에서 처리 될 때 아티팩트로만 생성 됩니다. 따라서이 아티팩트는 출력 자산에 기록 됩니다. 입력 자산 및 출력 자산에 대 한 메타 데이터를 생성 하는 데 다른 도구가 사용 됩니다. 따라서 입력 메타데이터는 출력 메타데이터와 스키마가 약간 다를 수 있습니다.

이 문서에서는 입력 메타 데이터 ( &lt; asset_id &gt;_metadata.js)의 기반으로 사용 되는 JSON 스키마의 요소와 형식에 대해 설명 합니다. 출력 자산에 대 한 메타 데이터가 포함 된 파일에 대 한 자세한 내용은 [출력 메타 데이터](output-metadata-schema.md)를 참조 하세요.  

JSON 스키마 예제는이 문서의 끝 부분에서 찾을 수 있습니다.  

## <a name="assetfile"></a>AssetFile  

인코딩 작업에 대한 AssetFile 요소 컬렉션이 포함됩니다.  

> [!NOTE]
> 다음 4개의 자식 요소가 순서대로 나타나야 합니다.  
> 
> 

| Name  | 설명 |
| --- | --- | 
| **VideoTracks**|각각의 실제 자산 파일에는 적절한 컨테이너 형식으로 인터리빙된 0개 이상의 비디오 트랙이 포함될 수 있습니다. 자세한 내용은 [Videotracks](#videotracks)같은 항목을 참조 하세요. |
| **AudioTracks**|각각의 실제 자산 파일에는 적절한 컨테이너 형식으로 인터리빙된 0개 이상의 오디오 트랙이 포함될 수 있습니다. 자세한 내용은 [오디오 트랙](#audiotracks) 을 참조 하세요. |
| **메타데이터**  |key/value 문자열로 표시되는 자산 파일의 메타데이터입니다. <br />예: `<Metadata key="language" value="eng" />` |

### <a name="other-child-elements"></a>다른 자식 요소

| Name | 설명 |
| --- | --- |
| **이름**<br />필수 |자산 파일의 이름입니다. <br /><br />예: `"Name": "Ignite-short.mp4"` |
| **Uri**<br />필수 |입력 자산이 있는 URL입니다. 출력 자산이 속한 입력 자산을 식별 하려면 `Uri` ID 대신 필드를 사용 합니다.|
| **크기**<br />필수 |자산 파일의 크기(바이트)입니다.  <br /><br />예: `"Size": 75739259`|
| **Duration**<br />필수 |콘텐츠 재생 시간입니다. <br /><br />예: `"Duration": "PT1M10.304S"`. |
| **NumberOfStreams**<br />필수 |자산 파일의 스트림 수입니다.  <br /><br />예: `"NumberOfStreams": 2`|
| **FormatNames**<br />필수 |형식 이름입니다.  <br /><br />예: `"FormatNames": "mov,mp4,m4a,3gp,3g2,mj2"`|
| **FormatVerboseName**<br /> 필수 |자세한 형식 이름입니다. <br /><br />예: `"FormatVerboseName": "QuickTime / MOV"` |
| **StartTime** |콘텐츠 시작 시간입니다.  <br /><br />예: `"StartTime": "PT0S"` |
| **OverallBitRate** |자산 파일의 평균 비트 전송률 (비트/초)입니다.  <br /><br />예: `"OverallBitRate": 8618539`|

## <a name="videotracks"></a>VideoTracks

| Name |  | 설명 |
| --- | --- |
| **FourCC**<br />필수 |비디오 코덱은 ffmpeg에서 보고 하는 코드를 FourCC 합니다.<br /><br />예: `"FourCC": "avc1"` |
| **프로필** |비디오 트랙의 프로필입니다. <br /><br />예: `"Profile": "Main"`|
| **Level** |비디오 트랙의 수준입니다. <br /><br />예: `"Level": "3.2"`|
| **PixelFormat** |비디오 트랙의 픽셀 형식입니다. <br /><br />예: `"PixelFormat": "yuv420p"`|
| **Width**<br />필수 |인코딩된 비디오 너비(픽셀)입니다. <br /><br />예: `"Width": "1280"`|
| **높이**<br />필수 |인코딩된 비디오 높이(픽셀)입니다.<br /><br />예: `"Height": "720"` |
| **DisplayAspectRatioNumerator**<br />필수 |비디오 디스플레이 가로 세로 비율의 분자입니다.<br /><br />예: `"DisplayAspectRatioNumerator": 16.0` |
| **DisplayAspectRatioDenominator**<br />필수 |비디오 디스플레이 가로 세로 비율의 분모입니다. <br /><br />예: `"DisplayAspectRatioDenominator": 9.0`|
| **SampleAspectRatioNumerator** |비디오 샘플 가로 세로 비율의 분자입니다. <br /><br />예: `"SampleAspectRatioNumerator": 1.0`|
| **SampleAspectRatioDenominator**|예: `"SampleAspectRatioDenominator": 1.0`|
| **속도**<br />필수 |.3f 형식으로 측정된 비디오 프레임 속도입니다. <br /><br />예: `"FrameRate": 29.970`|
| **Bitrate** |자산 파일에서 계산 되는 평균 비디오 비트 전송률 (비트/초)입니다. 기본 스트림 페이로드만 계산되며, 패키징 오버헤드는 포함되지 않습니다. <br /><br />예: `"Bitrate": 8421583`|
| **HasBFrames** |B 프레임의 비디오 트랙 번호입니다. <br /><br />예: `"HasBFrames": 2`|
| **메타데이터** |다양한 정보를 저장하는 데 사용할 수 있는 일반 key/value 문자열입니다. <br />이 문서의 끝에 있는 전체 예제를 참조 하세요. |
| **ID**<br />필수 |이 오디오 또는 비디오 트랙의 0 기준 인덱스입니다.<br /><br /> 이 **ID**가 반드시 MP4 파일에 사용되는 TrackID일 필요는 없습니다. <br /><br />예: `"Id": 2`|
| **Codec** |비디오 트랙 코덱 문자열입니다. <br /><br />예: `"Codec": "h264"`|
| **CodecLongName** |오디오 또는 비디오 트랙 코덱의 긴 이름입니다. <br /><br />예: `"CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10"`|
| **Codec** |비디오 트랙 코덱 문자열입니다. <br /><br />예: `"Codec": "h264"`|
| **TimeBase**<br />필수 |시간 기준입니다.<br /><br />예: `"TimeBase": "1/30000"`|
| **NumberOfFrames** |프레임 수입니다(비디오 트랙의 경우). <br /><br />예: `"NumberOfFrames": 2107`|
| **StartTime** |트랙 시작 시간입니다.<br /><br />예: `"StartTime": "PT0.033S"` |
| **Duration** |트랙 지속 시간입니다. <br /><br />예: `"Duration": "PT1M10.304S"`|

## <a name="audiotracks"></a>AudioTracks

| Name  | 설명 |
| --- | --- | 
| **SampleFormat** |샘플 형식입니다. <br /><br />예: `"SampleFormat": "fltp"`|
| **ChannelLayout** |채널 레이아웃입니다. <br /><br />예: `"ChannelLayout": "stereo"`|
| **채널**<br />필수 |오디오 채널 수입니다(0개 이상). <br /><br />예: `"Channels": 2`|
| **SamplingRate**<br />필수 |오디오 샘플링 속도(샘플/초 또는 Hz)입니다. <br /><br />예: `"SamplingRate": 48000`|
| **Bitrate** |자산 파일에서 계산되는 평균 오디오 비트 전송률(bps)입니다. 기본 스트림 페이로드만 계산되며, 패키징 오버헤드는 이 개수에 포함되지 않습니다. <br /><br />예: `"Bitrate": 192080`|
| **메타데이터** |다양한 정보를 저장하는 데 사용할 수 있는 일반 key/value 문자열입니다.  <br />이 문서의 끝에 있는 전체 예제를 참조 하세요. |
| **ID**<br />필수 |이 오디오 또는 비디오 트랙의 0 기준 인덱스입니다.<br /><br /> 반드시 MP4 파일에 사용되는 TrackID일 필요는 없습니다. <br /><br />예: `"Id": 1`|
| **Codec** |비디오 트랙 코덱 문자열입니다. <br /><br />예: `"Codec": "aac"`|
| **CodecLongName** |오디오 또는 비디오 트랙 코덱의 긴 이름입니다. <br /><br />예: `"CodecLongName": "AAC (Advanced Audio Coding)"`|
| **TimeBase**<br />필수 |시간 기준입니다.<br /><br />예: `"TimeBase": "1/48000"` |
| **NumberOfFrames** |프레임 수입니다(비디오 트랙의 경우). <br /><br />예: `"NumberOfFrames": 3294`|
| **StartTime** |트랙 시작 시간입니다. 자세한 내용은 [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html)를 참조 하세요. <br /><br />예: `"StartTime": "PT0S"` |
| **Duration** |트랙 지속 시간입니다. <br /><br />예: `"Duration": "PT1M10.272S"` |

## <a name="metadata"></a>메타데이터

| Name | Description |
| --- | --- |
| **key**<br />필수 |key/value 쌍의 키입니다. |
| **value**<br /> 필수 |key/value 쌍의 값입니다. |

## <a name="schema-example"></a>스키마 예제

```json
{
  "AssetFile": [
    {
      "VideoTracks": [
        {
          "FourCC": "avc1",
          "Profile": "Main",
          "Level": "3.2",
          "PixelFormat": "yuv420p",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "SampleAspectRatioNumerator": 1.0,
          "SampleAspectRatioNumeratorSpecified": true,
          "SampleAspectRatioDenominator": 1.0,
          "SampleAspectRatioDenominatorSpecified": true,
          "FrameRate": 29.970,
          "Bitrate": 8421583,
          "BitrateSpecified": true,
          "HasBFrames": 2,
          "HasBFramesSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Video Media Handler"
            },
            {
              "key": "encoder",
              "value": "AVC Coding"
            }
          ],
          "Id": 2,
          "Codec": "h264",
          "CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10",
          "TimeBase": "1/30000",
          "NumberOfFrames": 2107,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0.033S",
          "Duration": "PT1M10.304S"
        }
      ],
      "AudioTracks": [
        {
          "SampleFormat": "fltp",
          "ChannelLayout": "stereo",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192080,
          "BitrateSpecified": true,
          "BitsPerSampleSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Sound Media Handler"
            }
          ],
          "Id": 1,
          "Codec": "aac",
          "CodecLongName": "AAC (Advanced Audio Coding)",
          "TimeBase": "1/48000",
          "NumberOfFrames": 3294,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0S",
          "Duration": "PT1M10.272S"
        }
      ],
      "Metadata": [
        {
          "key": "major_brand",
          "value": "mp42"
        },
        {
          "key": "minor_version",
          "value": "19529854"
        },
        {
          "key": "compatible_brands",
          "value": "mp42isom"
        },
        {
          "key": "creation_time",
          "value": "2018-02-21T21:42:08.000000Z"
        }
      ],
      "Name": "Ignite-short.mp4",
      "Uri": "https://amsstorageacct.blob.core.windows.net/asset-00000000-0000-0000-000000000000/ignite.mp4",
      "Size": 75739259,
      "Duration": "PT1M10.304S",
      "NumberOfStreams": 2,
      "FormatNames": "mov,mp4,m4a,3gp,3g2,mj2",
      "FormatVerboseName": "QuickTime / MOV",
      "StartTime": "PT0S",
      "OverallBitRate": 8618539,
      "OverallBitRateSpecified": true
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계

[출력 메타데이터](output-metadata-schema.md)
