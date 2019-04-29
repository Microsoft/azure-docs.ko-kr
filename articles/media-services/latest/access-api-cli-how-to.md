---
title: Azure Media Services API 액세스 - Azure CLI | Microsoft Docs
description: Azure Media Services API에 액세스하려면 이 방법의 단계를 따르도록 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 8374e4c49012a2c49de41001be0fdb30f9151332
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733507"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Azure CLI를 사용하여 Azure Media Services API 액세스
 
Azure Media Services API에 연결 하려면 Azure AD 서비스 주체 인증을 사용 하려면 응용 프로그램에 다음 매개 변수가 있는 Azure AD 토큰을 요청 해야 합니다.

* Azure AD 테넌트 엔드포인트
* Media Services 리소스 URI
* REST Media Services의 리소스 URI
* Azure AD 애플리케이션 값: 클라이언트 ID 및 클라이언트 암호.

에 대 한 자세한 설명은 [에 액세스 하는 Media Services v3 Api](media-services-apis-overview.md#accessing-the-azure-media-services-api)합니다.

이 문서에서는 Azure CLI를 사용하여 Azure AD 애플리케이션과 서비스 주체를 만들고 Azure Media Services 리소스에 액세스하는 데 필요한 값을 얻는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건 

[Media Services 계정 만들기](create-account-cli-how-to.md)

리소스 그룹 이름 및 Media Services 계정 이름에 사용한 값을 기억해 두세요.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>참고 항목

- [미디어 예약 단위 크기 조정 - CLI](media-reserved-units-cli-how-to.md)
- [Media Services 계정 만들기 - CLI](./scripts/cli-create-account.md) 
- [계정 자격 증명 다시 설정 - CLI](./scripts/cli-reset-account-credentials.md)
- [자산 만들기 - CLI](./scripts/cli-create-asset.md)
- [파일 업로드 - CLI](./scripts/cli-upload-file-asset.md)
- [변환 만들기 - CLI](./scripts/cli-create-transform.md)
- [작업 만들기 - CLI](./scripts/cli-create-jobs.md)
- [EventGrid 만들기 - CLI](./scripts/cli-create-event-grid.md)
- [자산 게시 - CLI](./scripts/cli-publish-asset.md)
- [필터링 - CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="next-steps"></a>다음 단계

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
