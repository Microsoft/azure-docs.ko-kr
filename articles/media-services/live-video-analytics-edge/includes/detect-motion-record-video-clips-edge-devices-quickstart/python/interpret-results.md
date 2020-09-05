---
ms.openlocfilehash: 870b5b54c4a5afb39b43063bd00d3cb73e8ee0f9
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682336"
---
미디어 그래프를 실행하면 동작 감지기 프로세서 노드의 결과가 IoT Hub 싱크 노드를 통해 IoT 허브에 전송됩니다. Visual Studio Code의 **출력** 창에 표시되는 메시지에는 `body` 섹션과 `applicationProperties` 섹션이 포함되어 있습니다. 자세한 내용은 [IoT Hub 메시지 만들기 및 읽기](../../../../../iot-hub/iot-hub-devguide-messages-construct.md)를 참조하세요.

다음 메시지에서 Live Video Analytics 모듈은 애플리케이션 속성 및 본문의 콘텐츠를 정의합니다.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 이벤트

미디어 그래프가 인스턴스화되면 RTSP 원본 노드가 rtspsim-live555 컨테이너에서 실행되는 RTSP 서버에 연결을 시도합니다. 연결에 성공하면 다음 이벤트가 출력됩니다.

```
[IoTHubMonitor] [05:37:21 AM] Message received from [lva-sample-device/lvaEdge]:
{  
"body": {
"sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 xxx.xxx.xxx.xxx\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets={SPS}\r\na=control:track1\r\n"  
},  
"applicationProperties": {  
    "dataVersion": "1.0",  
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sources/rtspSource",  
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",  
    "eventTime": "2020-05-21T05:37:21.398Z",
    }  
}
```

앞의 출력에서 

* 메시지는 진단 이벤트 `MediaSessionEstablished`입니다. RTSP 원본 노드(주체)에서 RTSP 시뮬레이터와의 연결을 설정하고 라이브 피드(시뮬레이션된)를 받기 시작했다는 것을 나타냅니다.
* `applicationProperties`에서 `subject`는 메시지가 생성된 그래프 토폴로지의 노드를 참조합니다. 여기서는 메시지가 RTSP 원본 노드에서 시작됩니다.
* `applicationProperties`에서 `eventType`은 이 이벤트가 진단 이벤트임을 나타냅니다.
* `eventTime` 값은 이벤트가 발생한 시간을 나타냅니다.
* `body` 섹션에는 진단 이벤트에 대한 데이터가 포함됩니다. 이 예제의 데이터는 [SDP(세션 설명 프로토콜)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 세부 정보로 구성됩니다.

### <a name="recordingstarted-event"></a>RecordingStarted 이벤트

동작이 감지되면 신호 게이트 프로세서 노드가 활성화되고 미디어 그래프의 파일 싱크 노드가 MP4 파일 쓰기를 시작합니다. 파일 싱크 노드가 작업 이벤트를 보냅니다. `type`은 동작 감지 프로세서의 결과임을 나타내기 위해 `motion`으로 설정됩니다. `eventTime` 값은 동작이 발생한 UTC 시간입니다. 이 프로세스에 대한 자세한 내용은 이 빠른 시작의 [개요](#overview) 섹션을 참조하세요.

다음은 이 메시지의 예제입니다.

```
[IoTHubMonitor] [05:37:27 AM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "outputType": "filePath",
    "outputLocation": "/var/media/sampleFilesFromEVR-filesinkOutput-20200521T053726Z.mp4"
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",  
    "subject": "/graphInstances/Sample-Graph-1/sinks/fileSink",
    "eventType": "Microsoft.Media.Graph.Operational.RecordingStarted",
    "eventTime": "2020-05-21T05:37:27.713Z",
    "dataVersion": "1.0"
  }
}
```

위의 메시지에서 

* `applicationProperties`에서 `subject`는 메시지가 생성된 미디어 그래프의 노드를 참조합니다. 여기서는 메시지가 파일 싱크 노드에서 시작됩니다.
* `applicationProperties`에서 `eventType`은 이 이벤트가 작동 중임을 나타냅니다.
* `eventTime` 값은 이벤트가 발생한 시간을 나타냅니다. 이 시간은 `MediaSessionEstablished`가 지나고 비디오 흐름이 시작된 지 5-6초 후입니다. 이 시간은 [자동차가 주차장에 진입하기 시작한](#review-the-sample-video) 5-6초 마크에 해당합니다.
* `body` 섹션에는 운영 이벤트에 대한 데이터가 포함됩니다. 이 예제의 데이터는 `outputType` 및 `outputLocation`으로 구성됩니다.
* `outputType` 변수는 이 정보가 파일 경로에 대한 것임을 나타냅니다.
* `outputLocation` 값은 에지 모듈에서 MP4 파일의 위치입니다.

### <a name="recordingstopped-and-recordingavailable-events"></a>RecordingStopped 및 RecordingAvailable 이벤트

[그래프 토폴로지](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/topology.json)에서 신호 게이트 프로세서 노드의 속성을 살펴보면 활성화 시간이 5초로 설정된 것을 볼 수 있습니다. 따라서 `RecordingStarted` 이벤트가 수신되고 약 5초 후 다음 이벤트가 수신됩니다.

* 녹화가 중지되었음을 나타내는 `RecordingStopped` 이벤트
* 이제 MP4 파일을 볼 수 있음을 나타내는 `RecordingAvailable` 이벤트

일반적으로 두 이벤트는 서로 몇 초 간격을 두고 내보내집니다.
