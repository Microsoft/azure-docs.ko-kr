---
title: .NET SDK에서 세션 토큰 형식을 변환 하는 방법-Azure Cosmos DB
description: 서로 다른 .NET SDK 버전 간에 호환성 되도록 세션 토큰 형식을 변환 하는 방법에 대해 알아봅니다.
author: vinhms
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/30/2020
ms.author: vitrinh
ms.custom: devx-track-csharp
ms.openlocfilehash: 787c39681d0e9aff25d205c7b195be00b8c0bc9c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020017"
---
# <a name="convert-session-token-formats-in-net-sdk"></a>.NET SDK에서 세션 토큰 형식 변환

이 문서에서는 서로 다른 세션 토큰 형식 간에 변환 하 여 SDK 버전 간의 호환성을 유지 하는 방법을 설명 합니다.

> [!NOTE]
> 기본적으로 SDK는 세션 토큰을 자동으로 추적 하 고 가장 최근의 세션 토큰을 사용 합니다.  자세한 내용은 [세션 토큰 활용](how-to-manage-consistency.md#utilize-session-tokens)을 참조 하세요. 이 문서의 지침은 다음 조건에만 적용 됩니다.
> * Azure Cosmos DB 계정에서 세션 일관성을 사용 합니다.
> * 수동으로 세션 토큰을 관리 하 고 있습니다.
> * 동시에 여러 버전의 SDK를 사용 하 고 있습니다.

## <a name="session-token-formats"></a>세션 토큰 형식

세션 토큰 형식에는 **단순** 및 **벡터**의 두 가지가 있습니다.  이러한 두 가지 형식은 서로 바꿔 사용할 수 없으므로 다른 버전의 클라이언트 응용 프로그램으로 전달할 때 형식을 변환 해야 합니다.
- **단순** 세션 토큰 형식은 .Net SDK V1 (Microsoft.Azure.DocumentDB-버전 1.x)에서 사용 됩니다.
- **벡터** 세션 토큰 형식은 .Net SDK V2 (Microsoft.Azure.DocumentDB-버전 2.x)에서 사용 됩니다.

### <a name="simple-session-token"></a>단순 세션 토큰

단순 세션 토큰의 형식은 다음과 같습니다. `{pkrangeid}:{globalLSN}`

### <a name="vector-session-token"></a>벡터 세션 토큰

벡터 세션 토큰의 형식은 다음과 같습니다. `{pkrangeid}:{Version}#{GlobalLSN}#{RegionId1}={LocalLsn1}#{RegionId2}={LocalLsn2}....#{RegionIdN}={LocalLsnN}`

## <a name="convert-to-simple-session-token"></a>단순 세션 토큰으로 변환

.NET SDK V1을 사용 하 여 세션 토큰을 클라이언트에 전달 하려면 **간단한** 세션 토큰 형식을 사용 합니다.  예를 들어 다음 샘플 코드를 사용 하 여 변환 합니다.

```csharp
private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SessionTokenHelpers.SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInSimpleFormat;

if (sessionTokenSegments.Length == 1)
{
    // returning the same token since it already has the correct format
    sessionTokenInSimpleFormat = sessionTokenToConvert;
}
else
{
    long version = 0;
    long globalLSN = 0;

    if (!long.TryParse(sessionTokenSegments[0], out version)
        || !long.TryParse(sessionTokenSegments[1], out globalLSN))
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }

    sessionTokenInSimpleFormat = string.Format("{0}:{1}", items[0], globalLSN);
}
```

## <a name="convert-to-vector-session-token"></a>벡터 세션 토큰으로 변환

.NET SDK V2를 사용 하 여 세션 토큰을 클라이언트에 전달 하려면 **vector** 세션 토큰 형식을 사용 합니다.  예를 들어 다음 샘플 코드를 사용 하 여 변환 합니다.

```csharp

private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInVectorFormat;

if (sessionTokenSegments.Length == 1)
{
    long globalLSN = 0;
    if (long.TryParse(sessionTokenSegments[0], out globalLSN))
    {
        sessionTokenInVectorFormat = string.Format("{0}:-2#{1}", items[0], globalLSN);
    }
    else
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }
}
else
{
    // returning the same token since it already has the correct format
    sessionTokenInVectorFormat = sessionTokenToConvert;
}
```

## <a name="next-steps"></a>다음 단계

다음 문서를 읽어보세요.

* [Azure Cosmos DB에서 세션 토큰을 사용하여 일관성 관리](how-to-manage-consistency.md#utilize-session-tokens)
* [Azure Cosmos DB에서 올바른 일관성 수준 선택](consistency-levels-choosing.md)
* [Azure Cosmos DB의 일관성, 가용성 및 성능 절충](consistency-levels-tradeoffs.md)
* [다양한 일관성 수준의 가용성 및 성능 절충](consistency-levels-tradeoffs.md)
