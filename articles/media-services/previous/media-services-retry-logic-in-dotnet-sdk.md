---
title: .NET용 Media Services SDK의 재시도 논리 | Microsoft Docs
description: 이 항목에서는 NET용 Media Services SDK의 재시도 논리에 대한 개요를 제공합니다.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 63715f668438519131eba5bfff7aa38fc73267d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094660"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>.NET용 Media Services SDK의 다시 시도 논리  

Microsoft Azure 서비스에서 작업할 때 일시적 오류가 발생할 수 있습니다. 일시적 오류가 발생하는 대부분의 경우 몇 번 재시도하면 성공적으로 작동합니다. .NET용 Media Services SDK는 웹 요청, 쿼리 실행, 변경 저장, 저장소 작업에 의해 발생하는 예외 및 오류와 관련된 일시적 오류를 처리하기 위해 재시도 논리를 구현합니다.  기본적으로 .NET용 Media Services SDK는 애플리케이션에 예외를 다시 던져넣기 전에 재시도를 네 번 실행합니다. 그러면 애플리케이션의 코드가 이 예외를 제대로 처리해야 합니다.  

 다음은 Web Request, Storage, Query 및 SaveChanges 정책에 대한 간략한 지침입니다.  

* Storage 정책은 blob 스토리지 작업(자산 파일의 업로드 또는 다운로드)에 사용됩니다.  
* Web Request 정책은 일반적인 웹 요청(예: 인증 토큰을 가져오고 사용자 클러스터 엔드포인트를 해결하기 위함)에 사용됩니다.  
* Query 정책은 REST(예: mediaContext.Assets.Where(…))에서 엔터티를 쿼리하는 데 사용됩니다.  
* SaveChanges 정책은 서비스 내에서 데이터를 변경하는 무언가를 하는 것(예: 엔터티 생성, 엔터티 업데이트, 작업에 대한 서비스 함수 호출)에 대해 사용됩니다.  
  
  이 항목에서는 .NET용 Media Services SDK 재처리 논리에 의해 처리되는 예외 형식 및 오류 코드를 나열합니다.  

## <a name="exception-types"></a>예외 유형
다음 테이블에서는 .NET용 Media Services SDK가 일시적 오류를 일으키는 일부 작업을 처리하거나 처리하지 않는 예외를 설명합니다.  

| 예외 | 웹 요청 | Storage | 쿼리 | SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>자세한 내용은 [WebException 상태 코드](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) 섹션을 참조하세요. |예 |예 |예 |예 |
| DataServiceClientException<br/> 자세한 내용은 [HTTP 오류 상태 코드](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)를 참조하세요. |아닙니다. |예 |예 |예 |
| DataServiceQueryException<br/> 자세한 내용은 [HTTP 오류 상태 코드](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)를 참조하세요. |아닙니다. |예 |예 |예 |
| DataServiceRequestException<br/> 자세한 내용은 [HTTP 오류 상태 코드](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)를 참조하세요. |아닙니다. |예 |예 |예 |
| DataServiceTransportException |아닙니다. |아니요 |예 |예 |
| TimeoutException |예 |예 |예 |아닙니다. |
| SocketException |예 |예 |예 |예 |
| StorageException |아닙니다. |사용자 계정 컨트롤 |아니오 |아닙니다. |
| IOException |아닙니다. |사용자 계정 컨트롤 |아니오 |아닙니다. |

### <a name="WebExceptionStatus"></a> WebException 상태 코드
다음 테이블은 어떤 WebException 오류 코드에 대해 재시도 논리가 구현되었는지 보여줍니다. [WebExceptionStatus](https://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) 열거형은 상태 코드를 정의합니다.  

| 상태 | 웹 요청 | Storage | 쿼리 | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |예 |예 |예 |예 |
| NameResolutionFailure |예 |예 |예 |예 |
| ProxyNameResolutionFailure |예 |예 |예 |예 |
| SendFailure |예 |예 |예 |예 |
| PipelineFailure |예 |예 |예 |아닙니다. |
| ConnectionClosed |예 |예 |예 |아닙니다. |
| KeepAliveFailure |예 |예 |예 |아닙니다. |
| UnknownError |예 |예 |예 |아닙니다. |
| ReceiveFailure |예 |예 |예 |아닙니다. |
| RequestCanceled |예 |예 |예 |아닙니다. |
| 시간 제한 |예 |예 |예 |아닙니다. |
| ProtocolError <br/>ProtocolError 시의 재시도는 HTTP 상태 코드 처리에 의해 제어됩니다. 자세한 내용은 [HTTP 오류 상태 코드](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)를 참조하세요. |예 |예 |예 |예 |

### <a name="HTTPStatusCode"></a> HTTP 오류 상태 코드
Query 및 SaveChanges 작업에서 DataServiceClientException, DataServiceQueryException 또는 DataServiceQueryException를 던질 경우 HTTP 오류 상태 코드가 StatusCode 속성에 반환됩니다.  다음 테이블은 어떤 오류 코드에 대해 재시도 논리가 구현되었는지 보여줍니다.  

| 상태 | 웹 요청 | Storage | 쿼리 | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |아닙니다. |사용자 계정 컨트롤 |아니오 |아닙니다. |
| 403 |아닙니다. |예<br/>더 긴 대기 시간으로 재시도를 처리함. |아닙니다. |아닙니다. |
| 408 |예 |예 |예 |예 |
| 429 |예 |예 |예 |예 |
| 500 |예 |예 |예 |아닙니다. |
| 502 |예 |예 |예 |아닙니다. |
| 503 |예 |예 |예 |예 |
| 504 |예 |예 |예 |아닙니다. |

.NET용 Media Services SDK 재처리 논리의 실제 구현을 보려면 [azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling)를 참조하세요.

## <a name="next-steps"></a>다음 단계
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

