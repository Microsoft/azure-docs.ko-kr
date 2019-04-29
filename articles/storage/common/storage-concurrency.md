---
title: Microsoft Azure Storage에서 동시성 관리
description: Blob, 큐, 테이블 및 파일 서비스의 동시성을 관리하는 방법에 대해 알아봅니다.
services: storage
author: jasontang501
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 05/11/2017
ms.author: jasontang501
ms.subservice: common
ms.openlocfilehash: 9e786aed031d528b8ae574444b71753ac538cf47
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766204"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Microsoft Azure Storage에서 동시성 관리
## <a name="overview"></a>개요
최신 인터넷 기반 애플리케이션에서는 대개 여러 사용자가 데이터를 동시에 보고 업데이트합니다. 이로 인해 애플리케이션 개발자는 최종 사용자에게 예측 가능한 환경을 제공하는 방법, 특히 여러 사용자가 같은 데이터를 업데이트할 수 있는 시나리오를 신중하게 고려해야 합니다. 개발자가 일반적으로 고려하는 주요 데이터 동시성 전략에는 다음의 세 가지가 있습니다.  

1. 낙관적 동시성 - 업데이트를 수행하는 애플리케이션이 업데이트의 일환으로 애플리케이션이 데이터를 마지막으로 읽은 이후 해당 데이터가 변경되었는지를 확인합니다. 예를 들어 Wiki 페이지를 보는 두 사용자가 같은 페이지를 업데이트하면 Wiki 플랫폼은 두 번째 업데이트가 첫 번째 업데이트를 덮어쓰지 않도록 해야 하며, 두 사용자가 모두 자신의 업데이트가 정상적으로 적용되었는지를 파악할 수 있도록 해야 합니다. 이 전략은 웹 애플리케이션에서 가장 흔히 사용됩니다.
2. 비관적 동시성 - 업데이트를 수행하려는 애플리케이션이 개체를 잠가서 다른 사용자가 잠금이 해제될 때까지 데이터를 업데이트하지 못하도록 합니다. 예를 들어 마스터만 업데이트를 수행는 위치는 마스터/하위 데이터 복제 시나리오에서 마스터 일반적으로 보유할 배타적 잠금을 다른 사용자를 업데이트할 수 있도록 데이터 오랜된 기간에 대 한 합니다.
3. 마지막 작성자의 업데이트 적용 - 애플리케이션이 데이터를 처음 읽은 후 다른 애플리케이션이 데이터를 업데이트했는지 여부를 확인하지 않고 모든 업데이트 작업을 진행하도록 허용하는 방식입니다. 데이터가 분할되어 여러 사용자가 같은 데이터에 액세스할 가능성이 없을 때는 일반적으로 이 전략을 사용하거나 공식적인 전략을 사용하지 않습니다. 일시적인 데이터 스트림을 처리하는 경우에도 이 전략이 유용할 수 있습니다.  

이 문서에서는 Azure Storage 플랫폼이 이와 같은 모든 세 가지 동시성 전략에 대해 가장 효율적인 지원을 제공함으로써 개발을 간소화하는 방식을 대략적으로 설명합니다.  

## <a name="azure-storage--simplifies-cloud-development"></a>Azure Storage - 클라우드 개발 간소화
Azure Storage 서비스는 세 가지 전략을 모두 지원하지만 특히 낙관적 동시성과 비관적 동시성을 완벽하게 지원할 수 있습니다. Storage 서비스가 데이터 삽입 또는 업데이트 작업을 커밋하면 해당 데이터에 대한 모든 추가 액세스 시 최신 업데이트가 표시되도록 보장하는 강력한 동시성 모델을 사용하도록 디자인되었기 때문입니다. 결과적 일관성 모델을 사용하는 저장소 플랫폼에서는 특정 사용자가 쓰기를 수행하는 시점과 다른 사용자에게 업데이트된 데이터가 표시되는 시점 간에 지연 시간이 있으므로, 불일치 사항이 최종 사용자에게 영향을 주지 않도록 하기 위해 클라이언트 애플리케이션 개발 과정이 복잡해집니다.  

