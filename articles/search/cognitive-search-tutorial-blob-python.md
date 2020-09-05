---
title: '자습서: Azure Blob을 통한 Python 및 AI'
titleSuffix: Azure Cognitive Search
description: Jupyter Python 노트북 및 Azure Cognitive Search REST API를 사용하여 Blob 스토리지의 콘텐츠에 대한 텍스트를 추출하고 자연어를 처리하는 예제를 단계별로 살펴봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 06/12/2020
ms.custom: devx-track-python
ms.openlocfilehash: 39891b69cdb8e7f392657514d255f5f85b3eba60
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936030"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>자습서: Python 및 AI를 사용하여 Azure Blob에서 검색 가능한 콘텐츠 생성

Azure Blob Storage에 비정형 텍스트 또는 이미지가 있는 경우 [AI 보강 파이프라인](cognitive-search-concept-intro.md)은 정보를 추출하여 전체 텍스트 검색 또는 지식 마이닝 시나리오에 유용한 새 콘텐츠를 만들 수 있습니다. 파이프라인에서 이미지를 처리할 수 있지만, 이 Python 자습서에서는 텍스트에 중점을 두고 언어 감지 및 자연어 처리를 적용하여 쿼리, 패싯 및 필터에 활용할 수 있는 새로운 필드를 만듭니다.

이 자습서에서는 Python 및 [Search REST API](/rest/api/searchservice/)를 사용하여 다음 작업을 수행합니다.

> [!div class="checklist"]
> * Azure Blob Storage에서 PDF, HTML, DOCX 및 PPTX와 같은 전체 문서(비정형 텍스트)로 시작합니다.
> * 텍스트를 추출하고, 언어를 감지하고, 엔터티를 인식하고, 핵심 구를 검색하는 파이프라인을 정의합니다.
> * 출력(원시 콘텐츠 및 파이프라인에서 생성된 이름-값 쌍)을 저장할 인덱스를 정의합니다.
> * 파이프라인을 실행하여 변환 및 분석을 시작하고 인덱스를 생성하고 로드합니다.
> * 전체 텍스트 검색과 풍부한 쿼리 구문을 사용하여 결과를 검색합니다.

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 엽니다.

