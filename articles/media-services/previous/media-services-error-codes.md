---
title: Azure Media Services 오류 코드 | Microsoft 문서
description: 이 항목에서는 Azure Media Services 오류 코드에 대한 개요를 제공합니다.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: f3c362730e7908e88b363659b7fa580b6f2cddf1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61217236"
---
# <a name="azure-media-services-error-codes"></a>Azure Media Services 오류 코드
Microsoft Azure Media Services를 사용할 경우 Media Services에서 지원되지 않는 작업에 대한 인증 토큰 만료와 같은 문제에 따라 서비스에서 HTTP 오류 코드를 받을 수도 있습니다. 다음은 Media Services에서 반환되는 **HTTP 오류 코드** 및 가능한 원인의 목록입니다.  

## <a name="400-bad-request"></a>400 잘못된 요청
요청에 잘못된 정보가 들어 있으며 다음 이유 중 하나 때문에 요청이 거부됩니다.

* 지원되지 않는 API 버전이 지정되었습니다. 가장 최신 버전은 [Media Services REST API 개발 설정](media-services-rest-how-to-use.md)을 참조하세요.
* Media Services의 API 버전이 지정되지 않았습니다. API 버전을 지정하는 방법에 대한 정보는 [Media Services Operations REST API 참조](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)을 참조하세요.
  
  > [!NOTE]
  > .NET 또는 Java SDK를 사용하여 Media Services에 연결하는 경우, Media Services에 대해 작업을 수행하려고 할 때마다 API 버전이 지정됩니다.
  > 
  > 
