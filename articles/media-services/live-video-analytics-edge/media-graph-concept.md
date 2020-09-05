---
title: 미디어 그래프 개념-Azure
description: 미디어 그래프를 사용 하 여 미디어를 캡처할 위치, 처리 방법 및 결과를 전달 해야 하는 위치를 정의할 수 있습니다. 이 문서에서는 미디어 그래프 개념에 대 한 자세한 설명을 제공 합니다.
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 6be741ee38cc8f1980fe9aa96883f9aacc1be8e2
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048428"
---
# <a name="media-graph"></a>미디어 그래프

## <a name="suggested-pre-reading"></a>추천 참고 자료

* [Live Video Analytics on IoT Edge 개요](overview.md)
* [Live Video Analytics on IoT Edge 용어](terminology.md)

## <a name="overview"></a>개요

미디어 그래프를 사용 하 여 미디어를 캡처할 위치, 처리 방법 및 결과를 전달 해야 하는 위치를 정의할 수 있습니다. 원하는 방식으로 구성 요소 또는 노드를 연결 하 여이를 수행 합니다. 아래 다이어그램에서는 미디어 그래프를 그래픽으로 표현 합니다.  

![미디어 그래프의 그래픽 표현](./media/media-graph/overview.png)

IoT Edge의 Live Video Analytics는 다양 한 유형의 원본, 프로세서 및 싱크를 지원 합니다.

* **원본 노드에서** 미디어 그래프로 미디어를 캡처할 수 있습니다. 이 컨텍스트의 미디어는 개념적으로 오디오 스트림, 비디오 스트림, 데이터 스트림 또는 단일 스트림으로 함께 결합 된 오디오, 비디오 및/또는 데이터가 있는 스트림이 될 수 있습니다.
* **프로세서 노드** 는 미디어 그래프 내의 미디어를 처리할 수 있도록 합니다.
* **싱크 노드** 는 미디어 그래프 외부의 서비스 및 앱에 처리 결과를 제공할 수 있도록 합니다.

## <a name="media-graph-topologies-and-instances"></a>미디어 그래프 토폴로지 및 인스턴스 

IoT Edge의 Live Video Analytics를 사용 하면 "graph 토폴로지" 및 "graph instance" 라는 두 가지 개념을 통해 미디어 그래프를 관리할 수 있습니다. 그래프 토폴로지를 사용 하면 매개 변수를 값의 자리 표시자로 사용 하 여 그래프의 청사진을 정의할 수 있습니다. 토폴로지는 미디어 그래프에서 사용 되는 노드 및 미디어 그래프 내에서 연결 되는 방법을 정의 합니다. 예를 들어 카메라에서 피드를 기록 하려는 경우 비디오를 수신 하는 원본 노드와 비디오를 쓰는 싱크 노드가 있는 그래프가 필요 합니다.

토폴로지의 매개 변수 값은 토폴로지를 참조 하는 그래프 인스턴스를 만들 때 지정 됩니다. 이를 통해 토폴로지에 지정 된 매개 변수에 대해 서로 다른 값을 사용 하 여 동일한 토폴로지를 참조 하는 여러 인스턴스를 만들 수 있습니다. 위의 예제에서 매개 변수를 사용 하 여 카메라의 IP 주소와 녹화 된 비디오의 이름을 나타낼 수 있습니다. 해당 토폴로지를 사용 하 여 여러 그래프 인스턴스를 만들 수 있습니다. 즉, 각 카메라에 대해 특정 IP 주소 및 특정 이름으로 각 카메라에 대해 하나의 인스턴스를 만들 수 있습니다.

## <a name="media-graph-states"></a>미디어 그래프 상태  

그래프 토폴로지 및 그래프 인스턴스의 수명 주기는 다음 상태 다이어그램에 표시 됩니다.

![그래프 토폴로지 및 그래프 인스턴스 수명 주기](./media/media-graph/graph-topology-lifecycle.svg)

