---
title: Blob 인덱스를 사용 하 여 Azure Blob Storage에서 데이터 관리 및 찾기 (미리 보기)
description: Blob 인덱스 태그를 사용 하 여 blob 개체를 범주화 하 고, 관리 하 고, 쿼리 하는 방법에 대해 알아봅니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/01/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: ed70a05e0a6213ce00a6e0514f0741e8abbaeef9
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690377"
---
# <a name="manage-and-find-data-on-azure-blob-storage-with-blob-index-preview"></a>Blob 인덱스를 사용 하 여 Azure Blob Storage에서 데이터 관리 및 찾기 (미리 보기)

데이터 집합이 더 크고 클 수록 데이터의 해상에서 특정 개체를 찾는 것은 어렵고 어려울 수 있습니다. Blob 인덱스는 키-값 인덱스 태그 특성을 사용 하 여 데이터 관리 및 검색 기능을 제공 합니다 .이 특성을 사용 하면 단일 컨테이너 내에서 또는 저장소 계정의 모든 컨테이너에서 개체를 분류 하 고 찾을 수 있습니다. 나중에 데이터의 요구 사항이 변경 됨에 따라 개체는 현재 컨테이너 조직과 함께 남아 있는 동안 인덱스 태그를 업데이트 하 여 동적으로 다시 분류 수 있습니다. Blob 인덱스를 활용 하면 동일한 서비스에서 blob 데이터와 관련 인덱스 특성을 통합 하 여 개발을 간소화할 수 있습니다. 기본 기능을 사용 하 여 효율적이 고 확장 가능한 응용 프로그램을 빌드할 수 있습니다. 

Blob 인덱스를 사용 하면 다음을 수행할 수 있습니다.

- 데이터 관리를 위해 키-값 인덱스 태그를 사용 하 여 동적으로 blob 범주화
- 단일 컨테이너 또는 전체 저장소 계정에서 태그가 지정 된 특정 blob을 빠르게 찾습니다.
- 인덱스 태그 계산을 기반으로 blob Api에 대 한 조건부 동작 지정
- [수명 주기 관리](storage-lifecycle-management-concepts.md) 와 같은 blob 플랫폼 기능에서 고급 컨트롤에 대 한 인덱스 태그를 활용 합니다.

저장소 계정에 수백만 개의 blob이 있으며 다양 한 응용 프로그램에서 작성 하 고 액세스 하는 시나리오를 고려해 보세요. 단일 프로젝트에서 관련 된 모든 데이터를 찾으려고 하지만 다른 blob 명명 규칙을 사용 하 여 여러 컨테이너에 데이터를 분산할 수 있으므로 범위 내에 있는 것이 확실 하지 않습니다. 그러나 응용 프로그램이 해당 프로젝트 및 식별 설명에 따라 태그가 있는 모든 데이터를 업로드 한다는 것을 알고 있습니다. 수백만 개의 blob을 검색 하 고 이름과 속성을 비교 하는 대신 검색 기준으로를 사용할 수 있습니다 `Project = Contoso` . Blob 인덱스는에서 50 blob 집합만 빠르게 찾고 반환 하도록 전체 저장소 계정에서 모든 컨테이너를 필터링 합니다 `Project = Contoso` . 

Blob 인덱스를 사용 하는 방법에 대 한 예제를 시작 하려면 [Blob 인덱스를 활용 하 여 데이터 관리 및 찾기](storage-blob-index-how-to.md)를 참조 하세요.

## <a name="blob-index-tags-and-data-management"></a>Blob 인덱스 태그 및 데이터 관리

컨테이너 및 blob 이름 접두사는 저장 된 데이터에 대 한 1 차원 분류입니다. 이제 blob 인덱스를 사용 하 여 특성 태그가 적용 된 모든 [blob 데이터 형식 (블록, 추가 또는 페이지)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) 에 대 한 다중 차원 분류를 수행할 수 있습니다. 이 다차원 분류는 기본적으로 인덱싱됩니다. 그러면 데이터를 신속 하 게 쿼리하고 찾을 수 있습니다.

