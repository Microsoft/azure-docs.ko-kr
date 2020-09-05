---
title: 미디어 업로드
titleSuffix: Azure Media Services
description: 스트리밍 또는 인코딩에 미디어를 업로드하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0bdb2c36bc895c9229e4c04e9e0d76aa852bd139
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297309"
---
# <a name="upload-media-for-streaming-or-encoding"></a>스트리밍 또는 인코딩에 미디어 업로드

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services에서 자산에 연결된 Blob 컨테이너에 디지털 파일(미디어)을 업로드합니다. [자산](/rest/api/media/operations/asset) 엔터티에는 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 자막 파일(및 이러한 파일에 대한 메타데이터)이 포함될 수 있습니다. 자산의 컨테이너에 파일이 업로드되면 이후 처리 및 스트리밍을 위해 콘텐츠가 클라우드에 안전하게 저장됩니다.

시작하기 전에 몇 가지 값을 수집하거나 고려해야 합니다.

1. 업로드할 파일의 로컬 파일 경로
1. 자산의 자산 ID(컨테이너)
1. 확장자를 포함하여 업로드된 파일에 사용할 이름
1. 사용하는 스토리지 계정의 이름
1. 스토리지 계정의 액세스 키

## <a name="portal"></a>[포털](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli.md)]

## <a name="rest"></a>[REST (영문)](#tab/rest/)

[Postman 또는 기타 REST 메서드를 사용하여 자산을 생성하고 자산에 대한 SAS URL을 얻었으면](how-to-create-asset.md?tabs=rest) Azure Storage API 또는 SDK(예: [Storage REST API](../../storage/common/storage-rest-api-auth.md) 또는 [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md))를 사용합니다.

---
<!-- add these to the tabs when available -->
다른 메서드에 대한 자세한 내용은 [.NET](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet), [Java](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-java), [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python) 및 [JavaScript(node.js)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-nodejs)에서 Blob 관련 작업에 대한 [Azure Storage 설명서](https://docs.microsoft.com/azure/storage/blobs/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [Media Services 개요](media-services-overview.md)
