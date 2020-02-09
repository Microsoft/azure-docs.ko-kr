---
title: Azure Media Services API 액세스 - Azure CLI | Microsoft Docs
description: 이 방법 문서의 단계에 따라 Azure Media Services API에 액세스 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 06ca26ee9a8af7f49aba6a48a1831d30d5886851
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896337"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Azure CLI를 사용하여 Azure Media Services API 액세스
 
Azure AD 서비스 주체 인증을 사용 하 여 Azure Media Services API에 연결 하려면 응용 프로그램에서 다음 매개 변수가 있는 Azure AD 토큰을 요청 해야 합니다.

* Azure AD 테넌트 엔드포인트
* Media Services 리소스 URI
* REST Media Services의 리소스 URI
* Azure AD 애플리케이션 값: 클라이언트 ID 및 클라이언트 암호.

> [!IMPORTANT]
> 자세한 설명은 [Media Services V3 Api에 액세스](media-services-apis-overview.md#accessing-the-azure-media-services-api)를 검토 합니다.

이 문서에서는 Azure CLI를 사용하여 Azure AD 애플리케이션과 서비스 주체를 만들고 Azure Media Services 리소스에 액세스하는 데 필요한 값을 얻는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건 

[Media Services 계정 만들기](create-account-cli-how-to.md)

리소스 그룹 이름 및 Media Services 계정 이름에 사용한 값을 기억해 두세요.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>참고 항목

- [미디어 예약 단위 크기 조정 - CLI](media-reserved-units-cli-how-to.md)
- [Media Services 계정 만들기 - CLI](create-account-cli-how-to.md) 
- [계정 자격 증명 다시 설정 - CLI](cli-reset-account-credentials.md)
- [자산 만들기 - CLI](cli-create-asset.md)
- [파일 업로드 - CLI](cli-upload-file-asset.md)
- [변환 만들기 - CLI](cli-create-transform.md)
- [사용자 지정 변환을 사용 하 여 인코딩-CLI](custom-preset-cli-howto.md)
- [작업 만들기 - CLI](cli-create-jobs.md)
- [EventGrid 만들기 - CLI](job-state-events-cli-how-to.md)
- [자산 게시 - CLI](cli-publish-asset.md)
- [필터링 - CLI](filters-dynamic-manifest-cli-howto.md)
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>다음 단계

콘텐츠를 스트림 하려는 스트리밍 엔드포인트이 실행 상태에 있어야 합니다. 다음 CLI 명령은 기본 스트리밍 엔드포인트을 시작 합니다.

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

