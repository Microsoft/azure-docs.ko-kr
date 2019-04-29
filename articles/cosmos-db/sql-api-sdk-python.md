---
title: Azure Cosmos DB는 SQL Python API, SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB Python SDK의 각 버전 간 변경 내용을 포함하여 SQL Python API 및 SDK에 대한 모든 것을 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: 9903339cbf0958893fb0d11a8c1b6ab7d156aae7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626794"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>SQL API용 Azure Cosmos DB Python SDK: 릴리스 정보 및 리소스
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 변경 피드](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [비동기 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST (영문)](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 리소스 공급자](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK 다운로드**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API 설명서**|[Python API 참조 설명서](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**SDK 설치 지침**|[Python SDK 설치 지침](https://github.com/Azure/azure-cosmos-python)|
|**SDK에 참여**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**시작**|[Python SDK 시작](sql-api-python-application.md)|
|**현재 지원되는 플랫폼**|[Python 2.7](https://www.python.org/downloads/) 및 [Python 3.5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>릴리스 정보

### <a name="a-name302302"></a><a name="3.0.2"/>3.0.2
* MultiPolygon 데이터 형식 지원이 추가됨
* 세션 읽기 다시 시도 정책의 버그 수정
* base 64 문자열을 디코딩하는 동안 잘못된 패딩 문제에 대한 버그 수정

### <a name="a-name301301"></a><a name="3.0.1"/>3.0.1
* LocationCache의 버그 수정
* 버그 수정 엔드포인트 다시 시도 논리
* 설명서 수정됨

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0
* 다중 지역 쓰기 지원
* azure.cosmos로 변경된 네임스페이스
* 컨테이너 및 항목으로 이름이 바뀐 컬렉션 및 문서 개념, cosmos_client로 이름이 바뀐 document_client 

### <a name="a-name233233"></a><a name="2.3.3"/>2.3.3
* 프록시 지원이 추가됨
* 변경 피드 읽기 지원이 추가됨
* 컬렉션 할당량 헤더 지원이 추가됨
* 큰 세션 토큰 문제의 버그 수정
* ReadMedia API 버그 수정
* 파티션 키 범위 캐시의 버그 수정

### <a name="a-name232232"></a><a name="2.3.2"/>2.3.2
* 연결 문제에 대한 기본 재시도 지원이 추가되었습니다.

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Azure DocumentDB 대신 Azure Cosmos DB를 참조하도록 업데이트된 설명서입니다.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* 이 SDK 버전은 https://aka.ms/cosmosdb-emulator에서 다운로드할 수 있는 최신 버전의 Azure Cosmos DB 에뮬레이터가 필요합니다.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* 집계 사전에 대한 버그 수정입니다.
* 리소스 링크에서 슬래시 잘라내기에 대한 버그 수정입니다.
* 유니코드 인코딩에 대한 테스트가 추가되었습니다.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* ConsistentPrefix라는 새로운 일관성 수준에 대한 지원이 추가되었습니다.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* 집계 쿼리(COUNT, MIN, MAX, SUM 및 AVG)에 대한 지원이 추가되었습니다.
* Cosmos DB 에뮬레이터에 대해 실행하는 경우 SSL 유효성 검사를 비활성화하기 위한 옵션을 추가했습니다.
* 종속 요청 모듈이 정확히 2.10.0이어야 하는 제한을 제거했습니다.
* 분할된 컬렉션에 대한 최소 처리량이 10,100RU/s에서 2500RU/s로 감소됩니다.
* 저장된 프로시저가 실행되는 동안 스크립트 로깅을 사용할 수 있도록 지원이 추가되었습니다.
* 이 릴리스에서 REST API 버전이 '2017-01-19'로 증가되었습니다.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* 문서 주석에 대한 편집이 변경되었습니다.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Python 3.5에 대한 지원이 추가되었습니다.
* 요청 모듈을 사용하는 연결 풀링에 대한 지원이 추가되었습니다.
* 세션 일관성에 대한 지원이 추가되었습니다.
* 분할된 컬렉션의 TOP/ORDERBY 쿼리에 대한 지원이 추가되었습니다.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* 제한된 요청에 대한 재시도 정책 지원이 추가되었습니다. (제한된 요청은 오류 코드 429, 요청 속도가 너무 크다는 예외를 수신합니다.) 기본적으로 오류 코드 429가 발생하면 Azure Cosmos DB는 각 요청을 9번 다시 시도하며 응답 헤더에서 retryAfter 시간을 적용합니다. 이제 다시 시도 간의 서버에서 반환한 retryAfter 시간을 무시하려는 경우 고정된 다시 시도 간격 시간은 ConnectionPolicy 개체에서 RetryOptions 속성의 일부로 설정될 수 있습니다. Azure Cosmos DB는 제한된 요청 각각에 대해 30초 동안 대기하고(다시 시도 횟수와 관계 없이) 오류 코드 429를 포함하는 응답을 반환합니다. 이 시간도 ConnectionPolicy 개체에 있는 RetryOptions 속성에서 재정의할 수 있습니다.
* 이제 Cosmos DB는 x-ms-throttle-retry-count 및 x-ms-throttle-retry-wait-time-ms를 다시 시도 사이에 요청이 대기한 제한 다시 시도 수 및 누적 시간을 표시하는 모든 요청의 응답 헤더로 반환합니다.
* document_client 클래스에 노출된 RetryPolicy 클래스 및 해당 속성(retry_policy)을 제거하고 대신 일부 기본 다시 시도 옵션을 재정의하는 데 사용할 수 있는 ConnectionPolicy 클래스의 RetryOptions 속성을 노출하는 RetryOptions 클래스를 지정했습니다.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* 다중 지역 데이터베이스 계정에 대한 지원이 추가되었습니다.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* 문서의 TTL(Time to Live) 기능에 대한 지원이 추가되었습니다.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* 파티션 키 경로에 특수 문자를 허용하는 서버 쪽 분할과 관련된 버그 수정입니다.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* [분할된 컬렉션](partition-data.md) 및 [사용자 정의 성능 수준](performance-levels.md)이 구현되었습니다. 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* 여러 파티션 간의 애플리케이션 분할을 지원하기 위해 해시 및 범위 파티션 해결 프로그램을 추가합니다.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Upsert를 구현합니다. 새로운 UpsertXXX 메서드가 Upsert 기능을 지원하기 위해 추가되었습니다.
* ID 기반 라우팅을 구현합니다. 공용 API 변경 없이 모두 내부에서 변경됩니다.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* 지리 공간 인덱스를 지원합니다.
* 모든 리소스에 대한 ID 속성의 유효성을 검사합니다. 리소스에 대한 ID는 ?, /, #, \, 문자를 포함하거나 공백으로 끝날 수 없습니다.
* 새 헤더 "인덱스 변환 진행률"을 ResourceResponse에 추가합니다.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* V2 인덱싱 정책을 구현합니다.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* 프록시 연결을 지원합니다

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 최신/지원 버전으로 원활히 전환할 수 있도록 SDK 사용 중지 최소 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다. 

사용 중지된 SDK를 사용하는 Cosmos DB에 대한 요청은 서비스에서 거부됩니다.

> [!WARNING]
> **1.0.0** 이전 버전의 Python에 대한 모든 버전의 Azure SQL SDK는 **2016년 2월 29일**에 사용 중지됩니다. 
> 
> 

<br/>

| Version | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [3.0.2](#3.0.2) |2018년 11월 15일 |--- |
| [3.0.1](#3.0.1) |2018년 10월 4일 |--- |
| [2.3.3](#2.3.3) |2018년 9월 8일 |--- |
| [2.3.2](#2.3.2) |2018년 5월 8일 |--- |
| [2.3.1](#2.3.1) |2017년 12월 21일 |--- |
| [2.3.0](#2.3.0) |2017년 11월 10일 |--- |
| [2.2.1](#2.2.1) |2017년 9월 29일 |--- |
| [2.2.0](#2.2.0) |2017년 5월 10일 |--- |
| [2.1.0](#2.1.0) |2017년 5월 1일 |--- |
| [2.0.1](#2.0.1) |2016년 10월 30일 |--- |
| [2.0.0](#2.0.0) |2016년 9월 29일 |--- |
| [1.9.0](#1.9.0) |2016년 7월 7일 |--- |
| [1.8.0](#1.8.0) |2016년 6월 14일 |--- |
| [1.7.0](#1.7.0) |2016년 4월 26일 |--- |
| [1.6.1](#1.6.1) |2016년 4월 8일 |--- |
| [1.6.0](#1.6.0) |2016년 3월 29일 |--- |
| [1.5.0](#1.5.0) |2016년 1월 3일 |--- |
| [1.4.2](#1.4.2) |2015년 10월 6일 |--- |
| 1.4.1 |2015년 10월 6일 |--- |
| [1.2.0](#1.2.0) |2015년 8월 6일 |--- |
| [1.1.0](#1.1.0) |2015년 7월 9일 |--- |
| [1.0.1](#1.0.1) |2015년 5월 25일 |--- |
| [1.0.0](#1.0.0) |2015년 4월 7일 |--- |
| 0.9.4-prelease |2015년 1월 14일 |2016년 2월 29일 |
| 0.9.3-prelease |2014년 12월 9일 |2016년 2월 29일 |
| 0.9.2-prelease |2014년 11월 25일 |2016년 2월 29일 |
| 0.9.1-prelease |2014년 9월 23일 |2016년 2월 29일 |
| 0.9.0-prelease |2014년 8월 21일 |2016년 2월 29일 |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요. 