저장소 계정에서 다음과 같은 5 개의 blob을 고려 합니다.
>
> container1/transaction.csv  
> container2/campaign.docx  
> 사진/bannerphoto.png  
> 보관/완료/2019review.pdf  
> logs/2020/01/01/logfile.txt  
>

이러한 blob은 현재 컨테이너/가상 폴더/b a p 이름의 접두사를 사용 하 여 분리 됩니다. Blob 인덱스를 사용 하 여 이러한 다섯 개의 blob에 대 한 인덱스 태그 특성을 설정 `Project = Contoso` 하 여 현재 접두사 조직을 유지 하면서 함께 범주화 할 수 있습니다. 이렇게 하면 저장소 플랫폼의 다차원 인덱스를 사용 하 여 데이터를 필터링 하 고 찾는 기능을 제공 하 여 데이터를 이동할 필요가 없습니다.

## <a name="setting-blob-index-tags"></a>Blob 인덱스 태그 설정

Blob 인덱스 태그는 저장소 계정 내의 새 개체 또는 기존 개체에 적용할 수 있는 키-값 특성입니다. PutBlob, PutBlockList 또는 CopyBlob 작업을 사용 하 여 업로드 프로세스 중에 인덱스 태그를 지정 하 고 선택적 x m 태그 헤더를 지정할 수 있습니다. 저장소 계정에 blob이 이미 있는 경우 요청 본문의 blob 인덱스 태그 특성을 지정 하는 서식이 지정 된 XML 문서를 사용 하 여 SetBlobTags를 호출할 수 있습니다. 

설정할 수 있는 다음 예제 태그를 고려 하십시오.

Blob에서 단일 태그를 적용 하 여 데이터 처리가 완료 된 시간을 설명할 수 있습니다.
>
> "processedDate" = ' 2020-01-01 '
>
Blob에 여러 태그를 적용 하 여 데이터를 보다 자세히 설명할 수 있습니다.
>
> "Project" = ' Contoso '  
> "분류 된" = ' True '  
> "Status" = ' 처리 되지 않음 '  
> "Priority" = ' 01 ' 
>

기존 인덱스 태그 특성을 수정 하려면 먼저 기존 태그 특성을 검색 하 고 태그 특성을 수정한 후를 SetBlobTags 작업으로 바꾸어야 합니다. Blob에서 모든 인덱스 태그를 제거 하려면 태그 특성을 지정 하지 않고 SetBlobTags 작업을 호출 합니다. Blob 인덱스 태그는 blob 데이터 콘텐츠에 대 한 하위 리소스 이므로 SetBlobTags는 기본 콘텐츠를 수정 하지 않고 blob의 마지막 수정 시간 또는 eTag (엔터티 태그)를 변경 하지 않습니다. 모든 현재 기본 blob 및 이전 버전에 대 한 인덱스 태그를 만들거나 수정할 수 있습니다. 그러나 스냅숏이 나 일시 삭제 된 blob의 태그는 수정할 수 없습니다. 

Blob 인덱스 태그에는 다음과 같은 제한이 적용 됩니다.
- 각 blob에는 최대 10 개의 blob 인덱스 태그를 사용할 수 있습니다.
- 태그 키는 1 ~ 007e; 128 자 사이 여야 합니다.
- 태그 값은 0 ~ 007e; 256 자 사이 여야 합니다.
- 태그 키 및 값은 대/소문자를 구분 합니다.
- 태그 키 및 값은 문자열 데이터 형식만 지원 합니다. 숫자, 날짜, 시간 또는 특수 문자는 문자열로 저장 됩니다.
- 태그 키 및 값은 다음 명명 규칙을 따라야 합니다.
  - 영숫자 영숫자: a-z, a-z, 0-9
  - 특수 문자: 공백, 더하기, 빼기, 마침표, 콜론, 같음, 밑줄, 슬래시