[그래프 토폴로지를 만드는](direct-methods.md#graphtopologyset)것으로 시작 합니다. 그런 다음이 토폴로지에서 처리 하려는 각 라이브 비디오 피드에 대해 [그래프 인스턴스를 만듭니다](direct-methods.md#graphinstanceset). 

그래프 인스턴스는 `Inactive` (유휴 상태) 상태가 됩니다.

라이브 비디오 피드를 graph 인스턴스로 보낼 준비가 되 면 [활성화](direct-methods.md#graphinstanceactivate) 합니다. Graph 인스턴스는 transitionary 상태를 잠깐 이동 하 `Activating` 고 성공 하면 `Active` 상태로 전환 합니다. 상태에서 `Active` 미디어는 처리 됩니다 (graph 인스턴스가 입력 데이터를 수신 하는 경우).

> [!NOTE]
>  그래프 인스턴스는 데이터를 이동 하지 않고 활성 상태일 수 있습니다. 예를 들어 카메라가 오프 라인 상태가 됩니다.
> Graph 인스턴스가 활성 상태일 때 Azure 구독에 대 한 요금이 청구 됩니다.

다른 라이브 비디오 피드가 처리 될 경우 동일한 토폴로지에 대해 다른 그래프 인스턴스를 만들고 활성화 하는 프로세스를 반복할 수 있습니다.

라이브 비디오 피드의 처리가 완료 되 면 그래프 인스턴스를 [비활성화할](direct-methods.md#graphinstancedeactivate) 수 있습니다. Graph 인스턴스는 transitionary 상태를 잠깐 이동 `Deactivating` 하 여 포함 된 모든 데이터를 플러시한 다음 `Inactive` 상태로 돌아갑니다.

그래프 인스턴스는 상태에 있는 경우에만 [삭제할](direct-methods.md#graphinstancedelete) 수 있습니다 `Inactive` .

특정 그래프 토폴로지를 참조 하는 모든 그래프 인스턴스가 삭제 된 후에는 [그래프 토폴로지를 삭제할](direct-methods.md#graphtopologydelete)수 있습니다.


## <a name="sources-processors-and-sinks"></a>원본, 프로세서 및 싱크  

IoT Edge의 Live Video Analytics는 미디어 그래프 내에서 다음과 같은 유형의 노드를 지원 합니다.

### <a name="sources"></a>원본 

#### <a name="rtsp-source"></a>RTSP 원본 

RTSP 원본 노드를 사용 하면 [RTSP] (서버에서 미디어를 수집할 수 있습니다 https://tools.ietf.org/html/rfc2326 . 감시 및 IP 기반 카메라는 휴대폰 및 비디오 카메라와 같은 다른 종류의 장치와 다른 RTSP (실시간 스트리밍 프로토콜) 라는 프로토콜에서 데이터를 전송 합니다. 이 프로토콜은 서버 (카메라)와 클라이언트 간의 미디어 세션을 설정 하 고 제어 하는 데 사용 됩니다. 미디어 그래프의 RTSP 원본 노드는 클라이언트 역할을 하며 RTSP 서버를 사용 하 여 세션을 설정할 수 있습니다. 대부분의 [IP 카메라](https://en.wikipedia.org/wiki/IP_camera) 와 같은 장치에는 기본 제공 RTSP 서버가 있습니다. [Onvif](https://www.onvif.org/) 는 [프로필 G, S & T](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) 규격 장치 정의에서 RTSP를 지원 하도록 요구 합니다. RTSP 원본 노드에서는 인증 된 연결을 사용 하도록 설정 하는 자격 증명과 함께 RTSP URL을 지정 해야 합니다.

#### <a name="iot-hub-message-source"></a>IoT Hub 메시지 원본 

다른 [IoT Edge 모듈과](../../iot-edge/iot-edge-glossary.md#iot-edge-module)마찬가지로 IoT Edge 모듈의 Live Video Analytics는 [IoT Edge 허브](../../iot-edge/iot-edge-glossary.md#iot-edge-hub)를 통해 메시지를 받을 수 있습니다. 이러한 메시지는 다른 모듈,에 지 장치에서 실행 되는 앱 또는 클라우드에서 보낼 수 있습니다. 이러한 메시지는 모듈의 [명명 된 입력](../../iot-edge/module-composition.md#sink) 으로 전달 (라우팅됩니다) 됩니다. IoT Hub 메시지 원본 노드에서는 이러한 메시지가 미디어 그래프에 도달할 수 있습니다. 이러한 메시지 또는 신호를 미디어 그래프에서 내부적으로 사용할 수 있습니다. 일반적으로 신호 게이트를 활성화 하려면 (아래 [신호 게이트](#signal-gate-processor) 참조) 

예를 들어 문이 열릴 때 메시지를 생성 하는 IoT Edge 모듈을 사용할 수 있습니다. 해당 모듈의 메시지를 IoT Edge 허브로 라우팅할 수 있습니다. 그런 다음 미디어 그래프의 IoT hub 메시지 원본으로 라우팅할 수 있습니다. 미디어 그래프 내에서 IoT hub 메시지 원본은 신호 게이트 프로세서에 이벤트를 전달할 수 있습니다. 그러면 RTSP 원본에서 파일로 비디오 기록을 켤 수 있습니다. 

### <a name="processors"></a>프로세서  

#### <a name="motion-detection-processor"></a>동작 감지 프로세서 

동작 감지 프로세서 노드를 사용 하면 라이브 비디오에서 동작을 감지할 수 있습니다. 들어오는 비디오 프레임을 검사 하 고 비디오에서 이동이 있는지 확인 합니다. 동작이 감지 되 면 비디오 프레임을 다운스트림 구성 요소로 전달 하 고 이벤트를 내보냅니다. 이동 감지 프로세서 노드 (다른 노드와 함께)를 사용 하 여 동작이 감지 되 면 들어오는 비디오의 기록을 트리거할 수 있습니다.

#### <a name="frame-rate-filter-processor"></a>프레임 속도로 필터 프로세서  

프레임 속도 필터 프로세서 노드를 사용 하면 지정 된 속도에서 들어오는 비디오 스트림의 프레임을 샘플링할 수 있습니다. 그러면 추가 처리를 위해 다운 스트림 구성 요소 (예: HTTP 확장 프로세서 노드)로 전송 되는 프레임 수를 줄일 수 있습니다.

#### <a name="http-extension-processor"></a>HTTP 확장 프로세서

HTTP 확장 프로세서 노드를 사용 하면 고유한 IoT Edge 모듈을 미디어 그래프에 연결할 수 있습니다. 이 노드는 디코딩된 비디오 프레임을 입력으로 사용 하 고 이러한 프레임을 모듈에서 노출 하는 HTTP REST 끝점에 릴레이 합니다. 이 노드에는 필요한 경우 REST 끝점을 사용 하 여 인증 하는 기능이 있습니다. 또한 노드에는 REST 끝점에 릴레이 되기 전에 비디오 프레임의 크기를 조정 하 고 인코딩하기 위한 기본 제공 이미지 포맷터가 있습니다. Scaler에는 이미지 가로 세로 비율을 유지 하거나, 패딩 하거나, 늘일 수 있는 옵션이 있습니다. 이미지 인코더는 JPEG, PNG 또는 BMP 형식을 지원 합니다.

#### <a name="grpc-extension-processor"></a>gRPC 확장 프로세서

GRPC 확장 프로세서 노드는 디코딩된 비디오 프레임을 입력으로 사용 하 고 이러한 프레임을 모듈에서 제공 하는 [Grpc](terminology.md#grpc) 끝점에 릴레이 합니다. 또한 노드에는 gRPC 끝점으로 릴레이 되기 전에 비디오 프레임의 크기를 조정 하 고 인코딩하기 위한 기본 제공 이미지 포맷터가 있습니다. Scaler에는 이미지 가로 세로 비율을 유지 하거나, 패딩 하거나, 늘일 수 있는 옵션이 있습니다. 이미지 인코더는 jpeg, png 또는 bmp 형식을 지원 합니다.

#### <a name="signal-gate-processor"></a>신호 게이트 프로세서  

신호 게이트 프로세서 노드를 사용 하면 한 노드에서 다른 노드로 미디어를 조건부로 전달할 수 있습니다. 또한 미디어와 이벤트를 동기화 할 수 있도록 하는 버퍼 역할을 합니다. 일반적인 사용 사례는 RTSP 원본 노드와 자산 싱크 노드 사이에 신호 게이트 프로세서 노드를 삽입 하 고 동작 탐지기 프로세서 노드의 출력을 사용 하 여 게이트를 트리거하는 것입니다. 이러한 미디어 그래프를 사용 하면 동작이 검색 된 경우에만 비디오를 기록할 수 있습니다.

### <a name="sinks"></a>Sinks  

#### <a name="asset-sink"></a>자산 싱크  

자산 싱크 노드를 사용 하면 미디어 (비디오 및/또는 오디오) 데이터를 Azure Media Services 자산에 쓸 수 있습니다. 미디어 그래프에는 자산 싱크 노드가 하나만 있을 수 있습니다. 자산에 대 한 자세한 내용 및 미디어를 기록 하 고 재생 하는 역할에 대 한 자세한 내용은 [자산](terminology.md#asset) 섹션을 참조 하세요. 또한이 노드의 속성을 사용 하는 방법에 대 한 자세한 내용은 [연속 비디오 녹화](continuous-video-recording-concept.md) 문서를 참조 하세요.

#### <a name="file-sink"></a>파일 싱크  

파일 싱크 노드를 사용 하면 미디어 (비디오 및/또는 오디오) 데이터를 IoT Edge 장치의 로컬 파일 시스템 위치에 쓸 수 있습니다. 미디어 그래프에는 파일 싱크 노드가 하나만 있을 수 있으며 신호 게이트 프로세서 노드의 다운스트림 이어야 합니다. 그러면 출력 파일의 지속 시간이 신호 게이트 프로세서 노드 속성에 지정 된 값으로 제한 됩니다.

#### <a name="iot-hub-message-sink"></a>IoT Hub 메시지 싱크  

IoT Hub 메시지 싱크 노드를 사용 하 여 IoT Edge 허브에 이벤트를 게시할 수 있습니다. 그런 다음 IoT Edge 허브는 데이터를에 지 장치의 다른 모듈 또는 앱으로 라우팅하거나 배포 매니페스트에 지정 된 경로 마다 클라우드에서 IoT Hub 수 있습니다. IoT Hub 메시지 싱크 노드는 동작 감지 프로세서 노드와 같은 업스트림 프로세서 또는 HTTP 확장 프로세서 노드를 통해 외부 유추 서비스에서 발생 한 이벤트를 허용할 수 있습니다.

## <a name="rules-on-the-use-of-nodes"></a>노드 사용에 대 한 규칙

미디어 그래프 내에서 다양 한 노드를 사용할 수 있는 방법에 대 한 추가 규칙은 [그래프 토폴로지의 제한 사항](quotas-limitations.md#limitations-on-graph-topologies-at-preview) 을 참조 하세요.

## <a name="scenarios"></a>시나리오

위에서 정의한 원본, 프로세서 및 싱크 조합을 사용 하 여 라이브 비디오 분석과 관련 된 다양 한 시나리오에 대 한 미디어 그래프를 작성할 수 있습니다. 예제 시나리오는 다음과 같습니다.

* [연속 비디오 녹화](continuous-video-recording-concept.md)
* [이벤트 기반 비디오 녹화](event-based-video-recording-concept.md)
* [비디오 녹화가 없는 Live Video Analytics](analyze-live-video-concept.md)

## <a name="next-steps"></a>다음 단계

라이브 비디오 피드에서 동작 감지를 실행 하는 방법을 보려면 [빠른 시작: 사용자 고유의 모델로 라이브 비디오 분석 실행](use-your-model-quickstart.md)을 참조 하세요.