## <a name="prerequisites"></a>필수 구성 요소

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3.7](https://www.anaconda.com/distribution/#download-section)
+ [만들기](search-create-service-portal.md) 또는 [기존 검색 서비스 찾기](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> 이 자습서에서는 체험 서비스를 사용할 수 있습니다. 체험 검색 서비스에서는 인덱스, 인덱서 및 데이터 원본이 각각 3개로 제한됩니다. 이 자습서에서는 각각을 하나씩 만듭니다. 시작하기 전에 새 리소스를 수용할 수 있는 공간이 서비스에 있는지 확인하세요.

## <a name="download-files"></a>파일 다운로드

1. 이 [OneDrive 폴더](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)를 열고, 왼쪽 위 모서리에서 **다운로드**를 클릭하여 파일을 컴퓨터에 복사합니다. 

1. 마우스 오른쪽 단추로 Zip 파일을 클릭하고, **압축 풀기**를 선택합니다. 다양한 형식의 14개 파일이 있습니다. 이 연습에서는 7개 파일을 사용합니다.

## <a name="1---create-services"></a>1 - 서비스 만들기

이 자습서에서는 Azure Cognitive Search를 인덱싱 및 쿼리에 사용하고 AI 보강을 위해 백 엔드의 Cognitive Services를 사용하고 Azure Blob Storage를 사용하여 데이터를 제공합니다. 이 자습서는 Cognitive Services에 인덱서당 하루 20개의 트랜잭션을 무료로 할당하기 때문에, 검색 및 스토리지 서비스만 만들면 됩니다.

가능하면, 근접성과 관리 효율성을 위해 두 가지 모두를 동일한 지역과 리소스 그룹에 만듭니다. 실제로 Azure Storage 계정은 모든 지역에 있을 수 있습니다.

### <a name="start-with-azure-storage"></a>Azure Storage 시작

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, **+ 리소스 만들기**를 클릭합니다.

1. *스토리지 계정*을 검색하고, Microsoft의 스토리지 계정 제품을 선택합니다.

   ![스토리지 계정 만들기](media/cognitive-search-tutorial-blob/storage-account.png "Storage 계정 만들기")

1. [기본 사항] 탭의 필수 항목은 다음과 같습니다. 다른 모든 항목에는 기본값을 적용합니다.

   + **리소스 그룹**. 기존 서비스를 선택하거나 새 서비스를 만들지만 동일한 그룹을 모든 서비스에 사용하여 전체적으로 관리할 수 있습니다.

   + **스토리지 계정 이름**. 동일한 유형의 여러 리소스가 있을 수 있다고 생각되면 유형 및 지역별로 구분할 수 있는 이름(예: *blobstoragewestus*)을 사용합니다. 

   + **위치** - 가능하면 Azure Cognitive Search 및 Cognitive Services에 사용되는 것과 동일한 위치를 선택합니다. 단일 위치에는 대역폭 요금이 부과되지 않습니다.

   + **계정 종류**. 기본값인 *StorageV2(범용 v2)* 를 선택합니다.

1. **검토 + 만들기**를 클릭하여 서비스를 만듭니다.

1. 서비스가 만들어지면 **리소스로 이동**을 클릭하여 [개요] 페이지를 엽니다.

1. **Blob** 서비스를 클릭합니다.

1. **+ 컨테이너**를 클릭하여 컨테이너를 만들고, 이름을 *cog-search-demo*로 지정합니다.

1. *cog-search-demo*를 선택한 다음, **업로드**를 클릭하여 다운로드 파일을 저장한 폴더를 엽니다. 이미지가 아닌 파일을 모두 선택합니다. 7개의 파일이 있어야 합니다. **확인**을 클릭하여 업로드합니다.

   ![샘플 파일 업로드](media/cognitive-search-tutorial-blob/sample-files.png "샘플 파일 업로드")

1. Azure Storage를 나가기 전에 Azure Cognitive Search에서 연결을 만들 수 있도록 연결 문자열을 가져옵니다. 

   1. 스토리지 계정의 [개요] 페이지로 돌아갑니다(*blobstragewestus*를 예로 사용했음). 
   
   1. 왼쪽 탐색 창에서 **액세스 키**를 선택하고, 연결 문자열 중 하나를 복사합니다. 

   연결 문자열은 다음 예제와 비슷한 URL입니다.

      ```http
      DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. 연결 문자열을 메모장에 저장합니다. 이는 나중에 데이터 원본 연결을 설정할 때 필요합니다.

### <a name="cognitive-services"></a>Cognitive Services

AI 보강은 자연어와 이미지 처리를 위한 Text Analytics 및 Computer Vision을 포함한 Cognitive Services를 통해 지원됩니다. 실제 프로토타입 또는 프로젝트를 완료하는 것이 목표라면 이 시점에서 Cognitive Services를 인덱싱 작업에 연결할 수 있도록 Azure Cognitive Search와 동일한 지역에 프로비저닝합니다.

이 자습서는 7개의 트랜잭션만 사용하므로 Azure Cognitive Search는 인덱서 실행당 20개의 체험 트랜잭션을 Cognitive Services에 연결할 수 있기 때문에 리소스 프로비저닝을 건너뛸 수 있습니다. 체험 할당으로 충분합니다. 대규모 프로젝트의 경우 종량제 S0 계층에서 Cognitive Services를 프로비저닝할 계획입니다. 자세한 내용은 [Cognitive Services 연결](cognitive-search-attach-cognitive-services.md)을 참조하세요.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

세 번째 구성 요소는 [포털에서 만들](search-create-service-portal.md) 수 있는 Azure Cognitive Search입니다. 이 연습은 체험 계층을 사용하여 완료할 수 있습니다. 

Azure Blob 스토리지와 마찬가지로 잠시 시간을 내어 액세스 키를 수집합니다. 또한 요청을 구조화하기 시작할 때 각 요청을 인증하는 데 사용되는 엔드포인트 및 관리 API 키를 제공해야 합니다.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Azure Cognitive Search용 관리 API 키와 URL을 가져옵니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 검색 서비스의 이름을 확인합니다. 엔드포인트 URL을 검토하여 서비스 이름을 확인할 수 있습니다. 엔드포인트 URL이 `https://mydemo.search.windows.net`인 경우 서비스 이름은 `mydemo`입니다.

2. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

   쿼리 키도 가져옵니다. 쿼리 요청은 읽기 전용 액세스로 발급하는 것이 좋습니다.

   ![서비스 이름과 관리자 및 쿼리 키 확인](media/search-get-started-nodejs/service-name-and-keys.png)

모든 요청에서 서비스에 보내는 각 요청의 헤더마다 API 키가 필요합니다. 유효한 키는 요청을 보내는 애플리케이션과 이 요청을 처리하는 서비스 간에 요청별로 신뢰를 설정합니다.

## <a name="2---start-a-notebook"></a>2 - 노트북 시작

다음 지침을 사용하여 노트북을 만들거나, 완성된 노트북을 [Azure-Search-python-samples repo](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)에서 다운로드합니다.

Anaconda Navigator를 사용하여 Jupyter Notebook을 시작하고 새로운 Python 3 Notebook을 만듭니다.

Notebook에서 이 스크립트를 실행하여 JSON 작업과 HTTP 요청 작성에 사용되는 라이브러리를 로드합니다.

```python
import json
import requests
from pprint import pprint
```

동일한 노트북에서, 데이터 원본, 인덱스, 인덱서 및 기술 세트의 이름을 정의합니다. 이 자습서의 이름을 설정하려면 이 스크립트를 실행합니다.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

다음 스크립트에서 검색 서비스에 대한 자리 표시자(YOUR-SEARCH-SERVICE-NAME)와 관리자 API 키에 대한 자리 표시자(YOUR-ADMIN-API-KEY)를 바꾼 후 이를 실행하여 검색 서비스 엔드포인트를 설정합니다.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2020-06-30'
}
```

## <a name="3---create-the-pipeline"></a>3 - 파이프라인 만들기

Azure Cognitive Search에서 AI 처리는 인덱싱(또는 데이터 수집) 중에 발생합니다. 이 연습 부분에서는 데이터 원본, 인덱스 정의, 기술 세트, 인덱서의 네 가지 개체를 만듭니다. 

### <a name="step-1-create-a-data-source"></a>1단계: 데이터 소스 만들기

[데이터 원본 개체](/rest/api/searchservice/create-data-source)는 파일이 포함된 Blob 컨테이너에 대한 연결 문자열을 제공합니다.

다음 스크립트에서는 자리 표시자 YOUR-BLOB-RESOURCE-CONNECTION-STRING을 이전 단계에서 만든 BLOB의 연결 문자열로 바꿉니다. 컨테이너의 자리 표시자 텍스트를 바꿉니다. 그런 다음, 스크립트를 실행하여 `cogsrch-py-datasource`라는 데이터 원본을 만듭니다.

```python
# Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
        "connectionString": datasourceConnectionString
    },
    "container": {
        "name": "<YOUR-BLOB-CONTAINER-NAME>"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

요청이 성공을 확인하는 201 상태 코드를 반환해야 합니다.

Azure Portal의 검색 서비스 대시보드 페이지에서 **데이터 원본** 목록에 cogsrch-py-datasource가 표시되는지 확인합니다. **새로 고침**을 클릭하여 페이지를 업데이트합니다.

![포털의 데이터 원본 타일](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "포털의 데이터 원본 타일")

### <a name="step-2-create-a-skillset"></a>2단계: 기술 집합 만들기

이 단계에서는 데이터에 적용할 보강 세트를 정의합니다. 각 보강 단계를 *기술*이라고 부르고, 보강 단계 집합을 *기술 집합*이라고 부릅니다. 이 자습서에서는 다음과 같은 [기본 제공 인식 기술](cognitive-search-predefined-skills.md)을 기술 세트에 사용합니다.

+ [엔터티 인식](cognitive-search-skill-entity-recognition.md): Blob 컨테이너의 콘텐츠에서 조직 이름을 추출합니다.

+ [언어 감지](cognitive-search-skill-language-detection.md): 콘텐츠의 언어를 식별합니다.

+ [텍스트 분할](cognitive-search-skill-textsplit.md): 핵심 구 추출 기술을 호출하기 전에 큰 콘텐츠를 작은 청크로 분할합니다. 핵심 구 추출은 50,000자 이하의 입력을 허용합니다. 일부 샘플 파일은 이 제한에 맞게 분할해야 합니다.

+ [핵심 구 추출](cognitive-search-skill-keyphrases.md): 상위 핵심 구를 추출합니다. 

`cogsrch-py-skillset`라는 기술 세트를 만들려면 다음 스크립트를 실행합니다.

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", 
                    "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "languageCode"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "textSplitMode": "pages",
            "maximumPageLength": 4000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/pages/*",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", 
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyPhrases"
                }
            ]
        }
    ]
}

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

요청이 성공을 확인하는 201 상태 코드를 반환해야 합니다.

각 페이지에서 핵심 구 추출 기술이 적용됩니다. 컨텍스트를 `"document/pages/*"`로 설정하여 (문서의 각 페이지에 대한) 문서/페이지 배열의 각 멤버에 대해 이 보강자를 실행합니다.

각 기술은 문서의 콘텐츠에서 실행됩니다. 처리하는 동안 Azure Cognitive Search는 각 문서를 해독하여 다른 파일 형식의 콘텐츠를 읽습니다. 원본 파일에서 발견된 텍스트는 각 문서당 하나씩 `content` 필드에 배치됩니다. 따라서 입력을 `"/document/content"`로 설정합니다.

기술 집합의 그래픽 표현은 아래와 같습니다.

![기술 세트 이해](media/cognitive-search-tutorial-blob/skillset.png "기술 세트 이해")

출력을 인덱스에 매핑할 수도 있고, 다운스트림 기술의 입력으로 사용할 수도 있고, 언어 코드처럼 둘 다 할 수도 있습니다. 인덱스에서 언어 코드는 필터링에 유용합니다. 입력으로써의 언어 코드는 단어 분리에 대한 언어적 규칙을 알려주기 위해 텍스트 분석 기술에 사용됩니다.

기술 집합 기본에 대한 자세한 내용은 [기술 집합을 정의하는 방법](cognitive-search-defining-skillset.md)을 참조하세요.

### <a name="step-3-create-an-index"></a>3단계: 인덱스 만들기

이 섹션에서는 검색 가능한 인덱스에 포함할 필드를 지정하고 각 필드의 검색 특성을 설정하여 인덱스 스키마를 정의합니다. 필드에는 형식이 있으며 필드가 사용되는 방식(검색 가능, 정렬 가능 등)을 결정하는 특성을 가질 수 있습니다. 인덱스의 필드 이름은 원본의 필드 이름을 동일하게 일치시키는 데 필요하지 않습니다. 이후 단계에서 인덱서의 필드 매핑을 원본-대상 연결 필드에 추가할 것입니다. 이 단계에서는 검색 애플리케이션과 관련된 필드 명명 규칙을 사용하여 인덱스를 정의합니다.

이 연습에서는 다음 필드와 필드 형식을 사용합니다.

| 필드 이름: | id         | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| 필드 형식: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

`cogsrch-py-index`라는 인덱스를 만들려면 이 스크립트를 실행합니다.

```python
# Create an index
index_payload = {
    "name": index_name,
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": "true",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false",
            "sortable": "true"
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": "false",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "languageCode",
            "type": "Edm.String",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "keyPhrases",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "organizations",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "sortable": "false",
            "filterable": "false",
            "facetable": "false"
        }
    ]
}

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

요청이 성공을 확인하는 201 상태 코드를 반환해야 합니다.

인덱스 정의에 대한 자세한 내용은 [인덱스 만들기(Azure Cognitive Search REST API)](/rest/api/searchservice/create-index)를 참조하세요.

### <a name="step-4-create-and-run-an-indexer"></a>4단계: 인덱서 만들기 및 실행

[인덱서](/rest/api/searchservice/create-indexer)는 파이프라인을 구동합니다. 지금까지 만든 세 가지 구성 요소(데이터 원본, 기술 세트, 인덱스)는 인덱서에 대한 입력입니다. Azure Cognitive Search에서 인덱서를 만드는 것은 전체 파이프라인을 이동시키는 이벤트입니다. 

이러한 객체를 인덱서에 서로 연결하려면 필드 매핑을 정의해야 합니다.

+ `"fieldMappings"`는 기술 세트보다 먼저 처리되며, 데이터 원본의 원본 필드를 인덱스의 대상 필드로 매핑합니다. 필드 이름과 유형이 양쪽 끝에서 동일하면 매핑이 필요 없습니다.

+ `"outputFieldMappings"`는 기술 세트 이후에 처리되며, 문서 크래킹 또는 보강을 통해 만들기 전에는 존재하지 않는 `"sourceFieldNames"`를 참조합니다. `"targetFieldName"`은 인덱스의 필드입니다.

입력을 출력에 연결하는 것 외에도, 필드 매핑을 사용하여 데이터 구조를 평면화할 수 있습니다. 자세한 내용은 [검색 가능한 인덱스에 보강된 필드를 매핑하는 방법](cognitive-search-output-field-mapping.md)을 참조하세요.

`cogsrch-py-indexer`라는 인덱스를 만들려면 이 스크립트를 실행합니다.

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

요청은 201 상태 코드를 곧 반환해야 하지만 처리가 완료되는 데 몇 분 정도 걸릴 수 있습니다. 데이터 세트는 작지만 이미지 분석과 같은 분석 기술은 컴퓨팅을 많이 사용하고 시간이 오래 걸립니다.

[인덱서 상태를 모니터링](#check-indexer-status)하여 인덱서가 실행 중이거나 완료되는 시점을 확인할 수 있습니다.

> [!TIP]
> 인덱서를 만들면 파이프라인이 호출됩니다. 데이터 액세스, 입력 및 출력 매핑 또는 작업 순서에 문제가 있으면 이 단계에 나타납니다. 코드 또는 스크립트를 변경하고 파이프라인을 다시 실행하려면 먼저 개체를 삭제해야 합니다. 자세한 내용은 [다시 설정하고 다시 실행](#reset)을 참조하세요.

#### <a name="about-the-request-body"></a>요청 본문 정보

이 스크립트는 `"maxFailedItems"`를 -1로 설정합니다. 이 값은 데이터를 가져오는 동안에는 오류를 무시하라고 인덱싱 엔진에 지시합니다. 데모 데이터 원본에는 문서가 몇 개 없기 때문에 이렇게 하면 도움이 됩니다. 데이터 원본의 크기가 큰 경우에는 0보다 큰 값으로 설정해야 합니다.

구성 매개 변수의 `"dataToExtract":"contentAndMetadata"` 문도 확인합니다. 이 명령문은 다른 파일 형식의 콘텐츠 및 각 파일과 관련된 메타데이터를 추출하도록 인덱서에 지시합니다.

콘텐츠가 추출되면 데이터 원본에서 찾은 이미지의 텍스트를 추출하도록 `imageAction`을 설정할 수 있습니다. `"imageAction":"generateNormalizedImages"` 구성은 OCR 기술 및 텍스트 병합 기술과 결합되어 이미지에서 텍스트(예: 트래픽 중지 신호의 "중지"라는 단어)를 추출하고 해당 텍스트를 콘텐츠 필드의 일부로 포함시키도록 인덱서에 지시합니다. 이 동작은 문서에 포함된 이미지(예: PDF 내 이미지)와 데이터 원본의 이미지(예: JPG 파일) 모두에 적용됩니다.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - 인덱싱 모니터링

인덱서가 정의되면 사용자가 요청을 제출할 때 인덱서가 자동으로 실행됩니다. 사용자가 정의한 인식 기술에 따라 인덱싱이 예상보다 오래 걸릴 수 있습니다. 인덱서 처리가 완료되었는지 확인하려면 다음 스크립트를 실행합니다.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

응답에서 `"lastResult"`의 `"status"` 및 `"endTime"` 값을 모니터링합니다. 정기적으로 스크립트를 실행하여 상태를 확인합니다. 인덱서가 완료된 경우 status가 "success"로 설정되고, "endTime"이 지정되며, 응답에 보강 과정에서 발생한 오류와 경고가 포함됩니다.

![인덱서가 생성됩니다.](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "인덱서가 생성됩니다.")

경고는 일부 원본 파일 및 기술 조합에서 일반적이며 항상 문제를 나타내는 것은 아닙니다. 많은 경고는 심각하지 않습니다. 예를 들어 텍스트가 없는 JPEG 파일을 인덱싱하면 다음 스크린샷에 경고가 표시됩니다.

![예제 인덱서 경고](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "예제 인덱서 경고")

## <a name="5---search"></a>5 - 검색

인덱싱이 완료되면 개별 필드의 콘텐츠를 반환하는 쿼리를 실행합니다. 기본적으로 Azure Cognitive Search는 상위 50개 결과를 반환합니다. 샘플 데이터가 작기 때문에 기본값으로 충분합니다. 그러나 더 큰 데이터 집합으로 작업할 경우 쿼리 문자열에 더 많은 결과를 반환하는 매개 변수를 포함해야 합니다. 자세한 지침은 [Azure Cognitive Search에서 결과를 페이징하는 방법](search-pagination-page-layout.md)을 참조하세요.

확인 단계로 모든 필드를 표시하는 인덱스 정의를 가져옵니다.

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

결과는 다음 예와 유사하게 나타납니다. 스크린샷은 응답의 일부만 보여줍니다.

![모든 필드에 대한 인덱스 쿼리](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "모든 필드에 대한 인덱스 쿼리")

출력은 각 필드의 이름, 형식 및 특성이 포함된 인덱스 스키마입니다.

`organizations`처럼 단일 필드의 모든 콘텐츠를 반환하도록 `"*"`에 대한 두 번째 쿼리를 제출합니다.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

결과는 다음 예와 유사하게 나타납니다. 스크린샷은 응답의 일부만 보여줍니다.

![조직 콘텐츠의 쿼리 인덱스](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "인덱스를 쿼리하여 조직의 콘텐츠 반환")

이 연습에서는 `content`, `languageCode`, `keyPhrases` 및 `organizations` 등의 추가 필드에 대해 이 작업을 반복합니다. 쉼표로 구분된 목록을 사용하여 `$select`를 통해 여러 필드를 반환할 수 있습니다.

쿼리 문자열의 복잡성 및 길이에 따라 GET 또는 POST를 사용할 수 있습니다. 자세한 내용은 [REST API를 사용한 쿼리](/rest/api/searchservice/search-documents)를 참조하세요.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>다시 설정하고 다시 실행

개발의 초기 실험 단계에서 설계 반복에 대한 가장 실용적인 방법은 Azure Cognitive Search에서 개체를 삭제하고 코드에서 이를 다시 작성하도록 허용하는 것입니다. 리소스 이름은 고유합니다. 개체를 삭제하면 동일한 이름을 사용하여 개체를 다시 만들 수 있습니다.

포털을 사용하여 인덱스, 인덱서, 데이터 원본 및 기술 세트를 삭제할 수 있습니다. 인덱서를 삭제할 때 필요한 경우 인덱스, 기술 세트 및 데이터 원본을 동시에 선택적으로 삭제할 수 있습니다.

![검색 개체 삭제](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "포털에서 검색 개체 삭제")

스크립트를 사용하여 삭제할 수도 있습니다. 다음 스크립트는 기술 세트를 삭제하는 방법을 보여줍니다. 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

삭제 성공 시 상태 코드 204가 반환됩니다.

## <a name="takeaways"></a>핵심 내용

이 자습서에서는 데이터 원본, 기술 집합, 인덱스 및 인덱서라고 하는 구성 요소를 만들어서 보강된 인덱싱 파이프라인을 빌드하는 기본 단계를 살펴보았습니다.

기술 세트 정의 및 입/출력을 통해 기술을 서로 연결하는 방법과 함께 [기본 제공 기술](cognitive-search-predefined-skills.md)을 소개했습니다. 인덱서 정의의 `outputFieldMappings`는 보강된 값을 Azure Cognitive Search 서비스의 파이프라인에서 검색 가능한 인덱스로 라우팅하는 데 필요하다는 것도 배웠습니다.

마지막으로, 결과를 테스트하고 추가 반복을 위해 시스템을 다시 설정하는 방법을 배웠습니다. 인덱스에 대한 쿼리를 실행하면 보강된 인덱싱 파이프라인에서 만든 출력이 반환된다는 것을 배웠습니다. 이 릴리스에는 내부 구문(시스템에서 만든 보강된 문서)을 보기 위한 메커니즘이 있습니다. 인덱서 상태를 확인하는 방법과 파이프라인을 다시 실행하기 전에 어떤 개체를 삭제해야 하는지도 배웠습니다.

## <a name="clean-up-resources"></a>리소스 정리

사용자 고유의 구독에서 작업하는 경우 프로젝트의 끝에서 더 이상 필요하지 않은 리소스를 제거하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 [모든 리소스] 또는 [리소스 그룹] 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

AI 보강 파이프라인의 모든 개체에 대해 알아보았으면, 기술 세트 정의와 개별적인 기술을 자세히 살펴보겠습니다.

> [!div class="nextstepaction"]
> [기술 세트를 만드는 방법](cognitive-search-defining-skillset.md)