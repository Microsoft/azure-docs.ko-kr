---
title: IoT Edge 릴리스 정보에 대 한 라이브 비디오 분석-Azure
description: 이 항목에서는 IoT Edge 릴리스, 개선 사항, 버그 수정 및 알려진 문제에 대 한 라이브 비디오 분석의 릴리스 정보를 제공 합니다.
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 23c2362d46a0b9bfe2fc2fbe31e00d0a42f80cfa
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055450"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>IoT Edge 릴리스 정보에 대 한 라이브 비디오 분석

>이 URL(`https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us`)을 RSS 피드 판독기에 복사하고 붙여넣어 업데이트를 위해 이 페이지를 다시 방문해야 하는 시기에 대한 알림을 받습니다.

이 문서에서는 다음에 대한 정보를 제공합니다.

* 최신 릴리스
* 알려진 문제
* 버그 수정
* 사용되지 않는 기능

## <a name="august-19-2020"></a>2020 년 8 월 19 일

모듈의 8 월 2020 새로 고침에 대 한이 릴리스 태그는 다음과 같습니다.

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> 빠른 시작 및 자습서에서 배포 매니페스트는 1 (라이브-비디오-분석: 1) 태그를 사용 합니다. 따라서 이러한 매니페스트를 다시 배포 하면 edge > 장치에서 모듈을 업데이트 해야 합니다.

## <a name="new-features"></a>새 기능 

* 이제 gRPC 프레임 워크를 사용 하 여 IoT Edge에서 라이브 비디오 분석과 사용자 지정 확장 간에 높은 데이터 콘텐츠 전송 성능을 얻을 수 있습니다. 시작 하려면 [이](analyze-live-video-use-your-grpc-model-quickstart.md) 항목을 참조 하세요.
* 라이브 비디오 분석의 광범위 한 지역 배포와 클라우드 서비스만 업데이트 되었습니다.  
* Live Video Analytics는 이제 전 세계 25 개의 추가 지역에서 사용할 수 있습니다. 다음은 사용 가능한 모든 지역 [목록](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) 입니다.  
* 빠른 시작에 대 한 [설정](https://aka.ms/lva-edge/setup-resources-for-samples) 도 새 지역 지원과 함께 업데이트 되었습니다.
    * 이미 리소스를 설정 하는 사용자에 대 한 작업 호출은 없습니다.

## <a name="bug-fixes"></a>버그 수정 

* 설정 스크립트에서 사용 되지 않는 azure 확장의 사용을 제거 합니다.

## <a name="july-13-2020"></a>2020 년 7 월 13 일

모듈의 7 월 2020 새로 고침에 대 한이 릴리스 태그는 다음과 같습니다.

```
mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> 빠른 시작 및 자습서에서 배포 매니페스트는 1 (라이브-비디오-분석: 1) 태그를 사용 합니다. 따라서 이러한 매니페스트를 다시 배포 하면 edge > 장치에서 모듈을 업데이트 해야 합니다.

### <a name="new-features"></a>새 기능
* 이제 자산 싱크 노드가 있는 그래프 토폴로지와 신호 게이트 프로세서 노드의 다운스트림 파일 싱크 노드를 만들 수 있습니다. 예제는 [이](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) 항목을 참조 하세요.

### <a name="bug-fixes"></a>버그 수정
* Desired 속성의 유효성 검사에 대 한 향상 된 기능

## <a name="june-1-2020"></a>2020년 6월 1일

이 릴리스는 IoT Edge에서 라이브 비디오 분석의 첫 번째 공개 미리 보기 릴리스입니다. 릴리스 태그는

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>지원 되는 기능
* 원하는 AI 모듈을 사용 하 여 라이브 비디오 스트림을 분석 하 고 필요에 따라 edge 장치 또는 클라우드에서 비디오 녹화
* IoT Edge에서 [지 원하는](../../iot-edge/support.md) Linux AMD64 운영 체제에서 사용
* Azure Portal 또는 Visual Studio Code를 사용 하 여 IoT Hub를 통해 모듈을 배포 하 고 구성 합니다.
* 다음 직접 메서드 호출을 통해 [그래프 토폴로지 및 그래프 인스턴스](media-graph-concept.md#media-graph-topologies-and-instances) 를 원격 또는 로컬로 관리

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList


## <a name="next-steps"></a>다음 단계

[개요](overview.md)
