---
title: Media Services 변환 및 작업
titleSuffix: Azure Media Services
description: Azure Media Services에서 비디오를 처리 하는 규칙을 설명 하는 변환을 만드는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/19/2019
ms.author: inhenkel
ms.openlocfilehash: 103509de90d8819b19c46ebba7ae2dcc6071ac33
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89256072"
---
# <a name="transforms-and-jobs-in-media-services"></a>Media Services 변환 및 작업

이 항목에서는 [변환](/rest/api/media/transforms) 및 [작업](/rest/api/media/jobs) 에 대 한 세부 정보를 제공 하 고 이러한 엔터티 간의 관계를 설명 합니다.

## <a name="overview"></a>개요

### <a name="transformsjobs-workflow"></a>변환/작업 워크플로

다음 다이어그램에서는 변환/작업 워크플로를 보여 줍니다.

![Azure Media Services의 변환 및 작업 워크플로](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>일반적인 워크플로

1. 변환을 만듭니다.
2. 해당 변환에서 작업을 제출 합니다.
3. 변환 목록을 표시 합니다.
4. 변환을 나중에 사용 하지 않으려는 경우 삭제 합니다.

#### <a name="example"></a>예

모든 비디오의 첫 번째 프레임을 미리 보기 이미지로 추출 한다고 가정해 보겠습니다. 수행 하는 단계는 다음과 같습니다.

1. 조리법을 정의 하거나 비디오를 처리 하는 규칙을 정의 합니다. "비디오의 첫 번째 프레임을 축소판 그림으로 사용" 합니다.
2. 각 비디오에 대해 서비스를 알려 줍니다.
    1. 비디오를 찾을 수 있는 위치입니다.
    2. 출력 썸네일 이미지를 기록할 위치

**변환**을 사용하면 작성법을 만들고(1단계), 해당 작성법을 사용하여 작업을 제출(2단계)할 수 있습니다.

> [!NOTE]
> Datetime 형식의 **변환** 및 **작업** 속성은 항상 UTC 형식입니다.

## <a name="transforms"></a>변형

**Transforms**는 비디오 인코딩 또는 분석에 대한 일반적인 작업을 구성하는 데 사용할 수 있습니다. 각 **변환은** 비디오 또는 오디오 파일을 처리 하기 위한 조리법 또는 작업 워크플로를 설명 합니다. 한 변환을 여러 규칙에 적용할 수 있습니다. 예를 들어 각 비디오를 지정된 비트 전송률에서 MP4 파일로 인코딩하고 비디오의 첫 번째 프레임으로 썸네일을 생성하도록 변환을 지정할 수 있습니다. 변환에 포함하려는 규칙마다 TransformOutput 항목 하나를 추가합니다. 미리 설정을 사용 하 여 입력 미디어 파일을 처리 하는 방법을 변환에 알립니다.

### <a name="viewing-schema"></a>스키마 보기

Media Services v 3에서 사전 설정은 API 자체에서 강력한 형식의 엔터티입니다. [OPEN API 사양 (또는 Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)에서 이러한 개체에 대 한 "스키마" 정의를 찾을 수 있습니다. [REST API](/rest/api/media/transforms/createorupdate#standardencoderpreset), [.net SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)또는 기타 Media Services v3 sdk 참조 설명서에서 미리 설정 된 정의 (예: **StandardEncoderPreset**)를 볼 수도 있습니다.

### <a name="creating-transforms"></a>변환 만들기

REST, CLI 또는 게시 된 Sdk를 사용 하 여 변환을 만들 수 있습니다. Media Services v3 API는 Azure Resource Manager에 의해 실행되기 때문에 Resource Manager 템플릿을 사용하여 Media Services 계정에 변환을 만들고 배포할 수 있습니다. 역할 기반 액세스 제어를 사용하여 변환에 대한 액세스를 잠글 수 있습니다.

### <a name="updating-transforms"></a>변환 업데이트

[변환을](/rest/api/media/transforms)업데이트 해야 하는 경우 **업데이트** 작업을 사용 합니다. 설명 또는 기본 TransformOutputs의 우선 순위를 변경 하기 위한 것입니다. 모든 진행 중인 작업이 완료 되 면 이러한 업데이트를 수행 하는 것이 좋습니다. 조리법을 다시 작성 하려는 경우 새 변환을 만들어야 합니다.

### <a name="transform-object-diagram"></a>개체 변환 다이어그램

다음 다이어그램에서는 **변환** 개체와이 개체가 참조 하는 개체 (파생 관계 포함)를 보여 줍니다. 회색 화살표에는 작업에서 참조 하는 형식과 녹색 화살표가 클래스 파생 관계를 표시 합니다.

이미지를 선택 하 여 전체 크기를 확인 합니다.  

[![개체 간의 클래스 파생 관계를 포함 하 여 변환 개체와이 개체가 참조 하는 개체를 보여 주는 다이어그램입니다.](./media/api-diagrams/transform-small.png)](./media/api-diagrams/transform-large.png#lightbox)

## <a name="jobs"></a>작업

**작업** 은 지정 된 입력 비디오 또는 오디오 콘텐츠에 **변환을** 적용 하는 Media Services에 대 한 실제 요청입니다. 변환을 만든 후에는 Media Services API 또는 게시된 SDK를 사용하여 작업을 제출할 수 있습니다. **Job**은 입력 비디오의 위치 및 출력 위치와 같은 정보를 지정합니다. HTTPS Url, SAS Url 또는 [자산](/rest/api/media/assets)을 사용 하 여 입력 비디오의 위치를 지정할 수 있습니다.  

### <a name="job-input-from-https"></a>HTTPS의 작업 입력

URL을 통해 콘텐츠를 이미 액세스할 수 있고 원본 파일을 Azure에 저장 하지 않아도 되는 경우 (예: S3에서 가져오기) [HTTPS의 작업 입력](job-input-from-http-how-to.md) 을 사용 합니다. 이 방법은 Azure Blob storage에 콘텐츠가 있지만 파일이 자산에 포함 될 필요가 없는 경우에도 적합 합니다. 현재이 메서드는 입력에 단일 파일만 지원 합니다.

### <a name="asset-as-job-input"></a>작업 입력으로 자산

입력 콘텐츠가 이미 자산에 있거나 콘텐츠가 로컬 파일에 저장 된 경우 [자산을 작업 입력으로](job-input-from-local-file-how-to.md) 사용 합니다. 스트리밍 또는 다운로드를 위해 입력 자산을 게시 하려는 경우 (다운로드를 위해 mp4를 게시 하 고 텍스트 또는 얼굴 검색에 대 한 음성도 수행 하려는 경우)에도 좋은 옵션입니다. 이 메서드는 다중 파일 자산을 지원 합니다 (예: 로컬로 인코드된 MBR 스트리밍 집합).

### <a name="checking-job-progress"></a>작업 진행 상황 확인

Event Grid로 이벤트를 모니터링하여 작업의 진행 상황 및 상태를 가져올 수 있습니다. 자세한 내용은 [Event Grid를 사용하여 이벤트 모니터링](job-state-events-cli-how-to.md)을 참조하세요.

### <a name="updating-jobs"></a>작업 업데이트

[작업 엔터티에 대](/rest/api/media/jobs) 한 업데이트 작업을 사용 하 여 작업을 제출한 후 *설명* 및 *우선 순위* 속성을 수정할 수 있습니다. *우선 순위* 속성에 대한 변경은 작업이 아직 큐에 대기 중인 상태에만 유효합니다. 작업 처리가 시작되었거나 완료된 경우 우선 순위를 변경해도 아무 효과가 없습니다.

### <a name="job-object-diagram"></a>작업 개체 다이어그램

다음 다이어그램에서는 **작업** 개체와이 개체가 참조 하는 개체 (파생 관계 포함)를 보여 줍니다.

전체 크기로 보려면 이미지를 클릭합니다.  

[![개체 간의 클래스 파생 관계를 포함 하 여 작업 개체와이 개체가 참조 하는 개체를 보여 주는 다이어그램입니다.](./media/api-diagrams/job-small.png)](./media/api-diagrams/job-large.png#lightbox)

## <a name="configure-media-reserved-units"></a>미디어 예약 단위 구성

Media Services v3 또는 Video Indexer에 의해 트리거되는 오디오 분석 및 비디오 분석 작업의 경우 10 개의 S3 미디어 예약 단위 (Mru)를 사용 하 여 계정을 프로 비전 하는 것이 좋습니다. 10개가 넘는 S3 MRU가 필요한 경우 [Azure Portal](https://portal.azure.com/)을 사용하여 지원 티켓을 엽니다.

자세한 내용은 [CLI를 사용하여 미디어 처리 크기 조정](media-reserved-units-cli-how-to.md)을 참조하세요.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="see-also"></a>참고 항목

* [오류 코드](/rest/api/media/jobs/get#joberrorcode)
* [Media Services 엔터티 필터링, 순서 지정, 페이징](entities-overview.md)

## <a name="next-steps"></a>다음 단계

- 개발을 시작 하기 전에 [Media Services V3 api를 사용 하 여 개발](media-services-apis-overview.md) (api에 액세스 하는 방법, 명명 규칙 등)을 검토 합니다.
- 다음 자습서를 확인 하세요.

    - [자습서: URL을 기반으로 원격 파일 인코딩 및 비디오 스트림](stream-files-tutorial-with-rest.md)
    - [자습서: 비디오 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
    - [자습서: Media Services v3으로 비디오 분석](analyze-videos-tutorial-with-api.md)
