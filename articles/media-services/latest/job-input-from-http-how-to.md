---
title: HTTPS URL에서 Azure Media Services 작업 입력 만들기 | Microsoft Docs
description: 이 항목에서는 HTTPS URL에서 Azure Media Services 작업 입력을 만드는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c6ab5051b436d12ca86501e4c71bbbea0a8d3107
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269066"
---
# <a name="create-a-job-input-from-an-https-url"></a>HTTPS URL에서 작업 입력 만들기

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services v3에서는, 비디오를 처리할 작업을 제출할 때 입력 비디오를 찾을 위치를 Media Services에 알려줘야 합니다. 옵션 중 하나는 다음 예제와 같이 HTTPS URL을 작업 입력으로 지정하는 것입니다. 현재 AMS v3은 HTTPS URL을 통한 청크 분할 전송 인코딩을 지원하지 않습니다. 전체 예제를 보려면 [GitHub 샘플](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)을 참조하세요.

> [!TIP]
> 개발을 시작 하기 전에 [Media Services V3 api를 사용 하 여 개발](media-services-apis-overview.md) (api에 액세스 하는 방법, 명명 규칙 등)을 검토 합니다.

## <a name="net-sample"></a>.NET 샘플

다음 코드에서는 HTTPS URL 입력으로 작업을 만드는 방법을 보여줍니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>작업 오류 코드

[오류 코드](/rest/api/media/jobs/get#joberrorcode)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[로컬 파일에서 작업 입력을 만듭니다](job-input-from-local-file-how-to.md).
