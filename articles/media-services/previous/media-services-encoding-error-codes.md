---
title: Azure Media Services 인코딩 오류 코드 | Microsoft Docs
description: 이 항목에는 인코딩 작업을 실행하는 동안 오류가 발생하는 경우 반환될 수 있는 오류 코드가 나열되어 있습니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 82a8eecb4fb211117000c94c51644ce1fd27916d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463906"
---
# <a name="encoding-error-codes"></a>Encoding 오류 코드

다음 표에서는 인코딩 작업을 실행하는 동안 오류가 발생한 경우 반환될 수 있는 오류 코드를 나열합니다.  .NET 코드에서 오류 세부 정보를 가져오려면 [ErrorDetails](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) 클래스를 사용합니다. REST 코드에서 오류 세부 정보를 가져오려면 [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) REST API를 사용합니다.

| ErrorDetail.Code | 가능한 오류 원인 |
| --- | --- |
| 알 수 없음 |작업을 실행하는 동안 알 수 없는 오류입니다. |
| ErrorDownloadingInputAssetMalformedContent |잘못된 파일 이름, 길이가 0 인 파일, 잘못된 형식 등 입력 자산을 다운로드하는 동안 발생하는 오류를 포함하는 오류 범주입니다. |
| ErrorDownloadingInputAssetServiceFailure |다운로드하는 동안 발생하는 네트워크 또는 저장소 오류 등 서비스 쪽의 문제를 포함하는 오류 범주입니다. |
| ErrorParsingConfiguration |구성이 잘못된 시스템 기본 설정이거나 잘못된 XML이 포함된 경우 등 작업 <see cref="MediaTask.PrivateData"/>(구성)가 잘못된 경우의 오류 범주입니다. |
| ErrorExecutingTaskMalformedContent |입력 미디어 파일 내의 문제로 인해 실패가 발생한 작업을 실행하는 동안 발생하는 오류 범주입니다. |
| ErrorExecutingTaskUnsupportedFormat |미디어 프로세서가 지정한 미디어 형식이 지원되지 않는 파일을 처리할 수 없거나 구성과 일치하지 않는 경우의 오류 범주입니다. 예를 들어 비디오만 사용할 수 있는 자산에서 오디오 전용 출력을 생성하려고 하는 경우 |
| ErrorProcessingTask |콘텐츠와 관련되지 않은 작업을 처리하는 동안 미디어 프로세서에서 발생하는 기타 오류 범주입니다. |
| ErrorUploadingOutputAsset |출력 자산을 업로드할 때 발생하는 오류를 포함하는 오류 범주입니다. |
| ErrorCancelingTask |작업을 취소하려고 할 때 발생하는 오류를 포함하는 오류 범주입니다. |
| TransientError |일시적인 문제를 포함하는 오류 범주(예: Azure Storage의 임시 네트워킹 문제) |

**Media Services** 팀에 도움을 요청하려면 [지원 티켓](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 엽니다.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>관련 문서
* [미디어 인코더 표준 사전 설정을 사용자 지정하여 고급 인코딩 작업 수행](media-services-custom-mes-presets-with-dotnet.md)
* [할당량 및 제한 사항](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