개발자는 적절한 동시성 전략을 선택해야 할 뿐 아니라 저장소 플랫폼이 변경 내용, 특히 여러 트랜잭션 간의 같은 개체에 대한 변경 내용을 격리하는 방법도 파악해야 합니다. Azure 저장소 서비스는 스냅숏 격리를 사용하여 단일 파티션 내에서 읽기 작업과 쓰기 작업이 동시에 수행되도록 허용합니다. 다른 격리 수준과는 달리 스냅숏 격리에서는 업데이트가 수행되는 동안에도 모든 읽기 시 데이터의 일관된 스냅숏이 표시됩니다. 이를 위해 업데이트 트랜잭션을 처리하는 동안 마지막으로 커밋된 값을 반환합니다.  

## <a name="managing-concurrency-in-blob-storage"></a>Blob Storage에서 동시성 관리
낙관적 동시성 모델이나 비관적 동시성 모델을 사용하여 Blob 서비스의 컨테이너와 Blob에 대한 액세스를 관리할 수 있습니다. 전략을 명시적으로 지정하지 않으면 마지막 작성자의 업데이트 적용 전략이 기본적으로 사용됩니다.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Blob 및 컨테이너에 대한 낙관적 동시성
Storage 서비스는 저장되는 모든 개체에 식별자를 할당합니다. 이 식별자는 개체에 대해 업데이트 작업을 수행할 때마다 업데이트되며, HTTP 프로토콜 내에 정의된 ETag(엔터티 태그) 헤더를 사용하여 HTTP GET 응답의 일부분으로 클라이언트에 반환됩니다. 이러한 개체에 대해 업데이트를 수행하는 사용자는 조건부 헤더와 함께 원본 ETag를 보내 특정 조건이 충족된 경우에만 업데이트가 수행되도록 할 수 있습니다. 이 경우 조건은 “If-Match” 헤더로, Storage 서비스는 업데이트 요청에 지정된 ETag의 값이 Storage 서비스에 저장된 값과 같은지를 확인해야 합니다.  

이 프로세스는 대략적으로 다음과 같이 수행됩니다.  

1. 저장소 서비스에서 Blob를 검색합니다. 응답에는 저장소 서비스에서 개체의 현재 버전을 식별하는 HTTP ETag 헤더 값이 포함됩니다.
2. Blob를 업데이트할 때는 서비스로 보내는 요청의 **If-Match** 조건부 헤더에 1단계에서 받은 ETag 값을 포함합니다.
3. 서비스가 요청의 ETag 값을 Blob의 현재 ETag 값과 비교합니다.
4. Blob의 현재 ETag 값이 요청의 **If-Match** 조건부 헤더에 포함된 ETag와 다른 버전이면 서비스는 클라이언트에 412 오류를 반환합니다. 클라이언트는 이 오류를 통해 Blob를 검색한 후 다른 프로세스에서 Blob를 업데이트했음을 확인할 수 있습니다.
5. Blob의 현재 ETag 값이 요청의 **If-Match** 조건부 헤더에 포함된 ETag와 같은 버전이면 서비스는 요청된 작업을 수행하며 Blob의 현재 ETag 값을 업데이트하여 새 버전을 만들었음을 표시합니다.  

