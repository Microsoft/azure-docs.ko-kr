---
title: 모니터링 및 로깅-Azure
description: 이 문서에서는 IoT Edge 모니터링 및 로깅에 대 한 라이브 비디오 분석의 개요를 제공 합니다.
ms.topic: reference
ms.date: 04/27/2020
ms.openlocfilehash: e1f31c6bb3ea344286ad9af89417ca9f8fd59527
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934296"
---
# <a name="monitoring-and-logging"></a>모니터링 및 로깅

이 문서에서는 원격 모니터링을 위해 IoT Edge 모듈의 Live Video Analytics에서 이벤트를 수신 하는 방법에 대해 설명 합니다. 

모듈에서 생성 하는 로그를 제어 하는 방법에 대해서도 알아봅니다.

## <a name="taxonomy-of-events"></a>이벤트 분류

IoT Edge에 대 한 Live Video Analytics는 다음 분류에 따라 이벤트 또는 원격 분석 데이터를 내보냅니다.

![IoT Edge 원격 분석 스키마에 대 한 라이브 비디오 분석](./media/telemetry-schema/taxonomy.png)

* 작업: 사용자가 수행한 작업의 일부로 생성 되거나 [미디어 그래프](media-graph-concept.md)를 실행 하는 동안 생성 되는 이벤트입니다.
   
   * 볼륨: 낮을 것으로 예상 됩니다 (몇 분 정도 또는 더 낮은 속도).
   * 예:

      기록 시작 (아래), 기록 중지 됨
      
      ```
      {
        "body": {
          "outputType": "assetName",
          "outputLocation": "sampleAssetFromEVR-LVAEdge-20200512T233309Z"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sinks/assetSink",
          "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
          "eventTime": "2020-05-12T23:33:10.392Z",
          "dataVersion": "1.0"
        }
      }
      ```