## <a name="getting-and-listing-blob-index-tags"></a>Blob 인덱스 태그 가져오기 및 나열

Blob 인덱스 태그는 blob 데이터와 함께 하위 리소스로 저장 되며 기본 blob 데이터 콘텐츠와 독립적으로 검색할 수 있습니다. 설정 되 면 단일 blob에 대 한 blob 인덱스 태그를 검색 하 고 GetBlobTags 작업을 사용 하 여 즉시 검토할 수 있습니다. 또한 매개 변수를 사용 하는 ListBlobs 작업은 `include:tags` 적용 된 blob 인덱스 태그와 함께 컨테이너 내의 모든 blob을 반환 합니다. 

Blob 인덱스 태그가 하나 이상인 blob의 경우 blob에 존재 하는 blob 인덱스 태그의 수를 나타내는 x-y Blob, GetBlob 및 GetBlobProperties 작업에서 x-y 태그 수가 반환 됩니다.

## <a name="finding-data-using-blob-index-tags"></a>Blob 인덱스 태그를 사용 하 여 데이터 찾기

Blob에 blob 인덱스 태그가 설정 된 상태에서 인덱싱 엔진은 해당 키/값 특성을 다차원 인덱스에 노출 합니다. 인덱스 태그가 blob에 존재 하 고 즉시 검색할 수 있는 반면 blob 인덱스가 새로 고쳐진 인덱스 태그 특성으로 업데이트 되기까지 다소 시간이 걸릴 수 있습니다. Blob 인덱스가 업데이트 된 후에는 이제 blob 저장소에서 제공 하는 기본 쿼리 및 검색 기능을 활용할 수 있습니다.

FindBlobsByTags 작업을 통해 인덱스 태그가 지정 된 blob 인덱스 쿼리 식과 일치 하는 blob의 필터링 된 반환 집합을 가져올 수 있습니다. Blob 인덱스는 저장소 계정 내의 모든 컨테이너에서 필터링을 지원 하거나 필터링의 범위를 단일 컨테이너로 지정할 수 있습니다. 모든 blob 인덱스 태그 키 및 값은 문자열 이므로 지원 되는 관계형 연산자는 인덱스 태그 값에 대해 사전적 정렬을 사용 합니다.