클라이언트 저장소 라이브러리 4.2.0을 사용하는 다음 C# 코드 조각은 이전에 검색했거나 삽입한 Blob의 속성에서 액세스할 수 있는 ETag 값을 기준으로 **If-Match AccessCondition** 을 생성하는 방법의 간단한 예제를 보여 줍니다. 그런 다음 Blob을 업데이트할 때 **AccessCondition** 개체를 사용합니다: **AccessCondition** 개체는 요청에 **If-Match** 헤더를 추가합니다. 다른 프로세스가 Blob을 업데이트한 경우 Blob 서비스는 HTTP 412(전재 조건 실패) 상태 메시지를 반환합니다. 여기서 전체 샘플을 다운로드할 수 있습니다. [Azure Storage를 사용하여 동시성 관리](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage blob service which returns the ETag in response.
string originalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No ETag provided so original blob is overwritten (thus generating a new ETag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the original ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using original ETag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(originalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's original ETag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}  
```

Storage 서비스는 **If-Modified-Since**, **If-Unmodified-Since** 및 **If-None-Match**와 같은 추가 조건부 헤더 및 이러한 헤더의 조합도 지원합니다. 자세한 내용은 MSDN의 [Blob 서비스 작업의 조건부 헤더 지정](https://msdn.microsoft.com/library/azure/dd179371.aspx)을 참조하세요.  

아래 표에는 요청에서 **If-Match** 와 같은 조건부 헤더를 수락하며 응답에서 ETag 값을 반환하는 컨테이너 작업이 요약되어 있습니다.  

| 작업(Operation) | 컨테이너 ETag 값 반환 | 추가 헤더 수락 |
|:--- |:--- |:--- |
| 컨테이너 만들기 |예 |아닙니다. |
| 컨테이너 속성 가져오기 |예 |아닙니다. |
| 컨테이너 메타데이터 가져오기 |예 |아닙니다. |
| 컨테이너 메타데이터 설정 |예 |예 |
| 컨테이너 ACL 가져오기 |예 |아닙니다. |
| 컨테이너 ACL 설정 |예 |예(*) |
| 컨테이너 삭제 |아닙니다. |예 |
| 컨테이너 임대 |예 |예 |
| Blob 나열 |아닙니다. |아닙니다. |

(*) SetContainerACL이 정의하는 권한은 캐시되며 이러한 권한에 대한 업데이트가 전파되려면 30초가 걸립니다. 이 시간 동안에는 업데이트의 일관성이 보장되지 않습니다.  

아래 표에는 요청에서 **If-Match** 와 같은 조건부 헤더를 수락하며 응답에서 ETag 값을 반환하는 Blob 작업이 요약되어 있습니다.

| 작업(Operation) | ETag 값 반환 | 추가 헤더 수락 |
|:--- |:--- |:--- |
| Blob 배치 |예 |예 |
| Blob 가져오기 |예 |예 |
| Blob 속성 가져오기 |예 |예 |
| Blob 속성 설정 |예 |예 |
| Blob 메타데이터 가져오기 |예 |예 |
| Blob 메타데이터 설정 |예 |예 |
| Blob 임대(*) |예 |예 |
| Blob 스냅숏 |예 |예 |
| Blob 복사 |예 |예(원본 및 대상 Blob의 경우) |
| Blob 복사 중단 |아닙니다. |아닙니다. |
| Blob 삭제 |아닙니다. |예 |
| 블록 배치 |아닙니다. |아닙니다. |
| 블록 목록 배치 |예 |예 |
| 블록 목록 가져오기 |예 |아닙니다. |
| 페이지 가져오기 |예 |예 |
| 페이지 범위 가져오기 |예 |예 |

(*) Blob 임대에서는 Blob의 ETag가 변경되지 않습니다.  

### <a name="pessimistic-concurrency-for-blobs"></a>Blob에 대한 비관적 동시성
단독 사용을 위해 Blob를 잠그려는 경우 Blob에 대한 [임대](https://msdn.microsoft.com/library/azure/ee691972.aspx)를 획득할 수 있습니다. 임대를 획득하는 경우 임대에 필요한 기간을 지정합니다. 이 기간은 15초에서 60초 사이이거나 배타적 잠금 상태가 되는 무한일 수 있습니다. 유한 임대는 갱신하여 연장할 수 있으며 완료된 임대는 해제할 수 있습니다. Blob 서비스는 만료된 유한 임대를 자동으로 해제합니다.  

임대를 사용하면 배타적 쓰기/공유 읽기, 배타적 쓰기/배타적 읽기, 공유 쓰기/배타적 읽기 등의 다양한 동기화 전략을 지원할 수 있습니다. 임대가 있는 상태에서 저장소 서비스가 배타적 쓰기(배치, 설정 및 삭제 작업)를 강제로 수행하지만 읽기 작업에 대해 독점성을 보장하는 경우 개발자는 모든 클라이언트 애플리케이션에서 임대 ID를 사용하고 한 번에 한 클라이언트만 유효한 임대 ID를 사용하도록 해야 합니다. 임대 ID를 포함하지 않는 읽기 작업에서는 공유 읽기가 수행됩니다.  

다음 C# 코드 조각은 Blob에 대해 30초 동안 배타적 임대를 획득하고 Blob의 내용을 업데이트한 후에 임대를 해제하는 예제를 보여 줍니다. 새 임대를 획득하려 할 때 Blob에 대해 유효한 임대가 이미 있으면 Blob service는 “HTTP(409) 충돌” 상태 결과를 반환합니다. 다음 코드 조각은 저장소 서비스에서 Blob를 업데이트하기 위한 요청을 할 때 **AccessCondition** 개체를 사용하여 임대 정보를 캡슐화합니다.  여기서 전체 샘플을 다운로드할 수 있습니다. [Azure Storage를 사용하여 동시성 관리](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}  
```

임대 ID를 전달하지 않고 임대한 Blob에 대해 쓰기 작업을 시도하면 요청이 실패하고 412 오류가 발생합니다. **UploadText** 메서드를 호출하기 전에 임대가 만료되었는데 임대 ID를 전달하는 경우에도 요청이 실패하고 **412** 오류가 발생합니다. 임대 만료 시간 및 임대 ID를 관리하는 방법에 대한 자세한 내용은 [Blob 임대](https://msdn.microsoft.com/library/azure/ee691972.aspx) REST 문서를 참조하세요.  

다음 Blob 작업에서는 임대를 사용하여 비관적 동시성을 관리할 수 있습니다.  

* Blob 배치
* Blob 가져오기
* Blob 속성 가져오기
* Blob 속성 설정
* Blob 메타데이터 가져오기
* Blob 메타데이터 설정
* Blob 삭제
* 블록 배치
* 블록 목록 배치
* 블록 목록 가져오기
* 페이지 가져오기
* 페이지 범위 가져오기
* 스냅숏 Blob - 임대가 있는 경우 임대 ID는 선택 사항임
* Blob 복사 - 대상 Blob에 대한 임대가 있는 경우 임대 ID는 필수임
* Blob 복사 중단 - 대상 Blob에 대한 무한 임대가 있는 경우 임대 ID는 필수임
* Blob 임대  

### <a name="pessimistic-concurrency-for-containers"></a>컨테이너에 대한 비관적 동시성
컨테이너에 대해 임대를 사용하면 배타적 쓰기/공유 읽기, 배타적 쓰기/배타적 읽기, 공유 쓰기/배타적 읽기 등 Blob에서와 같은 동기화 전략을 지원할 수 있습니다. 그러나 Blob와 달리 저장소 서비스에서는 삭제 작업에 대해서만 독점성을 적용합니다. 임대가 활성 상태인 컨테이너를 삭제하려면 클라이언트가 삭제 요청에 활성 임대 ID를 포함해야 합니다. 기타 모든 컨테이너 작업은 임대 ID를 포함하지 않아도 임대한 컨테이너에서 성공합니다. 이 경우 해당 작업은 공유 작업입니다. 업데이트(배치 또는 설정) 또는 읽기 작업에서 독점성이 필요한 경우 개발자는 모든 클라이언트가 임대 ID를 사용하고 한 번에 한 클라이언트만 유효한 임대 ID를 사용하도록 해야 합니다.  

다음 컨테이너 작업에서는 임대를 사용하여 비관적 동시성을 관리할 수 있습니다.  

* 컨테이너 삭제
* 컨테이너 속성 가져오기
* 컨테이너 메타데이터 가져오기
* 컨테이너 메타데이터 설정
* 컨테이너 ACL 가져오기
* 컨테이너 ACL 설정
* 컨테이너 임대  

자세한 내용은 다음을 참조하세요.  

* [Blob 서비스 작업의 조건부 헤더 지정](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [컨테이너 임대](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Blob 임대](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>테이블 서비스에서 동시성 관리
낙관적 동시성 검사를 수행하도록 명시적으로 선택해야 하는 Blob 서비스에서와는 달리 테이블 서비스에서는 엔터티로 작업할 때 낙관적 동시성 검사를 기본 동작으로 사용합니다. 테이블 서비스와 Blob 서비스의 또 다른 차이점은, 테이블 서비스에서는 엔터티의 동시성 동작만 관리할 수 있는 반면 Blob 서비스에서는 컨테이너와 Blob의 동시성을 모두 관리할 수 있다는 것입니다.  

낙관적 동시성을 사용하고 엔터티를 Table Storage 서비스에서 검색한 이후 다른 프로세스에서 해당 엔터티를 수정했는지 확인하려는 경우 테이블 서비스에서 엔터티를 반환할 때 수신되는 ETag 값을 사용하면 됩니다. 이 프로세스는 대략적으로 다음과 같이 수행됩니다.  

1. Table Storage 서비스에서 엔터티를 검색합니다. 응답에는 스토리지 서비스에서 해당 엔터티와 연결된 현재 식별자를 식별하는 ETag 값이 포함됩니다.
2. 엔터티를 업데이트할 때는 서비스로 보내는 요청의 필수 **If-Match** 헤더에 1단계에서 받은 ETag 값을 포함합니다.
3. 서비스가 요청의 ETag 값을 엔터티의 현재 ETag 값과 비교합니다.
4. 엔터티의 현재 ETag 값이 요청의 필수 **If-Match** 헤더에 포함된 ETag와 다르면 서비스는 클라이언트에 412 오류를 반환합니다. 클라이언트는 이 오류를 통해 엔터티를 검색한 후 다른 프로세스에서 Blob를 업데이트했음을 확인할 수 있습니다.
5. 엔터티의 현재 ETag 값이 요청의 필수 **If-Match** 헤더에 포함된 ETag와 같거나 **If-Match** 헤더에 와일드카드 문자(*)가 포함되어 있으면 서비스는 요청된 작업을 수행하며 엔터티의 현재 ETag 값을 업데이트하여 엔터티가 업데이트되었음을 표시합니다.  

Blob 서비스와 달리 테이블 서비스에서는 클라이언트가 업데이트 요청에 **If-Match** 헤더를 포함해야 합니다. 그러나 클라이언트가 요청에서 **If-Match** 헤더를 와일드카드 문자(*)로 설정하는 경우에는 무조건 업데이트(마지막 작성자의 업데이트 적용 전략)를 강제 지정하고 동시성 검사를 무시할 수 있습니다.  

다음 C# 코드 조각은 이전에 만들거나 검색한 customer 엔터티의 메일 주소를 업데이트하는 작업을 보여 줍니다. 초기 삽입 또는 검색 작업에서는 ETag 값을 customer 개체에 저장합니다. 이 샘플은 바꾸기 작업을 실행할 때 같은 개체 인스턴스를 사용하므로 ETag 값을 테이블 서비스에 자동으로 다시 보내기 때문에 서비스가 동시성 위반을 검사할 수 있습니다. 다른 프로세스가 Table Storage에서 엔터티를 업데이트한 경우 서비스는 HTTP 412(전재 조건 실패) 상태 메시지를 반환합니다.  여기서 전체 샘플을 다운로드할 수 있습니다. [Azure Storage를 사용하여 동시성 관리](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}  
```

동시성 검사를 명시적으로 사용하지 않도록 설정하려면 바꾸기 작업을 실행하기 전에 **employee** 개체의 **ETag** 속성을 “*”로 설정해야 합니다.  

```csharp
customer.ETag = "*";  
```

다음 표에는 테이블 엔터티 작업이 ETag 값을 사용하는 방식이 요약되어 있습니다.

| 작업(Operation) | ETag 값 반환 | If-Match 요청 헤더 필요 여부 |
|:--- |:--- |:--- |
| 엔터티 쿼리 |예 |아닙니다. |
| 엔터티 삽입 |예 |아닙니다. |
| 엔터티 업데이트 |예 |예 |
| 엔터티 병합 |예 |예 |
| 엔터티 삭제 |아닙니다. |예 |
| 엔터티 삽입 또는 바꾸기 |예 |아닙니다. |
| 엔터티 삽입 또는 병합 |예 |아닙니다. |

**엔터티 삽입 또는 바꾸기**와 **엔터티 삽입 또는 병합** 작업에서는 테이블 서비스에 ETag 값을 보내지 않으므로 동시성 검사를 수행하지 *않습니다*.  

일반적으로 테이블을 사용하는 개발자는 확장 가능한 애플리케이션을 개발할 때 낙관적 동시성을 사용해야 합니다. 비관적 잠금이 필요한 경우 개발자가 테이블에 액세스할 때 사용할 수 있는 한 가지 방법은 각 테이블에 대해 지정된 Blob를 할당하고 테이블에 작업을 수행하기 전에 Blob에 대한 임대를 받는 것입니다. 이 방식을 사용하는 경우 애플리케이션은 테이블에 작업을 수행하기 전에 모든 데이터 액세스 경로가 임대를 획득하는지 확인해야 합니다. 또한 최소 임대 시간은 15초이므로 확장성을 신중하게 고려해야 합니다.  

자세한 내용은 다음을 참조하세요.  

* [엔터티에 대한 작업](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>큐 서비스에서 동시성 관리
큐 서비스에서 동시성이 중요한 시나리오 중 하나는 여러 클라이언트가 큐에서 메시지를 검색할 때입니다. 큐에서 메시지를 검색할 때 응답에는 메시지와 PopReceipt 값(메시지를 삭제할 때 필요함)이 포함됩니다. 메시지는 큐에서 자동으로 삭제되지 않으며 검색된 후 visibilitytimeout 매개 변수로 지정된 시간 간격 동안 다른 클라이언트에는 표시되지 않습니다. 메시지를 검색하는 클라이언트는 메시지가 처리된 후 응답의 TimeNextVisible 요소로 지정된 시간 전에 메시지를 삭제해야 합니다. 이 시간은 visibilitytimeout 매개 변수의 값을 기준으로 계산됩니다. visibilitytimeout의 값을 메시지가 검색된 시간에 합하여 TimeNextVisible의 값을 결정합니다.  

큐 서비스는 낙관적 동시성이나 비관적 동시성을 지원하지 않으므로 큐에서 검색된 메시지를 처리하는 클라이언트는 메시지가 idempotent 방식으로 처리되는지를 확인해야 합니다. SetQueueServiceProperties, SetQueueMetaData, SetQueueACL, UpdateMessage 등의 업데이트 작업에는 마지막 작성자의 업데이트 적용 전략이 사용됩니다.  

자세한 내용은 다음을 참조하세요.  

* [큐 서비스 REST API](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [메시지 가져오기](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>파일 서비스에서 동시성 관리
서로 다른 두 프로토콜 엔드포인트인 SMB와 REST를 사용하여 파일 서비스에 액세스할 수 있습니다. REST 서비스는 낙관적 잠금이나 비관적 잠금을 지원하지 않으며 모든 업데이트는 마지막 작성자의 업데이트 적용 전략을 따릅니다. 파일 공유를 탑재하는 SMB 클라이언트는 파일 시스템 잠금 메커니즘을 활용하여 공유 파일에 대한 액세스를 관리할 수 있습니다. 여기에는 비관적 잠금을 수행하는 기능이 포함됩니다. SMB 클라이언트는 파일을 열 때 파일 액세스 및 공유 모드를 모두 지정합니다. 파일 공유 모드를 "없음"으로 지정하는 동시에 파일 액세스 옵션을 "쓰기" 또는 "읽기/쓰기"로 설정하면 파일을 닫을 때까지 SMB 클라이언트가 파일을 잠급니다. SMB 클라이언트가 파일을 잠근 경우 해당 파일에 대해 REST 작업을 시도하면 REST 서비스가 상태 코드 409(충돌) 및 오류 코드 SharingViolation을 반환합니다.  

SMB 클라이언트는 삭제를 위해 파일을 열 때 해당 파일에 대한 기타 모든 SMB 클라이언트 열기 핸들이 닫힐 때까지 파일을 삭제 보류 중으로 표시합니다. 파일이 삭제 보류 중으로 표시되어 있는 동안 해당 파일에 대해 REST 작업을 수행하면 상태 코드 409(충돌)와 오류 코드 SMBDeletePending이 반환됩니다. SMB 클라이언트가 파일을 닫기 전에 삭제 보류 중 플래그를 제거할 수 있으므로 상태 코드 404(찾을 수 없음)는 반환되지 않습니다. 즉, 파일이 제거된 경우에만 상태 코드 404(찾을 수 없음)가 반환됩니다. 파일이 SMB 삭제 보류 중 상태인 경우 목록 파일 결과에 포함되지 않습니다. 또한 REST 파일 삭제 및 REST 디렉터리 삭제 작업은 원자성으로 커밋되며 삭제 보류 중 상태를 나타내지 않습니다.  

자세한 내용은 다음을 참조하세요.  

* [파일 잠금 관리](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>요약 및 다음 단계
Microsoft Azure Storage 서비스는 개발자가 기본적으로 제공하려는 동시성 및 데이터 일관성과 같은 주요 디자인 가정 사항을 다시 고려하거나 절충하지 않고도 매우 복잡한 온라인 애플리케이션의 요구 사항을 충족할 수 있도록 디자인되었습니다.  

이 블로그에서 참조하는 전체 샘플 애플리케이션은 다음 문서를 참조하세요.  

* [Azure Storage를 사용하여 동시성 관리 - 샘플 애플리케이션](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Azure Storage에 대한 자세한 내용은 다음을 참조하세요.  

* [Microsoft Azure Storage 홈페이지](https://azure.microsoft.com/services/storage/)
* [Azure Storage 소개](storage-introduction.md)
* [Blob](../blobs/storage-dotnet-how-to-use-blobs.md), [테이블](../../cosmos-db/table-storage-how-to-use-dotnet.md), [큐](../storage-dotnet-how-to-use-queues.md) 및 [파일](../storage-dotnet-how-to-use-files.md)에 대한 저장소 시작
* 스토리지 아키텍처 - [Azure Storage: 강력한 일관성을 지닌 고가용성 클라우드 스토리지 서비스](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