* 정의되지 않은 속성이 지정되었습니다. 속성 이름이 오류 메시지에 있습니다. 지정된 엔터티의 구성 요소인 속성 만이 지정될 수 있습니다. 엔터티 및 해당 속성의 목록은 [Azure Media Services REST API 참조](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)를 참조하세요.
* 잘못된 속성 값이 지정되었습니다. 속성 이름이 오류 메시지에 있습니다. 유효한 속성 유형 및 해당 값에 대한 이전 링크를 참조하세요.
* 필수 속성 값이 누락되었습니다.
* 지정된 URL 일부에 잘못된 값을 들어 있습니다.
* WriteOnce 속성을 업데이트하려는 시도가 있었습니다.
* 지정되지 않았거나 확인할 수 없는 기본 AssetFile을 사용해 입력 자신이 있는 작업을 만들려는 시도가 있었습니다.
* SAS Locator를 업데이트하려는 시도가 있었습니다. SAS Locator는 생성 또는 삭제만 될 수 있습니다. 스트리밍 로케이터는 업데이트될 수 있습니다. 자세한 내용은 [Locators](https://docs.microsoft.com/rest/api/media/operations/locator)를 참조하세요.
* 지원되지 않는 작업 또는 쿼리가 제출되었습니다.

## <a name="401-unauthorized"></a>401 권한 없음
요청이 다음과 같은 이유로 (권한이 부여될 수 있기 전에) 인증될 수 없습니다.

* 누락된 인증 헤더입니다.
* 잘못된 인증 헤더 값입니다.
  * 토큰이 만료되었습니다. 
  * 토큰에 잘못된 서명이 들어 있습니다.

## <a name="403-forbidden"></a>403 사용할 수 없음
요청은 다음과 같은 이유로 허용되지않습니다.

* Media Services 계정을 찾을 수 없거나 삭제되었습니다.
* Media Services 계정은 비활성화이고 요청 유형은 HTTP GET이 아닙니다. 서비스 작업은 또한 403 응답을 반환합니다.
* 인증 토큰에는 사용자의 자격 증명 정보인 AccountName 및/또는 SubscriptionId가 들어 있지 않습니다. Azure Management Portal의 Media Services 계정에 대한 Media Services UI 확장에서 이 정보를 찾을 수 있습니다.
* 리소스를 액세스할 수 없습니다.
  
  * 사용자 Media Services 계정에서는 사용할 수 없는 MediaProcessor를 사용하려는 시도가 있었습니다.
  * Media Services에서 정의한 JobTemplate을 업데이트하려는 시도가 있었습니다.
  * 일부 다른 Media Services 계정의 로케이터를 덮어쓰기하려는 시도가 있었습니다.
  * 일부 다른 Media Services 계정의 ContentKey를 덮어쓰기하려는 시도가 있었습니다.
* Media Services 계정에 대한 서비스 할당량에 도달했기 때문에 리소스를 만들 수 없습니다. 서비스 할당량에 관한 자세한 내용은 [할당량 및 제한 사항](media-services-quotas-and-limitations.md)을 참조하세요.

## <a name="404-not-found"></a>404 찾을 수 없음
리소스에 관한 요청은 다음과 같은 이유로 허용되지않습니다.

* 존재하지 않는 엔터티를 업데이트하려고 시도가 있었습니다.
* 존재하지 않는 엔터티를 삭제하려는 시도가 있었습니다.
* 존재하지 않는 엔터티에 연결하는 엔터티를 만들려는 시도가 있었습니다.
* 존재하지 않는 엔터티를 GET하려는 시도가 있었습니다.
* Media Services 계정과 연결되지 않은 저장소 계정을 지정하려는 시도가 있었습니다.  

## <a name="409-conflict"></a>409 충돌
요청은 다음과 같은 이유로 허용되지않습니다.

* 둘 이상의 AssetFile에 Asset 내에서 지정된 이름이 있습니다.
* Asset 내에서 두 번째 보조 AssetFile를 만들려는 시도가 있었습니다.
* 이미 사용된 지정 Id를 사용하여 ContentKey를 만들려는 시도가 있었습니다.
* 이미 사용된 지정 Id를 사용하여 Locator를 만들려는 시도가 있었습니다.
* 둘 이상의 IngestManifestFile에 IngestManifest 내에서 지정된 이름이 있습니다.
* 두 번째 저장소 암호화 ContentKey를 저장소 암호화된 Asset에 연결하려는 시도가 있었습니다.
* 동일한 ContentKey를 Asset에 연결하려는 시도가 있었습니다.
* 저장소 컨테이너가 누락되었거나 더 이상 해당 Asset과 연결되지 않은 Asset에 로케이터를 만들려는 시도가 있었습니다.
* 사용 중인 로케이터가 5개 있는 Asset에 로케이터를 만들려는 시도가 있었습니다. (Azure Storage는 한 개의 스토리지 컨테이너에 다섯 개의 공유 액세스 정책이란 제한을 적용합니다.)
* Asset을 IngestManifestAsset에 연결하는 저장소 계정은 상위 IngestManifest에서 사용되는 저장소 계정과는 다릅니다.  

## <a name="500-internal-server-error"></a>500 내부 서버 오류
요청을 처리하는 동안 Media Services가 처리를 계속 실행하는 것을 막는 오류가 발생합니다. 다음 이유 중 하나 때문일 수 있습니다.

* Media Services 계정의 서비스 할당량 정보를 일시적으로 사용할 수 없기 때문에 Asset 또는 Job을 만들 수 없습니다.
* Azure File Storage 일반적으로 사용 가능(영문)
* 기타 예기치 않은 오류.

## <a name="503-service-unavailable"></a>503 서비스를 사용할 수 없음
서버는 현재 요청을 받을 수 없습니다. 이 오류는 서비스에 요청을 과도하게 해서 발생할 수 있습니다. Media Services 제한 메커니즘은 서비스에 과도한 요청을 보내는 애플리케이션의 리소스 사용을 제한합니다.

> [!NOTE]
> 503 오류가 발생하는 이유에 관한 자세한 정보를 얻기 위해 오류 메시지 및 오류 코드 문자열을 확인합니다. 이 오류가 항상 제한을 의미하지는 않습니다.
> 
> 

가능한 상태 설명은 다음과 같습니다.

* "서버가 사용 중입니다. 이 유형의 요청은 이전에 실행되었을 때 {0}초 이상 걸렸습니다."
* "서버가 사용 중입니다. 초당 요청이 {0}개보다 많을 경우 제한될 수 있습니다."
* "서버가 사용 중입니다. {1}초 내에 요청이 {0}개보다 많을 경우 제한될 수 있습니다."

이 오류를 처리하려면 지수적 백오프 재시도 논리를 사용 하는 것이 좋습니다. 연속된 오류 응답에 대해 재시도 간에 점진적으로 더 긴 대기 시간을 두는 것을 의미합니다.  자세한 내용은 [일시적인 오류 처리 애플리케이션 블록(영문)](https://msdn.microsoft.com/library/hh680905.aspx)을 참조하세요.

> [!NOTE]
> [.Net용 Azure Media Services SDK](https://github.com/Azure/azure-sdk-for-media-services/tree/master)를 사용 중인 경우 503 오류에 대한 재시도 논리는 SDK로 구현되었습니다.  
> 
> 

## <a name="see-also"></a>관련 항목
[Media Services 관리 오류 코드](https://msdn.microsoft.com/library/windowsazure/dn167016.aspx)

## <a name="next-steps"></a>다음 단계
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

