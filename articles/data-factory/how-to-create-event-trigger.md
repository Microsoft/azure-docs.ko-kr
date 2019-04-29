---
title: Azure Data Factory에서 이벤트 기반 트리거 만들기 | Microsoft Docs
description: Azure Data Factory에서 이벤트에 대한 응답으로 파이프라인을 실행하는 트리거를 만드는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 94c9c3f997143d72262c1ba3d8dbfea90d6f920c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347724"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-an-event"></a>이벤트에 대한 응답으로 파이프라인을 실행하는 트리거 만들기

이 문서에서는 Data Factory 파이프라인에서 만들 수 있는 이벤트 기반 트리거를 설명합니다.

EDA(이벤트 기반 아키텍처)는 프로덕션, 검색, 소비 및 이벤트에 대한 응답이 포함된 일반적인 데이터 통합 패턴입니다. 데이터 통합 시나리오에서는 Data Factory 고객이 이벤트를 기반으로 파이프라인을 트리거해야 하는 경우가 종종 있습니다. Data Factory가 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)와 통합되어 이벤트에서 파이프 라인을 트리거 할 수 있습니다.

10분 동안 이 기능의 소개 및 데모에 대한 다음 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Event-based-data-integration-with-Azure-Data-Factory/player]


> [!NOTE]
> 이 문서에서 설명하는 통합은 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)에 따라 달라집니다. 구독이 Event Grid 리소스 공급자에 등록되어 있는지 확인합니다. 자세한 내용은 [리소스 공급자 및 형식](../azure-resource-manager/resource-manager-supported-services.md#azure-portal)을 참조하세요.

## <a name="data-factory-ui"></a>Data Factory UI

### <a name="create-a-new-event-trigger"></a>새 이벤트 트리거 만들기

일반적인 이벤트는 Azure Storage 계정에 파일이 도착하거나 파일을 삭제하는 것입니다. Data Factory 파이프라인에서 이런 이벤트에 응답하는 트리거를 만들 수 있습니다.

> [!NOTE]
> 이 통합은 버전 2 저장소 계정(범용)만 지원합니다.

![새 이벤트 트리거 만들기](media/how-to-create-event-trigger/event-based-trigger-image1.png)

### <a name="configure-the-event-trigger"></a>이벤트 트리거 구성

**Blob path begins with**(Blob 경로 시작 문자) 및 **Blob path ends with**(Blob 경로 마지막 문자) 속성을 통해 이벤트를 수신할 컨테이너, 폴더 및 Blob 이름을 지정할 수 있습니다. **Blob path begins with**(Blob 경로 시작 문자) 및 **Blob path ends with**(Blob 경로 마지막 문자) 속성 모두에 대해 이 문서의 뒷부분에 나오는 예제와 같이 다양한 패턴을 사용할 수 있습니다. 이러한 속성 중 하나 이상이 필요합니다.

![이벤트 트리거 구성](media/how-to-create-event-trigger/event-based-trigger-image2.png)

### <a name="select-the-event-trigger-type"></a>이벤트 트리거 유형 선택

파일이 저장소 위치에 도착하고 해당 Blob이 만들어지는 즉시 이 이벤트는 Data Factory 파이프라인을 트리거하여 실행합니다. Blob 생성 이벤트, Blob 삭제 이벤트 또는 두 이벤트 모두에 응답하는 트리거를 Data Factory 파이프라인에 만들 수 있습니다.

![트리거 유형을 이벤트로 선택](media/how-to-create-event-trigger/event-based-trigger-image3.png)

### <a name="map-trigger-properties-to-pipeline-parameters"></a>트리거 속성을 파이프라인 매개 변수에 매핑

특정 Blob에 대해 이벤트 트리거가 실행되면 이벤트는 Blob의 폴더 경로와 파일 이름을 `@triggerBody().folderPath` 및 `@triggerBody().fileName` 속성에 캡처합니다. 파이프라인에서 이러한 속성 값을 사용하려면 속성을 파이프라인 매개 변수에 매핑해야 합니다. 속성을 매개 변수에 매핑한 후 파이프라인 전체에서 `@pipeline().parameters.parameterName` 식을 통해 트리거가 캡처한 값에 액세스할 수 있습니다.

![속성을 파이프라인 매개 변수에 매핑](media/how-to-create-event-trigger/event-based-trigger-image4.png)

예를 들어, 앞의 스크린샷에서 트리거는 `.csv`로 끝나는 Blob 경로가 저장소 계정에 생성될 때 실행되도록 구성됩니다. 따라서 확장명이 `.csv`인 Blob이 저장소 계정의 어디서든 만들어지면 `folderPath` 및 `fileName` 속성이 새 Blob의 위치를 캡처합니다. 예를 들어, `@triggerBody().folderPath`에는 `/containername/foldername/nestedfoldername` 등의 값이 있고, `@triggerBody().fileName`에는 `filename.csv` 등의 값이 있습니다. 예제에서 이러한 값은 파이프라인 매개 변수 `sourceFolder` 및 `sourceFile`에 매핑됩니다. 파이프라인 전체에서 해당 값을 각각 `@pipeline().parameters.sourceFolder` 및 `@pipeline().parameters.sourceFile`로 사용할 수 있습니다.

## <a name="json-schema"></a>JSON 스키마

다음 표에는 이벤트 기반 트리거와 관련된 스키마 요소의 개요가 제공됩니다.

| **JSON 요소** | **설명** | **형식** | **허용되는 값** | **필수** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | 저장소 계정의 Azure Resource Manager 리소스 ID입니다. | String | Azure Resource Manager ID | 예 |
| **events** | 이 트리거를 발생시키는 이벤트 유형입니다. | 배열    | Microsoft.Storage.BlobCreated, Microsoft.Storage.BlobDeleted | 예, 이러한 값의 조합입니다. |
| **blobPathBeginsWith** | Blob 경로는 발생시킬 트리거에 제공된 패턴으로 시작해야 합니다. 예를 들어 `/records/blobs/december/`는 `records` 컨테이너 아래의 `december` 폴더에서 Blob에 대한 트리거만을 시작합니다. | String   | | 이러한 속성 중 하나 이상에 대한 값을 제공해야 합니다. `blobPathBeginsWith` 또는 `blobPathEndsWith` |
| **blobPathEndsWith** | Blob 경로는 발생시킬 트리거에 제공된 패턴으로 끝나야 합니다. 예를 들어 `december/boxes.csv`는 `december` 폴더에서 `boxes`라는 이름의 Blob에 대한 트리거만을 시작합니다. | String   | | 이러한 속성 중 하나 이상에 대한 값을 제공해야 합니다. `blobPathBeginsWith` 또는 `blobPathEndsWith` |

## <a name="examples-of-event-based-triggers"></a>이벤트 기반 트리거의 예

이 섹션에는 이벤트 기반 트리거 설정의 예가 제공됩니다.

> [!IMPORTANT]
> 다음 예제에 표시된 대로 컨테이너 및 폴더, 컨테이너 및 파일, 컨테이너, 폴더 및 파일을 지정할 때마다 경로의 `/blobs/` 세그먼트를 포함해야 합니다.

| 자산 | 예 | 설명 |
|---|---|---|
| **다음으로 Blob 경로 시작** | `/containername/` | 컨테이너에서 모든 Blob에 대한 이벤트를 받습니다. |
| **다음으로 Blob 경로 시작** | `/containername/blobs/foldername/` | `containername` 컨테이너 및 `foldername` 폴더에서 모든 Blob에 대한 이벤트를 받습니다. |
| **다음으로 Blob 경로 시작** | `/containername/blobs/foldername/subfoldername/` | 하위 폴더를 참조할 수도 있습니다. |
| **다음으로 Blob 경로 시작** | `/containername/blobs/foldername/file.txt` | `containername` 컨테이너 아래의 `foldername` 폴더에서 `file.txt`라는 Blob에 대한 이벤트를 받습니다. |
| **다음으로 끝나는 Blob 경로** | `file.txt` | 모든 경로에서 `file.txt`라는 Blob에 대한 이벤트를 받습니다. |
| **다음으로 끝나는 Blob 경로** | `/containername/blobs/file.txt` | `containername` 컨테이너 아래에서 `file.txt`라는 이름의 Blob에 대한 이벤트를 받습니다. |
| **다음으로 끝나는 Blob 경로** | `foldername/file.txt` | 모든 컨테이너 아래의 `foldername` 폴더에서 `file.txt`라는 이름의 Blob에 대한 이벤트를 받습니다. |

## <a name="next-steps"></a>다음 단계
트리거에 대한 자세한 내용은 [파이프라인 실행 및 트리거](concepts-pipeline-execution-triggers.md#triggers)를 참조하세요.