* 진단: 문제 및/또는 성능 문제를 진단 하는 데 도움이 되는 이벤트입니다.

   * 볼륨: 높을 수 있습니다 (1 분에 여러 번).
   * 예:
   
      들어오는 비디오 피드의 RTSP [SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 정보 (아래) 또는 간격입니다.

      ```
      {
        "body": {
          "sdp": "SDP:\nv=0\r\no=- 1589326384077235 1 IN IP4 XXX.XX.XX.XXX\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/lots_015.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.04.12\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-73.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/lots_015.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=640028;sprop-parameter-sets=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\r\na=control:track1\r\n"
        },
        "applicationProperties": {
          "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
          "subject": "/graphInstances/Sample-Graph-2/sources/rtspSource",
          "eventType": "Microsoft.Media.Graph.Diagnostics.MediaSessionEstablished",
          "eventTime": "2020-05-12T23:33:04.077Z",
          "dataVersion": "1.0"
        }
      }
      ```
* 분석: 비디오 분석의 일부로 생성 되는 이벤트입니다.

   * 볼륨: 높을 수 있습니다 (1 분 이상 여러 번).
   * 예:
      
      동작이 검색 되었습니다 (아래). 유추 결과.
   ```      
   {
     "body": {
       "timestamp": 143039375044290,
       "inferences": [
         {
           "type": "motion",
           "motion": {
             "box": {
               "l": 0.48954,
               "t": 0.140741,
               "w": 0.075,
               "h": 0.058824
             }
           }
         }
       ]
     },
     "applicationProperties": {
       "topic": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/<my-resource-group>/providers/microsoft.media/mediaservices/<ams-account-name>",
       "subject": "/graphInstances/Sample-Graph-2/processors/md",
       "eventType": "Microsoft.Media.Graph.Analytics.Inference",
       "eventTime": "2020-05-12T23:33:09.381Z",
       "dataVersion": "1.0"
     }
   }
   ```
모듈에서 내보낸 이벤트는 [IoT Edge 허브](../../iot-edge/iot-edge-runtime.md#iot-edge-hub)로 전송 되며 여기에서 다른 대상으로 라우팅될 수 있습니다. 

### <a name="timestamps-in-analytic-events"></a>분석 이벤트의 타임 스탬프
위에서 설명한 것 처럼 비디오 분석의 일부로 생성 된 이벤트에는 연결 된 타임 스탬프가 있습니다. [라이브 비디오](video-recording-concept.md) 를 그래프 토폴로지의 일부로 기록한 경우이 타임 스탬프를 사용 하면 기록 된 비디오에서 특정 이벤트가 발생 한 위치를 찾을 수 있습니다. 다음은 분석 이벤트의 타임 스탬프를 [Azure Media Service 자산](terminology.md#asset)에 기록 되는 비디오의 타임 라인에 매핑하는 방법에 대 한 지침입니다.

먼저 값을 추출 `eventTime` 합니다. [시간 범위 필터](playback-recordings-how-to.md#time-range-filters) 에이 값을 사용 하 여 기록의 적절 한 부분을 검색 합니다. 예를 들어 30 초 전에 시작 되 고 30 초 후에 시작 되는 비디오를 가져올 수 있습니다 `eventTime` . 위의 예제에서 `eventTime` 이 (가) 2020-05-12T23:33:09.381 z 인 경우 +/-30 초 창의 HLS 매니페스트에 대 한 요청은 다음과 같이 표시 됩니다.
```
https://{hostname-here}/{locatorGUID}/content.ism/manifest(format=m3u8-aapl,startTime=2020-05-12T23:32:39Z,endTime=2020-05-12T23:33:39Z).m3u8
```
위의 URL은 미디어 재생 목록에 대 한 Url을 포함 하는 [마스터 재생 목록을](https://developer.apple.com/documentation/http_live_streaming/example_playlists_for_http_live_streaming)반환 합니다. 미디어 재생 목록은 다음과 같은 항목을 포함 합니다.

```
...
#EXTINF:3.103011,no-desc
Fragments(video=143039375031270,format=m3u8-aapl)
...
```
위의 항목에서 항목은 타임 스탬프 값에서 시작 하는 비디오 조각을 사용할 수 있음을 보고 `143039375031270` 합니다. `timestamp`분석 이벤트의 값은 미디어 재생 목록과 동일한 날짜/시간을 사용 하며, 관련 비디오 조각을 식별 하 고 올바른 프레임을 검색 하는 데 사용할 수 있습니다.

자세한 내용은 HLS에서 프레임의 정확한 검색에 대 한 여러 [문서](https://www.bing.com/search?q=frame+accurate+seeking+in+HLS) 중 하나를 참조 하세요.

## <a name="controlling-events"></a>이벤트 제어

[모듈 쌍 JSON 스키마](module-twin-configuration-schema.md)에 설명 된 대로 다음 모듈 쌍 속성을 사용 하 여 IoT Edge 모듈의 Live Video Analytics에서 게시 된 운영 및 진단 이벤트를 제어할 수 있습니다.

`diagnosticsEventsOutputName` – 모듈에서 진단 이벤트를 가져오기 위해이 속성의 값을 포함 하 고 제공 합니다. 모듈이 진단 이벤트를 게시 하지 않도록 하려면이를 생략 하거나 비워 둡니다.
   
`operationalEventsOutputName` – 모듈에서 작업 이벤트를 가져오기 위해이 속성의 값을 포함 하 고 제공 합니다. 작업 이벤트를 게시 하지 않으려면 모듈을 생략 하거나 비워 둡니다.
   
분석 이벤트는 동작 검색 프로세서 또는 HTTP 확장 프로세서와 같은 노드에 의해 생성 되며, IoT hub 싱크는 IoT Edge 허브로 전송 하는 데 사용 됩니다. 

배포 매니페스트에서 $edgeHub 모듈 쌍의 desired 속성을 통해 [위의 모든 이벤트 라우팅을](../../iot-edge/module-composition.md#declare-routes) 제어할 수 있습니다.

```
 "$edgeHub": {
   "properties.desired": {
     "schemaVersion": "1.0",
     "routes": {
       "moduleToHub": "FROM /messages/modules/lvaEdge/outputs/* INTO $upstream"
     },
     "storeAndForwardConfiguration": {
       "timeToLiveSecs": 7200
     }
   }
 }
```

위의 경우 lvaEdge는 IoT Edge 모듈에서 라이브 비디오 분석의 이름이 고, 라우팅 규칙은 [declare 경로](../../iot-edge/module-composition.md#declare-routes)에 정의 된 스키마를 따릅니다.

> [!NOTE]
> 분석 이벤트가 IoT Edge 허브에 도달 하 게 하려면 모든 동작 감지 프로세서 노드 및/또는 모든 HTTP 확장 프로세서 노드에 대 한 IoT Hub 싱크 노드 다운스트림이 있어야 합니다.

## <a name="event-schema"></a>이벤트 스키마

이벤트는에 지 장치에서 시작 되며 Edge 또는 클라우드에서 사용할 수 있습니다. IoT Edge에서 라이브 비디오 분석에 의해 생성 된 이벤트는 시스템 속성, 응용 프로그램 속성 및 본문을 사용 하 여 Azure IoT Hub에 의해 설정 된 [스트리밍 메시징 패턴](../../iot-hub/iot-hub-devguide-messages-construct.md) 을 따릅니다.

### <a name="summary"></a>요약

IoT Hub를 통해 관찰 된 모든 이벤트에는 아래에 설명 된 대로 공용 속성 집합이 있습니다.

|속성   |속성 유형| 데이터 형식   |Description|
|---|---|---|---|
|message-id |시스템 |guid|  고유한 이벤트 ID입니다.|
|토픽| applicationProperty |문자열|    Media Services 계정의 Azure Resource Manager 경로입니다.|
|subject|   applicationProperty |문자열|    이벤트를 내보내는 엔터티에 대 한 하위 경로입니다.|
|eventTime| applicationProperty|    문자열| 이벤트가 생성 된 시간입니다.|
|eventType| applicationProperty |문자열|    이벤트 유형 식별자 (아래 참조)|
|본문|본문  |object|    특정 이벤트 데이터입니다.|
|dataVersion    |applicationProperty|   문자열  |{Major}. 주|

### <a name="properties"></a>속성

#### <a name="message-id"></a>message-id

이벤트 GUID (globally unique identifier)

#### <a name="topic"></a>토픽

그래프와 연결 된 Azure Media 서비스 계정을 나타냅니다.

`/subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.Media/mediaServices/{accountName}`

#### <a name="subject"></a>subject

이벤트를 내보내는 엔터티입니다.

`/graphInstances/{graphInstanceName}`<br/>
`/graphInstances/{graphInstanceName}/sources/{sourceName}`<br/>
`/graphInstances/{graphInstanceName}/processors/{processorName}`<br/>
`/graphInstances/{graphInstanceName}/sinks/{sinkName}`

Subject 속성을 사용 하면 제네릭 이벤트를 생성 하는 모듈에 매핑할 수 있습니다. 예를 들어 잘못 된 RTSP 사용자 이름 또는 암호가 있는 경우 생성 된 이벤트는 `Microsoft.Media.Graph.Diagnostics.ProtocolError` `/graphInstances/myGraph/sources/myRtspSource` 노드에 있습니다.

#### <a name="event-types"></a>일정 유형

다음 스키마에 따라 이벤트 유형이 네임 스페이스에 할당 됩니다.

`Microsoft.Media.Graph.{EventClass}.{EventType}`

#### <a name="event-classes"></a>이벤트 클래스

|클래스 이름|Description|
|---|---|
|분석  |콘텐츠 분석의 일부로 생성 되는 이벤트입니다.|
|진단    |문제 및 성능 진단에 도움이 되는 이벤트입니다.|
|작동    |리소스 작업의 일부로 생성 되는 이벤트입니다.|

이벤트 유형은 각 이벤트 클래스에 대해 고유 합니다.

예:

* Microsoft..
* . Diagnostics..
* Microsoft. Graph. GraphInstanceStarted

### <a name="event-time"></a>이벤트 시간

이벤트 시간은 ISO8601 문자열 및 이벤트가 발생 한 시간에 설명 되어 있습니다.

## <a name="logging"></a>로깅

다른 IoT Edge 모듈과 마찬가지로 Edge 장치에서 [컨테이너 로그를 검사할](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) 수도 있습니다. 로그에 기록 되는 정보는 [다음 모듈](module-twin-configuration-schema.md) 쌍 속성에 의해 제어 될 수 있습니다.

* logLevel

   * 허용 되는 값은 Verbose, Information, Warning, Error, None입니다.
   * 기본값은 정보-로그에 오류, 경고 및 정보가 포함 됩니다. 메시지가.
   * 값을 Warning으로 설정 하면 로그에 오류 및 경고 메시지가 포함 됩니다.
   * 값을 Error로 설정 하면 로그에 오류 메시지만 포함 됩니다.
   * 값을 없음으로 설정 하면 로그가 생성 되지 않습니다 (권장 되지 않음).
   * 문제를 진단 하기 위해 Azure 지원에 로그를 공유 해야 하는 경우에만 자세한 정보를 사용 해야 합니다.
* logCategories

   * 응용 프로그램, 이벤트, MediaPipeline 중 하나 이상의 쉼표로 구분 된 목록입니다.
   * 기본값: 응용 프로그램, 이벤트
   * 응용 프로그램 – 모듈 시작 메시지, 환경 오류 및 직접 메서드 호출과 같은 모듈의 상위 수준 정보입니다.
   * 이벤트-이 문서 앞부분에서 설명한 모든 이벤트입니다.
   * MediaPipeline – RTSP 가능 카메라와의 연결을 설정 하는 데 어려움이 있는 경우와 같이 문제를 해결할 때 통찰력을 제공할 수 있는 하위 수준 로그입니다.
   
### <a name="generating-debug-logs"></a>디버그 로그 생성

특정 한 경우에는 Azure 지원에서 문제를 해결 하는 데 도움이 되도록 위에 설명 된 것 보다 더 자세한 로그를 생성 해야 할 수 있습니다. 이 작업을 수행 하는 두 가지 단계가 있습니다.

먼저 createOptions를 통해 [장치 저장소에 모듈 저장소를 연결](../../iot-edge/how-to-access-host-storage-from-module.md#link-module-storage-to-device-storage) 합니다. 빠른 시작에서 [배포 매니페스트 템플릿을](https://github.com/Azure-Samples/live-video-analytics-iot-edge-csharp/blob/master/src/edge/deployment.template.json) 검사 하면 다음이 표시 됩니다.

```
"createOptions": {
   …
   "Binds": [
     "/var/local/mediaservices/:/var/lib/azuremediaservices/"
   ]
 }
```

위의 단계를 통해 Edge 모듈이 (장치) 저장소 경로 "/var/local/mediaservices/"에 로그를 쓸 수 있습니다. 모듈에 다음과 같은 desired 속성을 추가 합니다.

`"debugLogsDirectory": "/var/lib/azuremediaservices/debuglogs/",`

그런 다음이 모듈은 디버그 로그를 이진 형식으로 Azure 지원과 공유할 수 있는 (장치) 저장소 경로/var/local/mediaservices/debuglogs/에 씁니다.

## <a name="faq"></a>FAQ

[FAQ](faq.md#monitoring-and-metrics)

## <a name="next-steps"></a>다음 단계

[연속 비디오 녹화](continuous-video-recording-tutorial.md)