다음 기준은 blob 인덱스 필터링에 적용 됩니다.
-   태그 키는 큰따옴표 (")로 묶어야 합니다.
-   태그 값과 컨테이너 이름은 작은따옴표 (')로 묶어야 합니다.
-   @ 문자는 특정 컨테이너 이름 (예: @container ' ContainerName ')에 대 한 필터링에만 사용할 수 있습니다.
- 필터는 문자열에 대 한 사전적 정렬을 통해 적용 됩니다.
-   동일한 키에 대 한 동일한 면 범위 작업이 유효 하지 않습니다 (예: "Rank" > ' 10 ' 및 "Rank" >= ' 15 ').
- REST를 사용 하 여 필터 식을 만들 때 문자는 URI로 인코딩해야 합니다.

아래 표에서는 Findblob Sbytags에 대 한 모든 유효한 연산자를 보여 줍니다.

|  연산자  |  Description  | 예제 |
|------------|---------------|---------|
|     =      |     같음     | "Status" = ' 진행 중 ' | 
|     >      |  보다 큼 |  "Date" > ' 2018-06-18 ' |
|     >=     |  크거나 같음 | "Priority" >= ' 5 ' | 
|     <      |  보다 작음    | "Age" < ' 32 ' |
|     <=     |  작거나 같음  | "Company" <= ' Contoso ' |
|    AND     |  논리적 and  | "Rank" >= ' 010 ' 및 "Rank" < ' 100 ' |
| @container |  특정 컨테이너에 대 한 범위   | @container = ' videofiles ' 및 "status" = ' done ' |

> [!NOTE]
> 태그를 설정 하 고 쿼리 하는 경우 사전순으로 정렬 합니다.
> - 숫자는 문자 앞에 정렬 됩니다. 숫자는 첫 번째 숫자를 기준으로 정렬 됩니다.
> - 대문자가 소문자보다 앞에 정렬됩니다.
> - 기호가 표준이 아닙니다. 일부 기호는 숫자 값 앞에 정렬 됩니다. 다른 기호는 문자 앞뒤에 정렬 됩니다.
>

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Blob 인덱스 태그를 사용 하는 조건부 Blob 작업
REST 버전 2019-10-10 이상에서 대부분의 [blob Service api](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs) 는 이제 지정 된 blob 인덱스 조건이 충족 될 경우에만 작업이 성공 하도록 하는 조건부 헤더 인 x-ms 태그를 지원 합니다. 조건이 충족 되지 않으면이 발생 `error 412: The condition specified using HTTP conditional header(s) is not met` 합니다.

X-y 헤더는 다른 기존 HTTP 조건부 헤더 (-Match,-None-Match 등)와 함께 사용할 수 있습니다.  요청에서 여러 조건부 헤더를 제공 하는 경우 작업이 성공 하려면 모두 true를 평가 해야 합니다.  모든 조건부 헤더는 실제로 논리적 AND와 결합 됩니다. 

아래 표에서는 조건부 연산에 대 한 모든 유효한 연산자를 보여 줍니다.

|  연산자  |  Description  | 예제 |
|------------|---------------|---------|
|     =      |     같음     | "Status" = ' 진행 중 ' |
|     <>     |   같지 않음   | "상태"  <>  ' 완료 '  | 
|     >      |  보다 큼 |  "Date" > ' 2018-06-18 ' |
|     >=     |  크거나 같음 | "Priority" >= ' 5 ' | 
|     <      |  보다 작음    | "Age" < ' 32 ' |
|     <=     |  작거나 같음  | "Company" <= ' Contoso ' |
|    AND     |  논리적 and  | "Rank" >= ' 010 ' 및 "Rank" < ' 100 ' |
|     또는     |  논리적 or   | "Status" = ' Done ' 또는 "Priority" >= ' 05 ' |

> [!NOTE]
> Blob 작업에는 조건부 x-m-태그 헤더에 허용 되지만, FindBlobsByTags 작업에는 없는 두 개의 추가 연산자 (not and logical or)가 있습니다.

## <a name="platform-integrations-with-blob-index-tags"></a>Blob 인덱스 태그를 사용한 플랫폼 통합

Blob 인덱스의 태그는 blob 데이터를 분류, 관리 및 검색 하는 데 도움이 될 뿐만 아니라 [수명 주기 관리](storage-lifecycle-management-concepts.md)와 같은 다른 Blob service 기능과의 통합도 제공 합니다. 

### <a name="lifecycle-management"></a>수명 주기 관리

수명 주기 관리에서 새 blobIndexMatch를 규칙 필터로 사용 하 여 데이터를 냉각기 계층으로 이동 하거나 blob에 적용 되는 인덱스 태그를 기준으로 데이터를 삭제할 수 있습니다. 이렇게 하면 규칙을 보다 세부적으로 지정할 수 있으며, 지정 된 태그 조건과 일치 하는 경우에만 데이터를 이동 하거나 삭제할 수 있습니다.

태그가 지정 된 데이터에 대 한 작업을 적용 하기 위해 수명 주기 규칙에 있는 독립 실행형 필터 집합으로 blob 인덱스 일치를 설정할 수 있습니다. 또는 접두사 일치와 blob 인덱스 일치를 결합 하 여 보다 구체적인 데이터 집합을 일치 시킬 수 있습니다. 수명 주기 규칙 처리에 여러 필터를 적용 하는 것은 모든 필터 조건이 일치 하는 경우에만 동작이 적용 되도록 논리적 AND 연산입니다. 

다음 샘플 수명 주기 관리 규칙은 ' videofiles ' 컨테이너의 블록 blob에 적용 되 고 데이터는의 blob 인덱스 태그 조건과 일치 하는 경우에만 보관 저장소에 적용 됩니다 ```"Status" = 'Processed' AND "Source" == 'RAW'``` .

# <a name="portal"></a>[포털](#tab/azure-portal)
![Azure Portal의 수명 주기 관리에 대 한 Blob 인덱스 일치 규칙 예제](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)
   
# <a name="json"></a>[JSON](#tab/json)
```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```
---

## <a name="permissions-and-authorization"></a>권한 및 권한 부여

다음 방법 중 하나를 사용 하 여 Blob 인덱스에 대 한 액세스 권한을 부여할 수 있습니다.

- RBAC (역할 기반 액세스 제어)를 사용 하 여 Azure Active Directory (Azure AD) 보안 주체에 사용 권한을 부여 합니다. Microsoft에서는 Azure AD를 사용 하 여 보안을 강화 하 고 사용 편의성을 권장 합니다. Blob 작업에 Azure AD를 사용 하는 방법에 대 한 자세한 내용은 [Azure Active Directory를 사용 하 여 blob 및 큐에 대 한 액세스 권한 부여](../common/storage-auth-aad.md)
- SAS (공유 액세스 서명)를 사용 하 여 blob 인덱스에 대 한 액세스를 위임 합니다. 공유 액세스 서명에 대한 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../common/storage-sas-overview.md)를 참조하세요.
- 계정 액세스 키를 사용 하 여 공유 키로 작업에 권한을 부여 합니다. 자세한 내용은 [공유 키를 사용하여 권한 부여](/rest/api/storageservices/authorize-with-shared-key)를 참조하세요.

Blob 인덱스 태그는 blob 데이터에 대 한 하위 리소스입니다. Blob 읽기 또는 쓰기에 대 한 사용 권한이 있는 사용자 또는 SAS 토큰은 blob 인덱스 태그에 액세스할 수 없습니다. 

### <a name="role-based-access-control"></a>역할 기반 Access Control 
[AAD id](../common/storage-auth-aad.md) 를 사용 하는 호출자에 게 blob 인덱스 태그에 대해 작동 하는 다음 사용 권한이 부여 될 수 있습니다. 

|   Blob 작업   |  RBAC 작업   |
|---------------------|----------------|
| 태그로 Blob 찾기  | Microsoft. Storage/storageAccounts/blobServices/컨테이너/b l o b/필터/작업 |
| Blob 태그 설정         | Microsoft. Storage/storageAccounts/blobServices/컨테이너/b l o b/태그/쓰기 | 
| Blob 태그 가져오기         | Microsoft. Storage/storageAccounts/blobServices/컨테이너/b l o b/태그/읽기 |

태그를 작동 하려면 기본 blob 데이터와 별도의 추가 권한이 필요 합니다. 저장소 Blob 데이터 참가자 역할에는 세 가지 권한이 모두 부여 됩니다. 저장소 Blob 데이터 판독기에는 태그별로 Blob 찾기 및 Blob 태그 가져오기 권한만 부여 됩니다.

### <a name="sas-permissions"></a>SAS 권한 
[SAS (공유 액세스 서명)](../common/storage-sas-overview.md) 를 사용 하는 호출자에 게 blob 태그에 대해 작동 하는 범위 지정 권한이 부여 될 수 있습니다.
#### <a name="blob-sas"></a>Blob SAS
Blob 인덱스 태그에 대 한 액세스를 허용 하기 위해 Blob service SAS에서 다음 사용 권한을 부여할 수 있습니다. Blob 읽기 및 쓰기 권한 만으로는 해당 인덱스 태그를 읽거나 쓸 수 없습니다.

|  사용 권한  |  URI 기호  | 허용되는 작업 |
|--------------|--------------|--------------------|
|  인덱스 태그  |      t         | Blob에 대 한 blob 인덱스 태그 가져오기 및 설정 |

#### <a name="container-sas"></a>컨테이너 SAS
Blob 태그를 필터링 할 수 있도록 컨테이너 서비스 SAS에서 다음 사용 권한을 부여할 수 있습니다.  Blob 목록 권한은 인덱스 태그로 필터링 blob을 허용 하기에 충분 하지 않습니다.

|  사용 권한  |  URI 기호  | 허용되는 작업 |
|--------------|--------------|--------------------|
| 인덱스 태그     |      f       | Blob 인덱스 태그를 사용 하 여 Blob 찾기 | 

## <a name="choosing-between-metadata-and-blob-index-tags"></a>메타 데이터와 Blob 인덱스 태그 중에서 선택 
Blob 인덱스 태그와 메타 데이터는 모두 blob 리소스와 함께 임의의 사용자 정의 키/값 속성을 저장 하는 기능을 제공 합니다. 모두 blob의 내용을 반환 하거나 변경 하지 않고 직접 검색 하 고 설정할 수 있습니다. 메타 데이터와 인덱스 태그를 모두 활용할 수 있습니다.

그러나 Blob 인덱스 태그만 자동으로 인덱싱되어 네이티브 blob service에서 쿼리할 수 있습니다. [Azure Search](../../search/search-blob-ai-integration.md)와 같은 별도의 서비스를 사용 하지 않는 경우에는 메타 데이터를 고유 하 게 인덱싱하고 쿼리할 수 없습니다. 또한 blob 인덱스 태그에는 기본 blob 데이터와는 별개의 읽기/필터링 및 쓰기에 대 한 추가 권한이 있습니다. 메타 데이터는 blob과 동일한 권한을 사용 하며 GetBlob 또는 GetBlobProperties 작업에서 HTTP 헤더로 반환 됩니다. Blob 인덱스 태그는 [Microsoft에서 관리](../common/storage-service-encryption.md) 하는 키를 사용 하 여 미사용에서 암호화 되는 반면 메타 데이터는 blob 데이터에 지정 된 동일한 암호화 키를 사용 하 여 미사용에 암호화 됩니다. 

다음 표에는 메타 데이터와 Blob 인덱스 태그의 차이점이 요약 되어 있습니다.

|              |   메타데이터   |   Blob 인덱스 태그  |
|--------------|--------------|--------------------|
| **제한**         | 숫자 제한은 없습니다. 총 8kb 대/소문자 구분 안 함 | blob 당 최대 10 개 태그 태그 당 768 바이트 대/소문자 구분 |
| **업데이트**      | 보관 계층에서는 허용 되지 않습니다. SetBlobMetadata는 기존 메타 데이터를 모두 바꿉니다. SetBlobMetadata는 blob의 마지막 수정 시간을 변경 합니다. | 모든 액세스 계층에 대해 허용 됨 SetBlobTags는 기존의 모든 태그를 바꿉니다. SetBlobTags는 blob의 마지막 수정 시간을 변경 하지 않습니다. |
| **스토리지**        | Blob 데이터와 함께 저장 됩니다. |  Blob 데이터에 대 한 하위 리소스 | 
| **인덱싱 & 쿼리** | N/A가 고유 합니다. Azure Search와 같은 별도의 서비스를 사용 해야 합니다. | 예, blob 저장소에 기본 제공 되는 기본 인덱싱 및 쿼리 기능 |
| **암호화** | Blob 데이터에 사용 되는 것과 동일한 암호화 키를 사용 하 여 미사용 암호화 |  Microsoft에서 관리 하는 암호화 키를 사용 하 여 미사용 암호화 |
| **가격**   | 메타 데이터의 크기는 blob에 대 한 저장소 비용에 포함 됩니다. |    인덱스 태그별 별 고정 비용 | 
| **헤더 응답** | GetBlob 및 GetBlobProperties에서 헤더로 반환 된 메타 데이터 | GetBlob 또는 GetBlobProperties에서 반환 된 TagCount GetBlobTags 및 ListBlobs 에서만 반환 된 태그 |
| **권한**  |    Blob 데이터에 대 한 읽기 또는 쓰기 권한이 메타 데이터로 확장 됩니다. |    태그를 읽거나 필터링 하거나 쓰려면 추가 권한이 필요 합니다. |

## <a name="pricing"></a>가격 책정
Blob 인덱스 가격은 현재 공개 미리 보기 상태 이며 일반 공급으로 변경 될 수 있습니다. 고객은 저장소 계정 내의 총 blob 인덱스 태그 수에 대 한 요금이 청구 되며 해당 월의 평균입니다. 인덱싱 엔진에 대 한 비용은 없습니다. SetBlobTags, GetBlobTags 및 FindBlobsByTags에 대 한 요청은 각 작업 유형에 따라 청구 됩니다. [자세한 내용은 블록 Blob 가격 책정을](https://azure.microsoft.com/pricing/details/storage/blobs/)참조 하세요.

## <a name="regional-availability-and-storage-account-support"></a>지역 가용성 및 저장소 계정 지원

현재 Blob 인덱스는 HNS (계층적 네임 스페이스)를 사용 하지 않는 범용 v2 (GPv2) 계정 에서만 사용할 수 있습니다. GPV1 (범용) 계정은 지원 되지 않지만 GPv1 계정을 GPv2 계정으로 업그레이드할 수 있습니다. 스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.

공개 미리 보기에서 Blob 인덱스는 현재 다음 select 지역 에서만 사용할 수 있습니다.
- 캐나다 중부
- 캐나다 동부
- 프랑스 중부
- 프랑스 남부

시작 하려면 [Blob 인덱스를 활용 하 여 데이터 관리 및 검색](storage-blob-index-how-to.md)을 참조 하세요.

> [!IMPORTANT]
> 이 문서의 조건 섹션을 참조 하세요. 미리 보기에 등록 하려면이 문서의 구독 등록 단원을 참조 하세요. 저장소 계정에서 Blob 인덱스를 사용 하려면 먼저 구독을 등록 해야 합니다.

### <a name="register-your-subscription-preview"></a>구독 등록 (미리 보기)
Blob 인덱스는 공개 미리 보기 상태 이므로이 기능을 사용 하려면 먼저 구독을 등록 해야 합니다. 요청을 제출하려면 다음 PowerShell 또는 CLI 명령을 실행합니다.

#### <a name="register-by-using-powershell"></a>PowerShell을 사용 하 여 등록
```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Azure CLI를 사용 하 여 등록
```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues-preview"></a>조건 및 알려진 문제 (미리 보기)
이 섹션에서는 현재 Blob 인덱스의 공개 미리 보기의 알려진 문제 및 조건을 설명 합니다. 대부분의 미리 보기와 마찬가지로,이 기능은 동작 변경으로 인해 GA에 도달할 때까지 프로덕션 워크 로드에 사용 하면 안 됩니다.

-   미리 보기의 경우 미리 보기 영역에서 저장소 계정에 대 한 Blob 인덱스를 사용 하려면 먼저 구독을 등록 해야 합니다.
-   GPv2 계정만 현재 미리 보기에서 지원 됩니다. Blob, BlockBlobStorage 및 HNS 사용 DataLake Gen2 계정은 현재 Blob 인덱스에서 지원 되지 않습니다. GPv1 계정은 지원 되지 않습니다.
-   인덱스 태그가 있는 페이지 blob 업로드는 현재 태그를 유지 하지 않습니다. 페이지 blob을 업로드 한 후 태그를 설정 해야 합니다.
-   필터링이 단일 컨테이너로 범위가 지정 된 경우 @container 필터 식의 모든 인덱스 태그가 같음 검사 (키 = 값) 인 경우에만를 전달할 수 있습니다. 
-   AND 조건과 함께 range 연산자를 사용 하는 경우 동일한 인덱스 태그 키 이름 (Age > ' 013 ' 및 Age < ' 100 ')만 지정할 수 있습니다.
-   버전 관리 및 Blob 인덱스는 현재 지원 되지 않습니다. Blob 인덱스 태그는 버전에 대해 유지 되지만 현재 blob 인덱스 엔진에는 전달 되지 않습니다.
-   계정 장애 조치 (failover)는 현재 지원 되지 않습니다. 장애 조치 (failover) 후 blob 인덱스가 제대로 업데이트 되지 않을 수 있습니다.
-   현재 수명 주기 관리는 Blob 인덱스가 일치 하는 같음 검사만 지원 합니다.
-   CopyBlob은 원본 blob에서 새 대상 blob으로 blob 인덱스 태그를 복사 하지 않습니다. 복사 작업 중에 대상 blob에 적용 하려는 태그를 지정할 수 있습니다. 
- 현재 대상 blob에 적용 된 태그가 있는 다른 저장소 계정에서 CopyBlob (비동기 복사)를 수행 하면 blob 인덱스 엔진이 필터 집합에서 blob 및 해당 태그를 반환 하지 않습니다. URL (동기화 복사)의 CopyBlob을 임시에 사용 하는 것이 좋습니다.
-   태그는 스냅숏 생성 시 유지 됩니다. 그러나 스냅숏의 수준을 올리는 것은 현재 지원 되지 않으며 태그 집합이 비어 있을 수 있습니다.

## <a name="faq"></a>FAQ

### <a name="can-blob-index-help-me-filter-and-query-content-inside-my-blobs"></a>Blob 인덱스는 내 blob 내에서 콘텐츠를 필터링 하 고 쿼리 하는 데 도움이 되나요? 
아니요, Blob 인덱스 태그는 원하는 blob을 찾는 데 도움이 될 수 있습니다. Blob 내에서 검색 해야 하는 경우 쿼리 가속 또는 Azure Search을 사용 합니다.

### <a name="are-there-any-special-considerations-regarding-blob-index-tag-values"></a>Blob 인덱스 태그 값에 대 한 특별 한 고려 사항이 있나요?
Blob 인덱스 태그는 문자열 데이터 형식만 지원 하 고 쿼리는 사전순으로 정렬 된 결과를 반환 합니다. 숫자의 경우 숫자를 0으로 패딩 하는 것이 좋습니다. 날짜 및 시간의 경우 ISO 8601 규격 형식으로 저장 하는 것이 좋습니다.

### <a name="are-blob-index-tags-and-azure-resource-manager-tags-related"></a>Blob 인덱스 태그와 Azure Resource Manager 태그가 관련 되어 있나요?
아니요, Azure Resource Manager 태그는 구독, 리소스 그룹, 저장소 계정 등의 제어 평면 리소스를 구성 하는 데 도움이 됩니다. Blob 인덱스 태그는 저장소 계정 내의 blob과 같은 데이터 평면 리소스에 대 한 개체 관리 및 검색 기능을 제공 합니다.

## <a name="next-steps"></a>다음 단계

Blob 인덱스를 활용 하는 방법에 대 한 예는 [Blob 인덱스를 활용 하 여 데이터 관리 및 찾기](storage-blob-index-how-to.md)를 참조 하세요.

[수명 주기 관리](storage-lifecycle-management-concepts.md) 에 대해 알아보고 Blob 인덱스 일치를 사용 하 여 규칙을 설정 합니다.

