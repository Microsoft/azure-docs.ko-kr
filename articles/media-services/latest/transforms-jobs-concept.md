---
title: Azure Media Services의 Transform 및 Job | Microsoft Docs
description: Media Services를 사용할 때 비디오를 처리하기 위한 규칙이나 사양을 설명하는 변환을 만들어야 합니다. 이 문서에서는 Transform의 개념과 사용법에 대해 간략히 설명합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/20/2019
ms.author: juliako
ms.openlocfilehash: 1c2ec576211741390ef91233101261a7881e4180
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466751"
---
# <a name="transforms-and-jobs"></a>Transform 및 Job

이 항목에 대 한 세부 정보를 제공 [변형](https://docs.microsoft.com/rest/api/media/transforms) 하 고 [작업](https://docs.microsoft.com/rest/api/media/jobs) 이러한 엔터티 간의 관계를 설명 합니다. 다음 다이어그램은 변환/작업 워크플로를 보여 줍니다.

![변환](./media/encoding/transforms-jobs.png)

> [!NOTE]
> 날짜/시간 형식의 **변환** 및 **작업** 속성의 우선 순위는 언제나 UTC 형식입니다.

## <a name="transforms"></a>변환

**Transforms**는 비디오 인코딩 또는 분석에 대한 일반적인 작업을 구성하는 데 사용할 수 있습니다. 각 **변환**은 비디오 또는 오디오 파일을 처리하는 작업의 작성법 또는 워크플로를 설명합니다. 한 변환을 여러 규칙에 적용할 수 있습니다. 예를 들어 각 비디오를 지정된 비트 전송률에서 MP4 파일로 인코딩하고 비디오의 첫 번째 프레임으로 썸네일을 생성하도록 변환을 지정할 수 있습니다. 변환에 포함하려는 규칙마다 TransformOutput 항목 하나를 추가합니다. Media Services v3 API 또는 게시된 SDK를 사용하여 Media Services 계정에 변환을 만들 수 있습니다. Media Services v3 API는 Azure Resource Manager에 의해 실행되기 때문에 Resource Manager 템플릿을 사용하여 Media Services 계정에 변환을 만들고 배포할 수 있습니다. 역할 기반 액세스 제어를 사용하여 변환에 대한 액세스를 잠글 수 있습니다.

[변환](https://docs.microsoft.com/rest/api/media/transforms) 엔터티에 대한 업데이트 작업은 기반 TransformOutputs의 설명 또는 우선 순위를 변경하기 위한 것입니다. 해당 업데이트는 모든 진행 중인 작업이 완료되었을 때 수행하는 것이 좋습니다. 레시피를 다시 작성하려면 새 변환을 만들어야 합니다.

## <a name="jobs"></a>교육

**작업**은 **변환**을 특정 입력 비디오 또는 오디오 콘텐츠에 적용하기 위한 Azure Media Services에 대한 실제 요청입니다. 변환을 만든 후에는 Media Services API 또는 게시된 SDK를 사용하여 작업을 제출할 수 있습니다. **작업**은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정합니다. 입력 비디오의 위치는 HTTPS URL, SAS URL 또는 [자산](https://docs.microsoft.com/rest/api/media/assets)을 사용하여 지정할 수 있습니다.  

사용 하 여 [HTTPS에서 입력 작업](job-input-from-http-how-to.md) 경우 콘텐츠 URL을 통해 액세스할 수 있는 이미 및 Azure에서 소스 파일을 저장할 필요가 없습니다 (예를 들어, S3에서 가져오기). 이 방법은 Azure Blob storage에서 콘텐츠를 포함 하지만 파일을 자산에 대 한 필요가 없습니다에 적합 합니다. 현재이 메서드는 입력에 대 한 단일 파일로 지원 합니다.
 
사용 하 여 [작업 입력 자산](job-input-from-local-file-how-to.md) 자산에는 입력된 콘텐츠가 이미 경우 콘텐츠를 로컬 파일에 저장 됩니다. 스트리밍 또는 다운로드 (예를 들어 하려는 다운로드용 mp4를 게시할 수도 음성 텍스트 또는 얼굴 감지를 위해)에 대 한 입력된 자산을 게시 하려는 경우 유용한 옵션 이기도 합니다. 이 메서드는 다중 파일 자산 (예를 들어, 로컬로 인코딩된 집합 스트리밍 MBR)를 지원 합니다.
 
Event Grid로 이벤트를 모니터링하여 작업의 진행 상황 및 상태를 가져올 수 있습니다. 자세한 내용은 [Event Grid를 사용하여 이벤트 모니터링](job-state-events-cli-how-to.md)을 참조하세요.

작업을 제출한 후 [작업](https://docs.microsoft.com/rest/api/media/jobs) 엔터티에 대한 업데이트 작업을 사용하여 *설명* 및 *우선 순위* 속성을 수정할 수 있습니다. *우선 순위* 속성에 대한 변경은 작업이 아직 큐에 대기 중인 상태에만 유효합니다. 작업 처리가 시작되었거나 완료된 경우 우선 순위를 변경해도 아무 효과가 없습니다.

## <a name="typical-workflow"></a>일반적인 워크플로

1. Transform 만들기 
2. Transform에 따라 Job 제출 
3. Transform 나열 
4. 나중에 이 "변환"을 사용할 계획이 아니면 삭제하세요. 

### <a name="example"></a>예

모든 비디오의 첫 번째 프레임을 썸네일 이미지로 추출하려는 경우 수행할 단계는 다음과 같습니다. 

1. 작성법 또는 비디오 처리 규칙을 정의합니다(예: "비디오의 첫 번째 프레임을 썸네일로 사용"). 
2. 각 비디오와 관련하여 서비스에 다음 정보를 제공합니다. 
    1. 해당 비디오를 찾을 위치  
    2. 출력 썸네일 이미지를 기록할 위치 

**변환**을 사용하면 작성법을 만들고(1단계), 해당 작성법을 사용하여 작업을 제출(2단계)할 수 있습니다.

## <a name="job-error-codes"></a>작업 오류 코드

[오류 코드](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)를 참조하세요.

## <a name="paging"></a>Paging

[Media Services 엔터티 필터링, 순서 지정, 페이징](entities-overview.md)을 참조하세요.

## <a name="configure-media-reserved-units"></a>미디어 예약 단위 구성

Media Services v3 또는 Video Indexer에 의해 트리거되는 오디오 분석 및 비디오 분석 작업의 경우 10개의 S3 MRU(미디어 예약 단위)를 사용하여 계정을 프로비전하는 것이 좋습니다. 10개가 넘는 S3 MRU가 필요한 경우 [Azure Portal](https://portal.azure.com/)을 사용하여 지원 티켓을 엽니다.

자세한 내용은 [CLI를 사용하여 미디어 처리 크기 조정](media-reserved-units-cli-how-to.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [자습서: .NET](stream-files-tutorial-with-api.md)를 사용하여 비디오 업로드, 인코딩 및 스트림
- [자습서: .NET을 사용하여 Media Services v3에서 비디오 분석](analyze-videos-tutorial-with-api.md)
