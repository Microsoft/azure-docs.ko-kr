---
ms.openlocfilehash: 2cf9cde80a46023575a4e0833de5c2c6b90ab10e
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687240"
---
미디어 그래프를 실행하면 HTTP 확장 프로세서 노드의 결과가 IoT Hub 싱크 노드를 통해 IoT 허브로 전달됩니다. **OUTPUT** 창에 표시되는 메시지에는 본문 섹션과 applicationProperties 섹션이 포함되어 있습니다. 자세한 내용은 [IoT Hub 메시지 만들기 및 읽기](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct)를 참조하세요.

다음 메시지에서 Live Video Analytics 모듈은 애플리케이션 속성 및 본문의 콘텐츠를 정의합니다.

### <a name="mediasessionestablished-event"></a>MediaSessionEstablished 이벤트

미디어 그래프가 인스턴스화되면 RTSP 원본 노드가 rtspsim-live555 컨테이너에서 실행되는 RTSP 서버에 연결을 시도합니다. 연결에 성공하면 다음 이벤트가 출력됩니다. 이벤트 유형은 `Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished`입니다.

```
[IoTHubMonitor] [9:42:18 AM] Message received from [lvaedgesample/lvaEdge]:
{  "body": {
    "sdp": "SDP:\nv=0\r\no=- 1586450538111534 1 IN IP4 nnn.nn.0.6\r\ns=Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\ni=media/camera-300s.mkv\r\nt=0 0\r\na=tool:LIVE555 Streaming Media v2020.03.06\r\na=type:broadcast\r\na=control:*\r\na=range:npt=0-300.000\r\na=x-qt-text-nam:Matroska video+audio+(optional)subtitles, streamed by the LIVE555 Media Server\r\na=x-qt-text-inf:media/camera-300s.mkv\r\nm=video 0 RTP/AVP 96\r\nc=IN IP4 0.0.0.0\r\nb=AS:500\r\na=rtpmap:96 H264/90000\r\na=fmtp:96 packetization-mode=1;profile-level-id=4D0029;sprop-parameter-sets=Z00AKeKQCgC3YC3AQEBpB4kRUA==,aO48gA==\r\na=control:track1\r\n"
  },
  "applicationProperties": {
    "dataVersion": "1.0",
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/sources/rtspSource",
    "eventType": "Microsoft.Media.MediaGraph.Diagnostics.MediaSessionEstablished",
    "eventTime": "2020-04-09T16:42:18.1280000Z"
  }
}
```

이 메시지에서 다음 세부 정보를 확인합니다.
* 메시지는 진단 이벤트입니다. `MediaSessionEstablished`는 RTSP 원본 노드(주체)에서 RTSP 시뮬레이터에 연결하고 라이브 피드(시뮬레이션된)를 받기 시작했음을 나타냅니다.
* `applicationProperties`에서 제목은 메시지가 미디어 그래프의 RTSP 원본 노드에서 생성되었음을 나타냅니다.
* `applicationProperties`에서 `eventType`은 이 이벤트가 진단 이벤트임을 나타냅니다.
* `eventTime`은 이벤트가 발생한 시간을 나타냅니다.
* 본문에는 진단 이벤트에 대한 데이터가 포함됩니다. 이 경우 데이터는 [SDP(세션 설명 프로토콜)](https://en.wikipedia.org/wiki/Session_Description_Protocol) 세부 정보로 구성됩니다.

### <a name="inference-event"></a>유추 이벤트

gRPC 확장 프로세서 노드는 lvaExtension 모듈에서 유추 결과를 받습니다. 그런 다음, IoT Hub 싱크 노드를 통해 결과를 유추 이벤트로 내보냅니다.
이러한 이벤트에서 유형은 자동차 또는 트럭과 같은 엔터티임을 나타내기 위해 엔터티로 설정됩니다. `eventTime` 값은 개체가 감지된 UTC 시간입니다.
다음 예제에서는 신뢰 수준이 서로 다른 세 대의 자동차가 동일한 비디오 프레임에서 감지되었습니다.

```
[IoTHubMonitor] [7:52:57 PM] Message received from [lva-sample-device/lvaEdge]:
{
  "body": {
    "timestamp": 143802500954716,
    "inferences": [
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "car",
            "confidence": 0.86707145
          },
          "attributes": [],
          "box": {
            "l": 0.7294476,
            "t": 0.567829,
            "w": 0.031738576,
            "h": 0.027762715
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "car",
            "confidence": 0.8634398
          },
          "attributes": [],
          "box": {
            "l": 0.4765757,
            "t": 0.59559196,
            "w": 0.05597749,
            "h": 0.048316106
          }
        },
        "extensions": {},
        "valueCase": "entity"
      },
      {
        "type": "entity",
        "subtype": "",
        "inferenceId": "",
        "relatedInferences": [],
        "entity": {
          "tag": {
            "value": "car",
            "confidence": 0.67120105
          },
          "attributes": [],
          "box": {
            "l": 0.7247387,
            "t": 0.49816906,
            "w": 0.032748587,
            "h": 0.030686663
          }
        },
        "extensions": {},
        "valueCase": "entity"
      }
    ]
  },
  "applicationProperties": {
    "topic": "/subscriptions/{subscriptionID}/resourceGroups/{name}/providers/microsoft.media/mediaservices/hubname",
    "subject": "/graphInstances/GRAPHINSTANCENAMEHERE/processors/grpcExtension",
    "eventType": "Microsoft.Media.Graph.Analytics.Inference",
    "eventTime": "2020-08-19T02:52:57.174Z",
    "dataVersion": "1.0"
  }
}
```

메시지에서 다음 세부 정보를 확인합니다.

* `applicationProperties`에서 제목은 메시지가 생성된 그래프 토폴로지의 노드를 참조합니다.
* `applicationProperties`에서 eventType은 이 이벤트가 분석 이벤트임을 나타냅니다.
* `eventTime` 값은 이벤트가 발생한 시간을 나타냅니다.
* `body` 섹션에는 분석 이벤트에 대한 데이터가 포함됩니다. 이 경우 이벤트는 유추 이벤트이므로 본문에 추론 데이터가 포함됩니다.
* `inferences` 섹션은 유형이 엔터티임을 나타냅니다. 이 섹션에는 엔터티에 대한 추가 데이터가 포함됩니다.
