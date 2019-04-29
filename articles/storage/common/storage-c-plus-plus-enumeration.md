---
title: Storage Client Library for C++을 사용하여 Azure Storage 리소스 나열 | Microsoft Docs
description: Microsoft Azure Storage Client Library for C++에서 목록 API를 사용하여 컨테이너, Blob, 큐, 테이블 및 엔터티를 열거하는 방법에 대해 배웁니다.
services: storage
author: dineshmurthy
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: dineshm
ms.subservice: common
ms.openlocfilehash: 13ddb4d64908421e999174623003acd2fb24024d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483454"
---
# <a name="list-azure-storage-resources-in-c"></a>C++에서 Azure Storage 리소스 나열
목록 작업은 Azure Storage를 사용하는 다양한 배포 시나리오에 중요합니다. 이 문서에서는 Microsoft Azure Storage Client Library for C++에서 제공된 API 목록을 사용하여 Microsoft Azure 스토리지에서 개체를 보다 효율적으로 열거하는 방법에 대해 설명합니다.

> [!NOTE]
> 이 가이드는 [NuGet](https://www.nuget.org/packages/wastorage) 또는 [GitHub](https://github.com/Azure/azure-storage-cpp)를 통해 사용할 수 있는 Azure Storage Client Library for C++ 버전 2.x을(를) 대상으로 합니다.
> 
> 

Storage Client Library는 Azure 스토리지에서 개체를 나열 또는 쿼리하는 다양한 방법을 제공합니다. 이 문서는 다음과 같은 시나리오를 다룹니다.

* 계정에서 컨테이너 나열
* 컨테이너 또는 가상 Blob 디렉터리에서 Blob 나열
* 계정에서 큐 나열
* 계정에서 테이블 나열
* 테이블에 엔터티 쿼리

이러한 각 메서드는 다른 시나리오에 대해 다른 오버로드를 사용하여 표시됩니다.

## <a name="asynchronous-versus-synchronous"></a>비동기 및 동기
Storage Client Library for C++는 [C++ REST 라이브러리](https://github.com/Microsoft/cpprestsdk) 상단에 기본 제공되어 있기 때문에 [pplx::task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html)를 사용하여 기본적으로 비동기 작업을 지원합니다. 예를 들면 다음과 같습니다.

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

동기 작업은 해당하는 비동기 작업을 다음과 같이 래핑합니다.

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

다중 스레딩 애플리케이션 또는 서비스로 작업하는 경우, 성능에 상당한 영향을 미치는 동기 API를 호출하는 스레드를 만드는 대신 비동기 API를 직접 사용하는 것이 좋습니다.

## <a name="segmented-listing"></a>분할된 목록
클라우드 저장소의 규모에는 분할된 목록이 필요합니다. 예를 들어, Azure Blob 컨테이너에 수백만 이상의 Blob이 있거나 Azure 테이블에 수십억 이상의 엔터티가 있을 수 있습니다. 이는 이론적인 수치가 아니라 실제 고객 사용 사례입니다.

따라서 단일 응답에 모든 개체를 나열하기에는 적합하지 않습니다. 대신 페이징을 사용하여 개체를 나열할 수 있습니다. 각각의 목록 API에는 *분할된* 오버로드가 있습니다.

분할된 목록 작업에 대한 응답에는 다음이 포함됩니다.

* <i>_segment</i>은 API 목록에 단일 호출을 반환한 결과 집합을 포함합니다.
* *continuation_token*은 결과의 다음 페이지를 가져오기 위해 다음 호출에 전달됩니다. 더 이상 반환할 결과가 없으면 연속 토큰이 null입니다.

예를 들어 컨테이너의 모든 blob을 나열하는 일반적인 호출은 다음 코드 조각처럼 보일 수 있습니다. 코드는 다음과 같은 [샘플](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp)에서 사용할 수 있습니다.

```cpp
// List blobs in the blob container
azure::storage::continuation_token token;
do
{
    azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

예를 들어, 페이지에 반환되는 결과 수는 각 API의 오버로드에서 *max_results* 매개 변수를 통해 제어할 수 있습니다.

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

*max_results* 매개 변수를 지정하지 않는 경우 한 페이지에 최대 5000개의 기본 최대 결과 값이 반환됩니다.

또한 Azure Table Storage에 대한 쿼리는 연속 토큰이 비어 있지 않은 경우에도 사용자가 지정한 *max_results* 매개 변수 값보다 더 적은 레코드를 반환하거나 레코드를 반환하지 않을 수 있습니다. 한 가지 이유는 쿼리가 5초 이내에 완료할 수 없기 때문일 수 있습니다. 연속 토큰이 비어 있지 않으면 해당 쿼리가 계속되며 코드가 세그먼트 결과의 크기를 가정하지 않습니다.

거의 모든 시나리오에 대한 권장 코딩 패턴은 목록 또는 쿼리의 명시적 진행을 제공하는 목록 및 서비스가 각 요청에 응답하는 방식을 분할하는 것입니다. 특히 C++ 애플리케이션 또는 서비스의 경우, 하위 수준의 목록 진행 제어가 메모리 및 성능 제어에 도움이 될 수 있습니다.

## <a name="greedy-listing"></a>Greedy 목록
Storage Client Library for C++ 이전 버전(0.5.0 Preview 버전 이하)에는 다음 예시와 같이 테이블 및 큐에 대해 분할되지 않은 API 목록이 포함되었습니다.

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

이러한 메서드는 분할된 API의 래퍼로 구현되었습니다. 분할된 목록에 대한 각 응답의 경우, 코드는 벡터에 대한 결과를 추가하고 전체 컨테이너가 검색된 후에 모든 결과를 반환했습니다.

이러한 방식은 저장소 계정 또는 테이블이 적은 개체를 포함할 때 적합할 수 있습니다. 하지만 개체 수가 증가하는 경우에는 모든 결과가 메모리에 유지되기 때문에 필요한 메모리가 제한 없이 증가할 수 있습니다. 호출자에게 해당 진행에 대한 정보가 없는 경우 목록 작업 한 번에 시간이 오래 걸릴 수 있습니다.

이러한 SDK에서 greedy 목록 API는 C#, Java 또는 JavaScript Node.js 환경에는 존재하지 않습니다. 이러한 greedy API 사용의 잠재적인 문제를 방지하기 위해 0.6.0 Preview 버전에서 이를 제거했습니다.

사용자의 코드가 이러한 greedy API를 호출하는 경우:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

분할된 목록 API를 사용하여 코드를 수정해야 합니다.

```cpp
azure::storage::continuation_token token;
do
{
    azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        process_entity(*it);
    }

    token = segment.continuation_token();
} while (!token.empty());
```

세그먼트의 *max_results* 매개 변수를 지정하여 애플리케이션에 대한 성능 고려 사항을 충족하도록 요청 수와 메모리 사용량 사이의 균형을 조절할 수 있습니다.

또한 분할된 목록 API를 사용하지만 "greedy" 스타일로 로컬 모음에 데이터를 저장하는 경우, 규모별로 주의 깊게 로컬 모음에서 데이터 저장을 처리하도록 코드를 리펙터링하는 것이 좋습니다.

## <a name="lazy-listing"></a>Lazy 목록
greedy 목록에 잠재적인 문제가 발생했어도 컨테이너에 너무 많은 개체가 있지 않다면 편리합니다.

또한 사용자가 C# 또는 Oracle Java SDK를 사용하는 경우, 필요한 경우 특정 오프셋의 데이터만 가져오는 lazy 스타일의 목록을 제공하는 열거 프로그래밍 모델에 친숙해야 합니다. C++에서는 반복기 기반 템플릿이 유사한 방식을 제공합니다.

예를 들어, **list_blobs**를 사용하는 일반적인 lazy 목록 API는 다음과 같습니다.

```cpp
list_blob_item_iterator list_blobs() const;
```

lazy 목록 패턴을 사용하는 일반적인 코드 조각은 다음과 같습니다.

```cpp
// List blobs in the blob container
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}
```

lazy 목록은 동기화 모드에서만 사용할 수 있습니다.

greedy 목록에 비해 lazy 목록은 필요한 경우에만 데이터를 가져옵니다. 내부적으로 다음 반복기가 다음 세그먼트로 이동할 때에만 Azure Storage에서 데이터를 가져옵니다. 따라서 메모리 사용량이 한계 크기로 제어되며 작업이 빠릅니다.

lazy 목록 API는 Storage Client Library for C++ 버전 2.2.0에 포함됩니다.

## <a name="conclusion"></a>결론
이 문서에서는 Storage Client Library for C++에서 다양한 개체에 대한 각기 다른 목록 API의 오버로드에 대해 다루었습니다. 요약하면

* 여러 스레드 시나리오에서 비동기 API를 사용하는 것이 좋습니다.
* 분할된 목록은 대부분의 시나리오에서 권장됩니다.
* lazy 목록은 동기 시나리오에서 편리한 래퍼로 라이브러리에서 제공됩니다.
* greedy 목록은 권장되지 않으며 라이브러리에서 제거되었습니다.

## <a name="next-steps"></a>다음 단계
Azure 스토리지 및 Storage Client Library for C++에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [C++에서 Blob Storage를 사용하는 방법](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [C++에서 Table Storage를 사용하는 방법](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [C++에서 Queue Storage를 사용하는 방법](../storage-c-plus-plus-how-to-use-queues.md)
* [Azure Storage Client Library for C++ API 설명서](https://azure.github.io/azure-storage-cpp/)
* [Azure Storage 팀 블로그](https://blogs.msdn.com/b/windowsazurestorage/)
* [Azure Storage 설명서](https://azure.microsoft.com/documentation/services/storage/)

