---
title: 인덱서 오류 및 경고
titleSuffix: Azure Cognitive Search
description: 이 문서에서는 Azure Cognitive Search의 AI 보강 중 발생할 수 있는 일반적인 오류 및 경고에 대 한 정보 및 솔루션을 제공 합니다.
manager: nitinme
author: amotley
ms.author: abmotley
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fdae02ca9d3c434a77eb972bfd4b955161bd72c4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935554"
---
# <a name="troubleshooting-common-indexer-errors-and-warnings-in-azure-cognitive-search"></a>Azure Cognitive Search에서 일반적인 인덱서 오류 및 경고 문제 해결

이 문서에서는 Azure Cognitive Search에서 인덱싱 및 AI 보강 중에 발생할 수 있는 일반적인 오류 및 경고에 대 한 정보 및 솔루션을 제공 합니다.

오류 수가 [' Maxfaileditems '](cognitive-search-concept-troubleshooting.md#tip-3-see-what-works-even-if-there-are-some-failures)를 초과 하면 인덱싱이 중지 됩니다. 

인덱서를 통해 이러한 오류를 무시 하 고 "실패 한 문서"를 건너뛰도록 하려면 여기에 설명 된 `maxFailedItems` 대로 및를 업데이트 하는 것이 좋습니다 `maxFailedItemsPerBatch` . [here](/rest/api/searchservice/create-indexer#general-parameters-for-all-indexers)

> [!NOTE]
> 문서 키 (사용 가능한 경우)와 함께 실패 한 각 문서는 인덱서 실행 상태에서 오류로 표시 됩니다. 오류가 허용 되도록 인덱서를 설정한 경우 [인덱스 api](/rest/api/searchservice/addupdate-or-delete-documents) 를 활용 하 여 나중에 수동으로 문서를 업로드할 수 있습니다.

이 문서의 오류 정보는 오류를 해결 하는 데 도움이 될 수 있으므로 인덱싱을 계속할 수 있습니다.

경고는 인덱싱을 중지 하지 않지만 예기치 않은 결과가 발생할 수 있는 조건을 표시 합니다. 작업 수행 여부는 데이터와 시나리오에 따라 달라 집니다.

API 버전부터 `2019-05-06` 항목 수준 인덱서 오류 및 경고는 원인 및 다음 단계를 보다 명확 하 게 이해할 수 있도록 구조화 되어 있습니다. 여기에는 다음 속성이 포함 됩니다.

| 속성 | Description | 예 |
| --- | --- | --- |
| key | 오류 또는 경고의 영향을 받는 문서의 문서 ID입니다. | https: \/ /coromsearch.blob.core.windows.net/jfk-1k/docid-32112954.pdf |
| name | 오류 또는 경고가 발생 한 위치를 설명 하는 작업 이름입니다. 이는 다음과 같은 구조 [category]에 의해 생성 됩니다. [하위 범주]. [resourceType]. ResourceName | DocumentExtraction. mySkillName 프로젝션. n a m e. n a m e. n a m e. n a m e. myIndexName myOutputFieldName |
| message | 오류 또는 경고에 대 한 개략적인 설명입니다. | 웹 Api 요청이 실패 했으므로 기술을 실행할 수 없습니다. |
| 자세히 | 사용자 지정 기술을 실행 하지 못한 경우 WebApi 응답과 같이 문제를 진단 하는 데 도움이 될 수 있는 추가 세부 정보입니다. | `link-cryptonyms-list - Error processing the request record : System.ArgumentNullException: Value cannot be null. Parameter name: source at System.Linq.Enumerable.All[TSource](IEnumerable`1 원본 `2 predicate) at Microsoft.CognitiveSearch.WebApiSkills.JfkWebApiSkills.` , Func ... 스택 추적의 나머지 ... |
| documentationLink | 문제를 디버그 하 고 해결 하기 위한 자세한 정보가 포함 된 관련 설명서에 대 한 링크입니다. 이 링크는 종종이 페이지에서 아래 섹션 중 하나를 가리킵니다. | https://go.microsoft.com/fwlink/?linkid=2106475 |

<a name="could-not-read-document"></a>

## <a name="error-could-not-read-document"></a>오류: 문서를 읽을 수 없습니다.

인덱서가 데이터 소스에서 문서를 읽을 수 없습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

| 이유 | 세부 정보/예제 | 해결 방법 |
| --- | --- | --- |
| 여러 문서에서 일치 하지 않는 필드 형식 | "값의 형식이 열 형식과 일치 하지 않습니다. `'{47.6,-122.1}'`작성자 열에 저장할 수 없습니다.  필요한 형식은 JArray입니다. "  "데이터 형식 nvarchar를 float로 변환 하는 동안 오류가 발생 했습니다."  "Nvarchar 값 ' 12 개월 '을 데이터 형식 int로 변환 하지 못했습니다."  "식을(를) 데이터 형식 int(으)로 변환하는 중 산술 오버플로 오류가 발생했습니다." | 각 필드의 형식이 서로 다른 문서에서 동일한 지 확인 합니다. 예를 들어 첫 번째 문서 `'startTime'` 필드가 날짜/시간이 고 두 번째 문서에서 문자열이 면이 오류가 발생 합니다. |
| 데이터 원본의 기본 서비스에서 발생 한 오류 | (Cosmos DB) `{"Errors":["Request rate is large"]}` | 저장소 인스턴스를 확인 하 여 정상 상태 인지 확인 합니다. 크기 조정/분할을 조정 해야 할 수 있습니다. |
| 일시적인 문제 | 서버에서 결과를 받을 때 전송 수준 오류가 발생했습니다. (공급자: TCP 공급자, 오류: 0-기존 연결이 원격 호스트에 의해 강제로 끊겼습니다.) | 예기치 않은 연결 문제가 발생 하는 경우도 있습니다. 나중에 인덱서를 통해 문서를 다시 실행 해 보세요. |

<a name="could-not-extract-document-content"></a>

## <a name="error-could-not-extract-content-or-metadata-from-your-document"></a>오류: 문서에서 콘텐츠나 메타 데이터를 추출할 수 없습니다.
Blob 데이터 원본이 있는 인덱서가 문서 (예: PDF 파일)에서 콘텐츠나 메타 데이터를 추출할 수 없습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

| 이유 | 세부 정보/예제 | 해결 방법 |
| --- | --- | --- |
| blob이 크기 제한을 초과 합니다. | 문서는 `'150441598'` 바이트 이며 `'134217728'` 현재 서비스 계층의 문서 추출에 대 한 최대 크기 바이트를 초과 합니다. | [blob 인덱싱 오류](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob의 콘텐츠 형식이 지원 되지 않습니다. | 문서에 지원 되지 않는 콘텐츠 형식이 있습니다. `'image/png'` | [blob 인덱싱 오류](search-howto-indexing-azure-blob-storage.md#dealing-with-errors) |
| blob이 암호화 되어 있습니다. | 문서를 처리할 수 없습니다. 암호화 또는 암호로 보호할 수 있습니다. | Blob [설정을](search-howto-indexing-azure-blob-storage.md#controlling-which-parts-of-the-blob-are-indexed)사용 하 여 blob를 건너뛸 수 있습니다. |
| 일시적인 문제 | "Blob을 처리 하는 동안 오류가 발생 했습니다. 요청이 중단 되었습니다. 요청이 취소 되었습니다." "처리 하는 동안 문서 시간이 초과 되었습니다." | 예기치 않은 연결 문제가 발생 하는 경우도 있습니다. 나중에 인덱서를 통해 문서를 다시 실행 해 보세요. |

<a name="could-not-parse-document"></a>

## <a name="error-could-not-parse-document"></a>오류: 문서를 구문 분석할 수 없습니다.
인덱서는 데이터 소스에서 문서를 읽었지만 문서 콘텐츠를 지정 된 필드 매핑 스키마로 변환 하는 동안 문제가 발생 했습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

| 이유 | 세부 정보/예제 | 해결 방법 |
| --- | --- | --- |
| 문서 키가 없습니다. | 문서 키를 누락 하거나 비워 둘 수 없습니다. | 모든 문서에 유효한 문서 키가 있는지 확인 합니다. 문서 키는 [인덱스 정의](/rest/api/searchservice/create-index#request-body)의 일부로 ' key ' 속성을 설정 하 여 결정 됩니다. 특정 문서에서 ' 키 '로 플래그가 지정 된 속성을 찾을 수 없으면 인덱서는이 오류를 내보냅니다. |
| 문서 키가 잘못 되었습니다. | 문서 키는 1024 자를 초과할 수 없습니다. | 유효성 검사 요구 사항에 맞게 문서 키를 수정 합니다. |
| 필드에 필드 매핑을 적용할 수 없습니다. | 필드에 매핑 함수를 적용할 수 없습니다 `'functionName'` `'fieldName'` . 배열은 null 일 수 없습니다. 매개 변수 이름: 바이트 | 인덱서에 정의 된 [필드 매핑을](search-indexer-field-mappings.md) 두 번 확인 하 고 실패 한 문서의 지정 된 필드의 데이터와 비교 합니다. 필드 매핑 또는 문서 데이터를 수정 해야 할 수도 있습니다. |
| 필드 값을 읽을 수 없습니다. | 인덱스에서 열 값을 읽을 수 없습니다 `'fieldName'` `'fieldIndex'` . 서버에서 결과를 받을 때 전송 수준 오류가 발생했습니다. (공급자: TCP 공급자, 오류: 0 - 현재 현결이 원격 호스트에 의해 강제로 끊겼습니다.) | 이러한 오류는 일반적으로 데이터 원본의 기본 서비스와 관련 된 예기치 않은 연결 문제로 인해 발생 합니다. 나중에 인덱서를 통해 문서를 다시 실행 해 보세요. |

<a name="Could not map output field '`xyz`' to search index due to deserialization problem while applying mapping function '`abc`'"></a>

## <a name="error-could-not-map-output-field-xyz-to-search-index-due-to-deserialization-problem-while-applying-mapping-function-abc"></a>오류: `xyz` 매핑 함수 ' '을 (를) 적용 하는 동안 deserialization 문제로 인해 ' ' 출력 필드를 검색 인덱스에 매핑할 수 없습니다. `abc`
출력 데이터가 사용 중인 매핑 함수에 대해 잘못 된 형식 이므로 출력 매핑이 실패 했을 수 있습니다. 예를 들어 이진 데이터에 Base64Encode mapping 함수를 적용 하면이 오류가 생성 됩니다. 이 문제를 해결 하려면 매핑 함수를 지정 하지 않고 인덱서를 다시 실행 하거나 매핑 함수가 출력 필드 데이터 형식과 호환 되는지 확인 합니다. 자세한 내용은 [출력 필드 매핑](cognitive-search-output-field-mapping.md) 을 참조 하세요.

<a name="could-not-execute-skill"></a>

## <a name="error-could-not-execute-skill"></a>오류: 기술을 실행할 수 없습니다.
인덱서가 기술에서 기술을 실행할 수 없습니다.

| 이유 | 세부 정보/예제 | 해결 방법 |
| --- | --- | --- |
| 일시적인 연결 문제 | 일시적인 오류가 발생 했습니다. 나중에 다시 시도하세요. | 예기치 않은 연결 문제가 발생 하는 경우도 있습니다. 나중에 인덱서를 통해 문서를 다시 실행 해 보세요. |
| 잠재적 제품 버그 | 예기치 않은 오류가 발생했습니다. | 이것은 알 수 없는 오류 클래스를 나타내며 제품 버그가 있음을 의미할 수 있습니다. 도움을 받으려면 [지원 티켓](https://ms.portal.azure.com/#create/Microsoft.Support) 을 제출 하십시오. |
| 실행 중에 기술에 오류가 발생 했습니다. | (병합 기술) 하나 이상의 오프셋 값이 잘못 되었으며 구문 분석할 수 없습니다. 항목이 텍스트 끝에 삽입 되었습니다. | 오류 메시지의 정보를 사용 하 여 문제를 해결할 수 있습니다. 이러한 종류의 오류는 해결할 조치가 필요 합니다. |

<a name="could-not-execute-skill-because-the-web-api-request-failed"></a>

## <a name="error-could-not-execute-skill-because-the-web-api-request-failed"></a>오류: Web API 요청이 실패 했으므로 기술을 실행할 수 없습니다.
웹 API에 대 한 호출이 실패 하 여 기술 실행이 실패 했습니다. 일반적으로이 오류 클래스는 사용자 지정 기술을 사용할 때 발생 합니다 .이 경우에는 사용자 지정 코드를 디버그 하 여 문제를 해결 해야 합니다. 기본 제공 기술에서 오류가 발생 한 경우 문제 해결에 도움이 필요 하면 오류 메시지를 참조 하십시오.

이 문제를 디버그 하는 동안이 기술에 대 한 [기술 입력 경고](#warning-skill-input-was-invalid) 에 주의 해야 합니다. 인덱서가 예기치 않은 입력을 전달 하 고 있으므로 웹 API 끝점이 실패할 수 있습니다.

<a name="could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>

## <a name="error-could-not-execute-skill-because-web-api-skill-response-is-invalid"></a>오류: Web API 기술 응답이 잘못 되었기 때문에 기술을 실행할 수 없습니다.
웹 API 호출에서 잘못 된 응답을 반환 하 여 기술 실행에 실패 했습니다. 일반적으로이 오류 클래스는 사용자 지정 기술을 사용할 때 발생 합니다 .이 경우에는 사용자 지정 코드를 디버그 하 여 문제를 해결 해야 합니다. 기본 제공 기술에서 오류가 발생 한 경우 [지원 티켓](https://ms.portal.azure.com/#create/Microsoft.Support) 을 제출 하 여 지원을 받으세요.

<a name="skill-did-not-execute-within-the-time-limit"></a>

## <a name="error-skill-did-not-execute-within-the-time-limit"></a>오류: 기술은 시간 제한 내에서 실행 되지 않았습니다.
이 오류 메시지가 나타날 수 있는 두 가지 경우에는 각각 다르게 처리 되어야 합니다. 이 오류를 반환한 기술에 따라 아래 지침을 따르세요.

### <a name="built-in-cognitive-service-skills"></a>기본 제공 인식 서비스 기술
언어 검색, 엔터티 인식 또는 OCR과 같은 여러 가지 내장 인식 기술이 인식 서비스 API 끝점에 의해 지원 됩니다. 이러한 끝점에 일시적인 문제가 있어 요청 시간이 초과 되는 경우가 있습니다. 일시적인 문제의 경우 대기를 제외 하 고 다시 시도 하는 경우를 제외 하 고는 해결 방법이 없습니다. 이를 완화 하려면 [일정에 따라 실행](search-howto-schedule-indexers.md)되도록 인덱서를 설정 하는 것이 좋습니다. 예약 된 인덱싱이 중단 된 위치에서 선택 합니다. 일시적인 문제를 해결 하는 것으로 가정 하면 인덱싱 및 인식 기술 처리는 다음에 예약 된 실행을 계속할 수 있습니다.

기본 제공 인식 기술에 대 한 동일한 문서에서이 오류가 계속 표시 되는 경우 [지원 티켓](https://ms.portal.azure.com/#create/Microsoft.Support) 을 제출 하 여 지원을 받으세요.

### <a name="custom-skills"></a>사용자 지정 기술
만든 사용자 지정 기술에 시간 초과 오류가 발생 하는 경우 몇 가지 작업을 수행해 볼 수 있습니다. 먼저 사용자 지정 기술을 검토 하 고 무한 루프에서 중단 되 고 결과를 일관 되 게 반환 하는지 확인 합니다. 이를 확인 한 후에는 기술 실행 시간을 결정 합니다. `timeout`사용자 지정 기술 정의에 대 한 값을 명시적으로 설정 하지 않은 경우 기본값은 `timeout` 30 초입니다. 30 초가 너무 길어서 기술 실행에 충분 하지 않은 경우 `timeout` 사용자 지정 기술 정의에 더 높은 값을 지정할 수 있습니다. 다음은 시간 제한이 90 초로 설정 된 사용자 지정 기술 정의의 예입니다.

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "uri": "<your custom skill uri>",
        "batchSize": 1,
        "timeout": "PT90S",
        "context": "/document",
        "inputs": [
          {
            "name": "input",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "output",
            "targetName": "output"
          }
        ]
      }
```

매개 변수에 대해 설정할 수 있는 최대값은 `timeout` 230 초입니다.  사용자 지정 기술이 230 초 내에 일관 되 게 실행할 수 없는 경우 `batchSize` 단일 실행 내에서 처리할 문서 수가 줄어들기 때문에 사용자 지정 기술를 줄일 수 있습니다.  이미 `batchSize` 를 1로 설정한 경우에는 230 초 이내에 실행할 수 있도록 기술을 다시 작성 하거나, 단일 사용자 지정 기술에 대 한 실행 시간이 최대 230 초인 경우 여러 사용자 지정 기술로 분할 해야 합니다. 자세한 내용은 [사용자 지정 기술 설명서](cognitive-search-custom-skill-web-api.md) 를 참조 하십시오.

<a name="could-not-mergeorupload--delete-document-to-the-search-index"></a>

## <a name="error-could-not-mergeorupload--delete-document-to-the-search-index"></a>오류: ' '을 (를) 할 수 없습니다. `MergeOrUpload` `Delete`검색 인덱스에 대 한 ' ' 문서

문서를 읽고 처리 했지만 인덱서가 검색 인덱스에 추가할 수 없습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

| 이유 | 세부 정보/예제 | 해결 방법 |
| --- | --- | --- |
| 필드에 너무 많은 용어가 있습니다. | 문서의 용어가 [32 KB 제한](search-limits-quotas-capacity.md#api-request-limits) 보다 큽니다. | 필드가 필터링 가능, 패싯 가능 또는 정렬 가능으로 구성 되지 않도록 하 여이 제한을 피할 수 있습니다.
| 문서가 너무 커서 인덱싱할 수 없습니다. | 문서가 [최대 api 요청 크기](search-limits-quotas-capacity.md#api-request-limits) 보다 큽니다. | [대량 데이터 집합을 인덱싱하는 방법](search-howto-large-index.md)
| 문서에 컬렉션에 너무 많은 개체가 포함 되어 있습니다. | 문서의 컬렉션이 [모든 복합 컬렉션 제한에서 최대 요소](search-limits-quotas-capacity.md#index-limits) 를 초과 합니다. "키가 있는 문서에 `'1000052'` 는 `'4303'` 컬렉션의 개체 (JSON 배열)가 있습니다. 최대 `'3000'` 개체 수는 전체 문서에서 컬렉션에 있을 수 있습니다. 컬렉션에서 개체를 제거 하 고 문서 인덱싱을 다시 시도 하십시오. " | 문서에 있는 복잡 한 컬렉션의 크기를 제한 아래로 줄여서 저장소 사용률을 줄이려면 권장 합니다.
| 쿼리가 나 인덱싱 등의 다른 부하에서 서비스를 하 고 있으므로 대상 인덱스에 연결 하는 데 문제가 있습니다 (재시도 후 지속 됨). | 인덱스 업데이트에 대 한 연결을 설정 하지 못했습니다. 검색 서비스의 부하가 높습니다. | [Search 서비스 확장](search-capacity-planning.md)
| 서비스 업데이트를 위해 검색 서비스를 패치 하 고 있거나 토폴로지를 다시 구성 하는 중입니다. | 인덱스 업데이트에 대 한 연결을 설정 하지 못했습니다. 검색 서비스가 현재 다운 되었거나 검색 서비스에서 전환 중입니다. | [SLA 설명서](https://azure.microsoft.com/support/legal/sla/search/v1_0/) 당 99.9%의 가용성을 위해 3 개 이상의 복제본을 사용 하 여 서비스를 구성 합니다.
| 기본 계산/네트워킹 리소스에서 오류가 발생 했습니다 (드문 경우). | 인덱스 업데이트에 대 한 연결을 설정 하지 못했습니다. 알 수 없는 오류가 발생했습니다. | 실패 상태에서 선택할 수 있도록 [일정에 따라 실행](search-howto-schedule-indexers.md) 되도록 인덱서를 구성 합니다.
| 네트워크 문제로 인해 대상 인덱스에 대해 수행 된 인덱싱 요청이 시간 제한 기간 내에 승인 되지 않았습니다. | 적시에 검색 인덱스에 대 한 연결을 설정할 수 없습니다. | 실패 상태에서 선택할 수 있도록 [일정에 따라 실행](search-howto-schedule-indexers.md) 되도록 인덱서를 구성 합니다. 또한이 오류 조건이 지속 되는 경우 인덱서 [일괄 처리 크기](/rest/api/searchservice/create-indexer#parameters) 를 줄여 보세요.

<a name="could-not-index-document-because-the-indexer-data-to-index-was-invalid"></a>

## <a name="error-could-not-index-document-because-some-of-the-documents-data-was-not-valid"></a>오류: 문서 데이터 중 일부가 잘못 되어 문서를 인덱싱할 수 없습니다.

인덱서는 문서를 읽고 처리 했지만 인덱스 필드의 구성과 인덱서를 통해 추출 및 처리 된 데이터의 불일치로 인해 검색 인덱스에 추가할 수 없습니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

| 이유 | 세부 정보/예제
| --- | ---
| 인덱서에 의해 추출 된 필드의 데이터 형식이 해당 대상 인덱스 필드의 데이터 모델과 호환 되지 않습니다. | ' 888 ' 키가 있는 문서의 '_data_' 데이터 필드에 잘못 된 값 ' Type ' Edm. 문자열 ' '이 (가) 있습니다. 필요한 형식은 ' Collection (Edm. String) ' 이었습니다. |
| 문자열 값에서 JSON 엔터티를 추출 하지 못했습니다. | '_Data_' 필드의 ' ' 형식 ' ' 값을 JSON 개체로 구문 분석할 수 없습니다. 오류: ' 값을 구문 분석 한 후 예기치 않은 문자가 발견 되었습니다. ' '. 경로 '_path_', 줄 1, 위치 3162. ' |
| 문자열 값에서 JSON 엔터티 컬렉션을 추출 하지 못했습니다.  | '_Data_' 필드의 ' ' 형식 ' ' 값을 JSON 배열로 구문 분석할 수 없습니다. 오류: ' 값을 구문 분석 한 후 예기치 않은 문자가 발견 되었습니다. ' '. 경로 ' [0] ', 줄 1, 위치 27. ' |
| 소스 문서에서 알 수 없는 형식이 검색 되었습니다. | 알 수 없는 '_unknown_' 형식을 인덱싱할 수 없습니다. |
| 원본 문서에서 지리적 위치에 대해 호환 되지 않는 표기법이 사용 되었습니다. | WKT POINT 문자열 리터럴은 지원 되지 않습니다. 대신 GeoJson point 리터럴을 사용 하세요. |

이러한 모든 경우에 대해 [지원 되는 데이터 형식](/rest/api/searchservice/supported-data-types) 및 [인덱서의 데이터 형식 맵](/rest/api/searchservice/data-type-map-for-indexers-in-azure-search) 을 참조 하 여 인덱스 스키마를 올바르게 작성 하 고 적절 한 [인덱서 필드 매핑을](search-indexer-field-mappings.md)설정 했는지 확인 합니다. 오류 메시지에는 불일치의 원인을 추적 하는 데 도움이 되는 세부 정보가 포함 됩니다.

## <a name="error-integrated-change-tracking-policy-cannot-be-used-because-table-has-a-composite-primary-key"></a>오류: 테이블에 복합 기본 키가 있으므로 통합 변경 내용 추적 정책을 사용할 수 없습니다.

이는 SQL 테이블에 적용 되며, 일반적으로 키가 복합 키로 정의 되거나 테이블이 Azure Search 인덱스가 아닌 SQL 인덱스에서와 같이 고유 클러스터형 인덱스를 정의한 경우에 발생 합니다. 주된 이유는 키 특성이 [고유 클러스터형 인덱스](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?view=sql-server-ver15)의 경우 복합 기본 키로 수정 되기 때문입니다. 이 경우에는 SQL 테이블이 고유 클러스터형 인덱스를 포함 하지 않는지 확인 하거나 키 필드를 중복 값이 없는 필드에 매핑해야 합니다.

<a name="could-not-process-document-within-indexer-max-run-time"></a>

## <a name="error-could-not-process-document-within-indexer-max-run-time"></a>오류: 인덱서 최대 실행 시간 내에서 문서를 처리할 수 없습니다.

이 오류는 인덱서가 허용 된 실행 시간 내에 데이터 원본에서 단일 문서 처리를 완료할 수 없는 경우에 발생 합니다. 기술력과를 사용 하는 경우 [최대 실행 시간이](search-limits-quotas-capacity.md#indexer-limits) 짧아집니다. 이 오류가 발생 하는 경우 maxFailedItems가 0 이외의 값으로 설정 된 경우 인덱서는 이후 실행 시 문서를 우회 하 여 인덱싱이 진행 되도록 합니다. 문서를 건너뛸 수 없는 경우 또는이 오류가 일관 되 게 표시 되는 경우 단일 인덱서 실행 내에서 부분 진행을 수행할 수 있도록 문서를 더 작은 문서로 분할 하는 것이 좋습니다.

이름 = "할 수 없는 프로젝트><</a>

## <a name="error-could-not-project-document"></a>오류: 문서를 프로젝션 할 수 없습니다.

이 오류는 인덱서가 [데이터를 기술 자료 저장소로 프로젝션](knowledge-store-projection-overview.md) 하려고 하는데 오류가 발생 했을 때 발생 합니다.  이 오류는 일관적이 고 수정할 수 수 있습니다. 또는이를 해결 하기 위해 대기 하 고 다시 시도 해야 하는 프로젝션 출력 싱크에 일시적인 오류일 수 있습니다.  다음은 알려진 오류 상태와 가능한 해결 방법의 집합입니다.

| 이유 | 세부 정보/예제 | 해결 방법 |
| --- | --- | --- |
| 컨테이너에서 프로젝션 blob을 업데이트할 수 없습니다. `'blobUri'``'containerName'` |지정한 컨테이너가 없습니다. | 인덱서는 지정 된 컨테이너가 이전에 생성 되었는지 여부를 확인 하 고 필요한 경우이를 만들지만 인덱서 실행 당 한 번만이 확인을 수행 합니다. 이 오류는이 단계 후에 항목이 컨테이너를 삭제 했음을 의미 합니다.  이 오류를 해결 하려면 저장소 계정 정보를 그대로 두고 인덱서가 완료 될 때까지 기다린 후 인덱서를 다시 실행 합니다. |
| 컨테이너에서 프로젝션 blob을 업데이트할 수 없습니다. `'blobUri'``'containerName'` |전송 연결에 데이터를 쓸 수 없음: 원격 호스트에서 기존 연결을 강제로 닫았습니다. | 이는 Azure Storage의 일시적인 오류로 예상 되므로 인덱서를 다시 실행 하 여 해결 해야 합니다. 이 오류가 지속적으로 발생 하면 추가 조사를 위해 [지원 티켓](https://ms.portal.azure.com/#create/Microsoft.Support) 을 제출 하십시오.  |
| 테이블의 행을 업데이트할 수 없습니다. `'projectionRow'``'tableName'` | 서버가 사용 중입니다. | 이는 Azure Storage의 일시적인 오류로 예상 되므로 인덱서를 다시 실행 하 여 해결 해야 합니다. 이 오류가 지속적으로 발생 하면 추가 조사를 위해 [지원 티켓](https://ms.portal.azure.com/#create/Microsoft.Support) 을 제출 하십시오.  |

<a name="could-not-execute-skill-because-a-skill-input-was-invalid"></a>

## <a name="warning-skill-input-was-invalid"></a>경고: 기술 입력이 잘못 되었습니다.
기술에 대 한 입력이 누락 되었거나, 형식이 잘못 되었거나, 잘못 된 경우 경고 메시지는 다음과 같은 영향을 표시 합니다.
1) 기술을 실행할 수 없습니다.
2) 기술이 실행 되었지만 예기치 않은 결과가 발생할 수 있습니다.

인식 기술에는 필수 입력과 선택적 입력이 있습니다. 예를 들어 [키 구 추출 기술](cognitive-search-skill-keyphrases.md) 에는 선택적 입력이 없는 두 개의 필수 입력이 있습니다 `text` `languageCode` . 사용자 지정 기술 입력은 모두 선택적 입력으로 간주 됩니다.

필요한 입력이 없거나 입력이 올바른 형식이 아닌 경우에는 기술이 생략 되 고 경고가 생성 됩니다. 건너뛴 스킬은 출력을 생성 하지 않으므로 다른 기술에서 건너뛴 기술의 출력을 사용 하는 경우 추가 경고를 생성할 수 있습니다.

선택적 입력이 없으면 기술이 계속 실행 되지만 누락 된 입력으로 인해 예기치 않은 출력이 생성 될 수 있습니다.

두 경우 모두 데이터의 모양으로 인해이 경고가 발생할 수 있습니다. 예를 들어, 및 필드를 사용 하는 사용자에 대 한 정보가 포함 된 문서가 있는 경우 `firstName` `middleName` `lastName` 에 대 한 항목이 없는 문서가 있을 수 있습니다 `middleName` . `middleName`파이프라인의 기술에 대 한 입력으로 전달 하는 경우이 기술 입력에 일부 시간이 없을 수 있습니다. 데이터 및 시나리오를 평가 하 여이 경고의 결과로 작업이 필요한 지 여부를 확인 해야 합니다.

누락 된 입력에 대 한 기본값을 제공 하려는 경우에는 [조건부 기술을](cognitive-search-skill-conditional.md) 사용 하 여 기본값을 생성 한 다음 [조건 기술](cognitive-search-skill-conditional.md) 출력을 기술 입력으로 사용할 수 있습니다.


```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'en'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

| 이유 | 세부 정보/예제 | 해결 방법 |
| --- | --- | --- |
| 기술 입력의 형식이 잘못 되었습니다. | "필요한 기술 입력이 예상 된 형식이 아닙니다 `String` . 이름: `text` , 원본: `/document/merged_content` . "  "필요한 기술 입력이 예상 된 형식이 아닙니다. 이름: `text` , 원본: `/document/merged_content` . "  "배열이 아닌 경우에는 반복할 수 없습니다 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` ."  " `0` 비 배열에서 선택할 수 없습니다. `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` " | 특정 기술에는 특정 형식의 입력이 필요 합니다. 예를 들어 [감정 기술은](cognitive-search-skill-sentiment.md) `text` 문자열이 될 것으로 예상 합니다. 입력에서 문자열이 아닌 값을 지정 하는 경우 기술은 실행 되지 않으며 출력을 생성 하지 않습니다. 데이터 집합에 형식에서 입력 값이 균일 한지 확인 하거나 [사용자 지정 웹 API 기술을](cognitive-search-custom-skill-web-api.md) 사용 하 여 입력을 전처리 합니다. 배열에 대 한 기술을 반복 하는 경우 기술 컨텍스트와 입력이 `*` 올바른 위치에 있는지 확인 합니다. 일반적으로 컨텍스트와 입력 소스는 모두 `*` 배열에 대해로 끝나야 합니다. |
| 기술 입력이 누락 되었습니다. | "필요한 기술 입력이 누락 되었습니다. 이름: `text` , 원본: `/document/merged_content` "" 누락 값 `/document/normalized_images/0/imageTags` "  " `0` 길이 배열로 선택할 수 없습니다 `/document/pages` `0` ." | 모든 문서에서이 경고가 발생 하는 경우 입력 경로에 철자가 있을 가능성이 높습니다 .이 경우에는 경로에서 속성 이름 대/소문자 구분 여부를 다시 확인 하 `*` 고, 데이터 원본의 문서에서 필요한 입력을 제공 해야 합니다. |
| 기술 언어 코드 입력이 잘못 되었습니다. | 기술 입력 `languageCode` 에 다음과 같은 언어 코드가 있습니다 `X,Y,Z` .이 중 하나 이상이 잘못 되었습니다. | [아래](cognitive-search-common-errors-warnings.md#skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid) 자세히 보기 |

<a name="skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>

## <a name="warning--skill-input-languagecode-has-the-following-language-codes-xyz-at-least-one-of-which-is-invalid"></a>경고: 기술 입력 ' languageCode '에 다음 언어 코드 ' X, Y, Z '가 하나 이상 잘못 되었습니다.
다운스트림 기술에 대 한 선택적 입력으로 전달 된 값 중 하나 이상이 `languageCode` 지원 되지 않습니다. 이 문제는 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) 의 출력을 후속 기술로 전달 하 고 출력이 해당 다운스트림 기술에서 지원 되는 것 보다 많은 언어로 구성 된 경우에 발생할 수 있습니다.

데이터 집합이 모두 한 언어로 표시 되는 경우 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) `languageCode` 해당 기술에 대 한 언어가 지원 되는 것으로 가정 하면 LanguageDetectionSkill 및 기술 입력을 제거 하 고 `defaultLanguageCode` 해당 기술에 대 한 기술 매개 변수를 대신 사용 해야 합니다.

데이터 집합에 여러 언어가 포함 되어 있으므로 [LanguageDetectionSkill](cognitive-search-skill-language-detection.md) 및 입력이 필요 하다 고 생각 되 면 `languageCode` 텍스트를 다운스트림 기술로 전달 하기 전에 지원 되지 않는 언어를 사용 하 여 텍스트를 필터링 하는 [ConditionalSkill](cognitive-search-skill-conditional.md) 를 추가 하는 것이 좋습니다.  EntityRecognitionSkill에 대 한 예는 다음과 같습니다.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'de' || $(/document/language) == 'en' || $(/document/language) == 'es' || $(/document/language) == 'fr' || $(/document/language) == 'it'" },
        { "name": "whenTrue", "source": "/document/content" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "supportedByEntityRecognitionSkill" } ]
}
```

이 오류 메시지를 생성할 수 있는 각 기술에 대해 현재 지원 되는 언어에 대 한 몇 가지 참조는 다음과 같습니다.
* [Text Analytics 지원 되는 언어](../cognitive-services/text-analytics/language-support.md) ( [KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md), [EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md), [SentimentSkill](cognitive-search-skill-sentiment.md)및 [PIIDetectionSkill](cognitive-search-skill-pii-detection.md))
* [Translator 지원 언어](../cognitive-services/translator/language-support.md) ( [텍스트 TranslationSkill](cognitive-search-skill-text-translation.md))
* [텍스트 SplitSkill](cognitive-search-skill-textsplit.md) 지원 되는 언어: `da, de, en, es, fi, fr, it, ko, pt`

<a name="skill-input-was-truncated"></a>

## <a name="warning-skill-input-was-truncated"></a>경고: 기술 입력이 잘렸습니다.
인식 기술은 한 번에 분석할 수 있는 텍스트의 길이에 제한이 있습니다. 이러한 기술에 대 한 텍스트 입력이 해당 한도를 초과 하는 경우이 한도에 맞게 텍스트를 자르고 잘린 텍스트에 대해 보강를 수행 합니다. 즉, 기술이 실행 되지만 일부 데이터에 대해서는 실행 되지 않습니다.

아래 예제 LanguageDetectionSkill에서 `'text'` 입력 필드는 문자 제한을 초과 하는 경우이 경고를 트리거할 수 있습니다. 기술 [문서](cognitive-search-predefined-skills.md)에서 기술 입력 한도를 찾을 수 있습니다.

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [...]
  }
```

모든 텍스트를 분석 하려면 [분할 기술을](cognitive-search-skill-textsplit.md)사용 하는 것이 좋습니다.

<a name="web-api-skill-response-contains-warnings"></a>

## <a name="warning-web-api-skill-response-contains-warnings"></a>경고: Web API 기술 응답에 경고가 포함 되어 있습니다.
인덱서가 기술에서 기술을 실행할 수 있었지만 웹 API 요청의 응답이 실행 중에 경고가 발생 했음을 나타냅니다. 경고를 검토 하 여 데이터의 영향을 받는 방법 및 조치가 필요한 지 여부를 파악 합니다.

<a name="the-current-indexer-configuration-does-not-support-incremental-progress"></a>

## <a name="warning-the-current-indexer-configuration-does-not-support-incremental-progress"></a>경고: 현재 인덱서 구성은 증분 진행률을 지원 하지 않습니다.

이 경고는 Cosmos DB 데이터 원본에 대해서만 발생 합니다.

인덱싱하는 동안 증분 진행률은 인덱서 실행이 일시적 오류 또는 실행 시간 제한에 의해 중단되었는지를 확인합니다. 인덱서는 처음부터 전체 컬렉션을 다시 인덱스하는 대신 다음으로 실행할 위치를 선택할 수 있습니다. 대규모 컬렉션을 인덱싱할 때 특히 유용합니다.

완료 되지 않은 인덱싱 작업을 다시 시작 하는 기능은 열을 기준으로 문서를 정렬 하는 경우에 대해 설명 `_ts` 합니다. 인덱서는 타임 스탬프를 사용 하 여 다음에 선택할 문서를 결정 합니다. 열이 `_ts` 없거나 인덱서가 사용자 지정 쿼리를 정렬 하는지 확인할 수 없는 경우 인덱서는 시작 시 시작 되 고이 경고가 표시 됩니다.

이 동작을 재정의 하 여 증분 진행률을 활성화 하 고 구성 속성을 사용 하 여이 경고를 표시 하지 않을 수 있습니다 `assumeOrderByHighWatermarkColumn` .

자세한 내용은 [증분 진행률 및 사용자 지정 쿼리](search-howto-index-cosmosdb.md#IncrementalProgress)를 참조 하세요.

<a name="some-data-was-lost-during projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>

## <a name="warning-some-data-was-lost-during-projection-row-x-in-table-y-has-string-property-z-which-was-too-long"></a>경고: 프로젝션 중 일부 데이터가 손실 되었습니다. ' Y ' 테이블의 ' X ' 행에 너무 긴 문자열 속성 ' Z '가 있습니다.

[Table Storage 서비스](https://azure.microsoft.com/services/storage/tables) 에는 [엔터티 속성](/rest/api/storageservices/understanding-the-table-service-data-model#property-types) 의 크기에 대 한 제한이 있습니다. 문자열은 32000 자이 하 여야 합니다. 문자열 속성이 32000 자 보다 긴 행을 프로젝션 하는 경우 첫 번째 32000 자만 유지 됩니다. 이 문제를 해결 하려면 문자열 속성이 32000 자 보다 긴 행을 프로젝션 하지 않도록 합니다.

<a name="truncated-extracted-text-to-x-characters"></a>

## <a name="warning-truncated-extracted-text-to-x-characters"></a>경고: 추출 된 텍스트가 X 문자로 잘렸습니다.
인덱서는 한 문서에서 추출할 수 있는 텍스트의 크기를 제한 합니다. 이 한도는 가격 책정 계층에 따라 달라 집니다. 무료 계층의 경우 32000 자, 400만 Basic의 경우 64000, 표준 S2의 경우 800만, 표준 S3의 경우 1600만입니다. 잘린 텍스트는 인덱싱되지 않습니다. 이 경고를 방지 하려면 많은 양의 텍스트를 포함 하는 문서를 여러 개의 작은 문서로 분리 해 보세요. 

자세한 내용은 [인덱서 제한](search-limits-quotas-capacity.md#indexer-limits)을 참조 하세요.

<a name="could-not-map-output-field-x-to-search-index"></a>

## <a name="warning-could-not-map-output-field-x-to-search-index"></a>경고: ' X ' 출력 필드를 검색 인덱스에 매핑할 수 없습니다.
존재 하지 않는/null 데이터를 참조 하는 출력 필드 매핑은 각 문서에 대 한 경고를 생성 하 고 빈 인덱스 필드를 생성 합니다. 이 문제를 해결 하려면 출력 필드 매핑 소스 경로에서 가능한 오타를 두 번 확인 하거나 [조건부 기술을](cognitive-search-skill-conditional.md#sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist)사용 하 여 기본값을 설정 합니다. 자세한 내용은 [출력 필드 매핑](cognitive-search-output-field-mapping.md) 을 참조 하세요.

| 이유 | 세부 정보/예제 | 해결 방법 |
| --- | --- | --- |
| 배열이 아닌 경우 반복할 수 없습니다. | "배열이 아닌 경우에는 반복할 수 없습니다 `/document/normalized_images/0/imageCelebrities/0/detail/celebrities` ." | 이 오류는 출력이 배열이 아닌 경우에 발생 합니다. 출력이 배열 이어야 한다고 생각 되 면 표시 된 출력 원본 필드 경로에서 오류를 확인 합니다. 예를 들어 `*` 원본 필드 이름에 누락 또는 추가가 있을 수 있습니다. 이 기술에 대 한 입력이 null 이어서 빈 배열이 생성 될 수도 있습니다. [기술 입력](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid) 에서 비슷한 세부 정보를 찾습니다.    |
| 배열이 아닌를 선택할 수 없습니다. `0` | " `0` 비 배열에서 선택할 수 없습니다 `/document/pages` ." | 이는 기술 출력이 배열을 생성 하지 않고 출력 소스 필드 이름이 배열 인덱스 또는 해당 경로에 있는 경우에 발생할 수 있습니다 `*` . 출력 원본 필드 이름에 제공 된 경로와 표시 된 필드 이름에 대 한 필드 값을 다시 확인 하십시오. [기술 입력](cognitive-search-common-errors-warnings.md#warning-skill-input-was-invalid) 에서 비슷한 세부 정보를 찾습니다.  |

<a name="the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>

## <a name="warning-the-data-change-detection-policy-is-configured-to-use-key-column-x"></a>경고: 데이터 변경 검색 정책이 키 열 ' X '를 사용 하도록 구성 되어 있습니다.
[데이터 변경 검색 정책](/rest/api/searchservice/create-data-source#data-change-detection-policies) 에는 변경을 검색 하는 데 사용 하는 열에 대 한 특정 요구 사항이 있습니다. 이러한 요구 사항 중 하나는 원본 항목이 변경 될 때마다이 열이 업데이트 되는 것입니다. 또 다른 요구 사항은이 열의 새 값이 이전 값 보다 크다는 것입니다. 키 열은 업데이트 마다 변경 되지 않으므로이 요구 사항을 충족 하지 않습니다. 이 문제를 해결 하려면 변경 검색 정책에 대해 다른 열을 선택 합니다.

<a name="document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>

## <a name="warning-document-text-appears-to-be-utf-16-encoded-but-is-missing-a-byte-order-mark"></a>경고: 문서 텍스트가 u t f-16으로 인코딩된 것으로 나타나지만 바이트 순서 표시가 없습니다.

[인덱서 구문 분석 모드](/rest/api/searchservice/create-indexer#blob-configuration-parameters) 는 텍스트를 구문 분석 하기 전에 인코딩하는 방법을 알고 있어야 합니다. 텍스트를 인코딩하는 가장 일반적인 두 가지 방법은 u t f-16과 u t f-8입니다. U t f-8은 각 문자의 길이가 1 바이트에서 4 바이트 사이에 있는 가변 길이 인코딩입니다. U t f-16은 고정 길이 인코딩입니다. 각 문자는 2 바이트 길이입니다. U t f-16에는 "big endian" 및 "little endian"의 두 가지 변형이 있습니다. 텍스트 인코딩은 텍스트 앞의 바이트 시리즈 인 "바이트 순서 표시"에 의해 결정 됩니다.

| Encoding | 바이트 순서 표시 |
| --- | --- |
| UTF-16 Big Endian | 0xFE 0xFF |
| UTF-16 작은 Endian | 0xFF 0xFE |
| UTF-8 | 0xEF 0Xef 0Xef |

바이트 순서 표시가 없는 경우 텍스트는 u t f-8로 인코딩된 것으로 간주 됩니다.

이 경고를 해결 하려면이 blob에 대 한 텍스트 인코딩이 무엇 인지 확인 하 고 적절 한 바이트 순서 표시를 추가 합니다.

<a name="cosmos-db-collection-has-a-lazy-indexing-policy"></a>

## <a name="warning-cosmos-db-collection-x-has-a-lazy-indexing-policy-some-data-may-be-lost"></a>경고: Cosmos DB 컬렉션 ' X '에 지연 인덱싱 정책이 있습니다. 일부 데이터가 손실 될 수 있습니다.

[지연](/azure/cosmos-db/index-policy#indexing-mode) 인덱싱 정책을 사용 하는 컬렉션은 일관 되 게 쿼리할 수 없습니다 .이로 인해 인덱서가 데이터를 누락 하 게 됩니다. 이 경고를 해결 하려면 인덱싱 정책을 일관 되 게 변경 합니다.