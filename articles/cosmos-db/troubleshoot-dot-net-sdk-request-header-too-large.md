---
title: Azure Cosmos DB에서 "요청 헤더 너무 큼" 메시지 또는 400 잘못 된 요청 문제 해결
description: 요청 헤더를 진단 하 고 수정 하는 방법에 대해 알아봅니다.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: d5d66ca05390af5f6fef91ca959f1db3d547d3fb
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89014050"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>"요청 헤더 너무 큼" 메시지 Azure Cosmos DB 진단 및 문제 해결
HTTP 오류 코드 400를 사용 하 여 "요청 헤더가 너무 큼" 메시지가 throw 됩니다. 이 오류는 요청 헤더의 크기가 최대 허용 크기를 초과 하 여 증가 하는 경우에 발생 합니다. 최신 버전의 SDK를 사용 하는 것이 좋습니다. 이러한 버전은 예외 메시지에 헤더 크기 추적을 추가 하기 때문에 버전 3(sp3) 또는 2.x 이상을 사용 합니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
세션 또는 연속 토큰이 너무 클 경우 "요청 헤더 너무 큼" 메시지가 발생 합니다. 다음 섹션에서는 각 범주의 문제의 원인과 해결 방법을 설명 합니다.

### <a name="session-token-is-too-large"></a>세션 토큰이 너무 큼

#### <a name="cause"></a>원인:
400 잘못 된 요청은 세션 토큰이 너무 커서 발생 가능성이 높습니다. 다음 문이 true 이면 세션 토큰이 너무 깁니다.

* 이 오류는 연속 토큰이 없는 경우 만들기, 읽기 및 업데이트와 같은 지점 작업에서 발생 합니다.
* 응용 프로그램을 변경 하지 않고 예외를 시작 했습니다. 세션 토큰은 컨테이너에서 파티션 수가 증가 함에 따라 증가 합니다. 파티션 수는 데이터 양이 증가 하거나 처리량이 증가 함에 따라 증가 합니다.

#### <a name="temporary-mitigation"></a>임시 완화: 
클라이언트 응용 프로그램을 다시 시작 하 여 모든 세션 토큰을 다시 설정 합니다. 결국 세션 토큰은 문제를 일으킨 이전 크기로 다시 증가 하 게 됩니다. 이 문제를 완전히 방지 하려면 다음 섹션의 솔루션을 사용 합니다.

#### <a name="solution"></a>해결 방법:
1. [.Net v3](performance-tips-dotnet-sdk-v3-sql.md) 또는 [.net v2](performance-tips.md) 성능 팁 문서의 지침을 따릅니다. TCP (전송 제어 프로토콜)를 사용 하 여 직접 연결 모드를 사용 하도록 응용 프로그램을 변환 합니다. TCP 프로토콜을 사용 하는 직접 연결 모드에는 HTTP 프로토콜과 같은 헤더 크기 제한이 없으므로이 문제를 방지할 수 있습니다. 서비스 interop를 사용할 수 없는 경우 쿼리 작업에 대 한 수정이 포함 된 최신 버전의 SDK를 사용 해야 합니다.
1. TCP 프로토콜을 사용 하는 직접 연결 모드가 워크 로드에 대 한 옵션이 아닌 경우 [클라이언트 일관성 수준을](how-to-manage-consistency.md)변경 하 여이를 완화 합니다. 세션 토큰은 Azure Cosmos DB의 기본 일관성 수준인 세션 일관성에만 사용 됩니다. 다른 일관성 수준은 세션 토큰을 사용 하지 않습니다.

### <a name="continuation-token-is-too-large"></a>연속 토큰이 너무 큼

#### <a name="cause"></a>원인:
연속 토큰이 너무 크거나 쿼리 연속 토큰 크기가 다른 경우 연속 토큰이 사용 되는 쿼리 작업에서 400 잘못 된 요청이 발생 합니다.
    
#### <a name="solution"></a>해결 방법:
1. [.Net v3](performance-tips-dotnet-sdk-v3-sql.md) 또는 [.net v2](performance-tips.md) 성능 팁 문서의 지침을 따릅니다. TCP 프로토콜을 사용 하 여 직접 연결 모드를 사용 하도록 응용 프로그램을 변환 합니다. TCP 프로토콜을 사용 하는 직접 연결 모드에는 HTTP 프로토콜과 같은 헤더 크기 제한이 없으므로이 문제를 방지할 수 있습니다. 
1. TCP 프로토콜을 사용 하는 직접 연결 모드가 워크 로드에 대 한 옵션이 아닌 경우 옵션을 설정 `ResponseContinuationTokenLimitInKb` 합니다. 이 옵션은 v2 또는 v3의에서 찾을 수 있습니다 `FeedOptions` `QueryRequestOptions` .

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용 하는 경우 문제를 [진단 하 고 해결](troubleshoot-dot-net-sdk.md) 합니다.
* [.Net v3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.net v2](performance-tips.md)의 성능 지침에 대해 알아봅니다.
