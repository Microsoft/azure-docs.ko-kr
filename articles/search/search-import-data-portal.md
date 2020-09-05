---
title: Azure Portal를 사용 하 여 검색 인덱스로 데이터 가져오기
titleSuffix: Azure Cognitive Search
description: Azure Portal에서 데이터 가져오기 마법사를 사용 하 여 azure Vm의 Cosmos DB, Blob storage, table storage, SQL Database, SQL Managed Instance 및 SQL Server에서 Azure 데이터를 크롤링하는 방법에 대해 알아봅니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7cff009d5d1e187e8d0330fadca530b57b3e3d21
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935214"
---
# <a name="import-data-wizard-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 데이터 가져오기 마법사

Azure Portal는 Azure Cognitive Search 대시보드에서 인덱스를 프로토타입 및 로드 하기 위한 **데이터 가져오기** 마법사를 제공 합니다. 이 문서에서는 마법사, 입/출력 및 일부 사용 정보를 사용할 때의 장점과 제한 사항을 설명 합니다. 기본 제공 샘플 데이터를 사용 하 여 마법사를 단계별로 실행 하는 방법에 대 한 실습 지침은 Azure Portal 빠른 시작을 [사용 하 여 Azure Cognitive Search 인덱스 만들기](search-get-started-portal.md) 를 참조 하세요.

이 마법사에서 수행 하는 작업은 다음과 같습니다.

1-지원 되는 Azure 데이터 원본에 연결 합니다.

2-샘플링 원본 데이터를 통해 유추 되는 인덱스 스키마를 만듭니다.

3-필요에 따라 AI 강화을 추가 하 여 콘텐츠 및 구조를 추출 하거나 생성 합니다.

4-마법사를 실행 하 여 개체를 만들고, 데이터를 가져오고, 일정 및 기타 구성 옵션을 설정 합니다.

마법사는 검색 서비스에 저장 된 여러 개체를 출력 합니다 .이 개체는 프로그래밍 방식으로 또는 다른 도구에서 액세스할 수 있습니다.

## <a name="advantages-and-limitations"></a>이점 및 제한 사항

코드를 작성 하기 전에 프로토타입 및 개념 증명 테스트를 위해 마법사를 사용할 수 있습니다. 이 마법사는 외부 데이터 원본에 연결 하 고, 데이터를 샘플링 하 여 초기 인덱스를 만든 다음, Azure Cognitive Search의 인덱스에 JSON 문서로 데이터를 가져옵니다. 

샘플링은 인덱스 스키마를 유추 하 고 몇 가지 제한 사항을 적용 하는 프로세스입니다. 데이터 원본을 만들면 마법사는 문서 샘플을 선택 하 여 데이터 원본의 일부인 열을 결정 합니다. 모든 파일을 읽을 수 있는 것은 아닙니다 .이는 매우 큰 데이터 원본에 몇 시간이 걸릴 수 있기 때문입니다. 문서를 선택할 경우 필드 이름이 나 유형 등의 원본 메타 데이터를 사용 하 여 인덱스 스키마에 필드 컬렉션을 만듭니다. 원본 데이터의 복잡도에 따라 정확성을 위해 초기 스키마를 편집 해야 할 수도 있고 완전성을 위해 확장 해야 할 수도 있습니다. 인덱스 정의 페이지에서 변경 내용을 인라인으로 만들 수 있습니다.

전반적으로, 마법사를 사용 하면 다음과 같은 이점이 있습니다. 요구 사항이 충족 되 면 몇 분 내에 쿼리 가능한 인덱스를 프로토타입으로 만들 수 있습니다. JSON 문서로 데이터를 제공 하는 것과 같은 인덱싱의 일부 복잡성은 마법사에서 처리 됩니다.

알려진 제한 사항은 다음과 같이 요약 됩니다.

+ 마법사는 반복 또는 재사용을 지원 하지 않습니다. 마법사의 각 단계에서 새 index, 기술 및 인덱서 구성을 만듭니다. 데이터 원본만 유지 되 고 마법사 내에서 다시 사용할 수 있습니다. 다른 개체를 편집 하거나 구체화 하려면 REST Api 또는 .NET SDK를 사용 하 여 구조를 검색 하 고 수정 해야 합니다.

+ 원본 콘텐츠는 지원 되는 Azure 데이터 원본에 있어야 합니다.

+ 샘플링은 원본 데이터의 하위 집합에 대 한 것입니다. 대량 데이터 원본의 경우 마법사에서 필드를 누락 시킬 수 있습니다. 샘플링에 충분 하지 않은 경우 스키마를 확장 하거나 유추 된 데이터 형식을 수정 해야 할 수도 있습니다.

+ 포털에 표시 되는 AI 보강은 몇 가지 기본 제공 기술로 제한 됩니다. 

+ 마법사에서 만들 수 있는 [기술 자료 저장소](knowledge-store-concept-intro.md)는 몇 가지 기본 프로젝션으로 제한 됩니다. 마법사에서 만든 보강 문서를 저장 하려는 경우에는 blob 컨테이너와 테이블에 기본 이름 및 구조가 제공 됩니다.

<a name="data-source-inputs"></a>

## <a name="data-source-input"></a>데이터 원본 입력

**데이터 가져오기** 마법사는 azure Cognitive Search 인덱서에 제공 되는 내부 논리를 사용 하 여 외부 데이터 원본에 연결 합니다 .이는 소스를 샘플링 하 고, 메타 데이터를 읽고, 콘텐츠와 구조를 읽고, azure Cognitive Search에 대 한 후속 가져오기에 대 한 JSON으로 콘텐츠를 serialize 하는 데 사용 됩니다.

단일 테이블, 데이터베이스 뷰 또는 동등한 데이터 구조 에서만 가져올 수 있지만 구조에는 계층적 또는 중첩 된 하위 구조체 포함 될 수 있습니다. 자세한 내용은 [복합 형식을 모델링 하는 방법](search-howto-complex-data-types.md)을 참조 하세요.

마법사를 실행 하기 전에이 단일 테이블이 나 뷰를 만든 다음 내용을 포함 해야 합니다. 명확한 이유 때문에 빈 데이터 원본에서 **데이터 가져오기** 마법사를 실행 하는 것은 적합 하지 않습니다.

|  선택 영역 | 설명 |
| ---------- | ----------- |
| **기존 데이터 원본** |검색 서비스에 이미 인덱서가 정의 되어 있는 경우 다시 사용할 수 있는 기존 데이터 원본 정의가 있을 수 있습니다. Azure Cognitive Search에서 데이터 원본 개체는 인덱서 에서만 사용 됩니다. 데이터 원본 개체를 프로그래밍 방식으로 만들거나 **데이터 가져오기** 마법사를 통해 만든 다음 필요에 따라 다시 사용할 수 있습니다.|
| **샘플**| Azure Cognitive Search는 자습서 및 빠른 시작에서 사용 되는 두 개의 기본 제공 샘플 데이터 원본 (부동산 SQL 데이터베이스 및 Cosmos DB에 호스트 된 호텔 데이터베이스)을 제공 합니다. 호텔 샘플을 기반으로 하는 연습은 Azure Portal 빠른 시작 [에서 인덱스 만들기](search-get-started-portal.md) 를 참조 하세요. |
| [**Azure SQL Database 또는 SQL Managed Instance**](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) |읽기 권한이 포함된 데이터베이스 사용자에 대한 서비스 이름, 자격 증명 및 데이터베이스 이름은 페이지에 또는 ADO.NET 연결 문자열을 통해 지정될 수 있습니다. 연결 문자열 옵션을 선택하여 속성을 보거나 사용자 지정합니다. <br/><br/>행 집합을 제공하는 테이블 또는 보기는 페이지에 지정되어야 합니다. 연결에 성공한 후에 이 옵션이 나타나면 선택할 수 있는 드롭다운 목록을 제공합니다.|
| **Azure VM의 SQL Server** |정규화된 서비스 이름, 사용자 ID와 암호 및 데이터베이스를 연결 문자열로 지정합니다. 이 데이터 원본을 사용하려면 연결을 암호화하는 로컬 저장소에 미리 인증서를 설치해야 합니다. 지침은 [Azure Cognitive Search에 대 한 SQL VM 연결](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)을 참조 하세요. <br/><br/>행 집합을 제공하는 테이블 또는 보기는 페이지에 지정되어야 합니다. 연결에 성공한 후에 이 옵션이 나타나면 선택할 수 있는 드롭다운 목록을 제공합니다. |
| [**Azure Cosmos DB**](search-howto-index-cosmosdb.md)|요구 사항은 계정, 데이터베이스 및 컬렉션을 포함합니다. 컬렉션의 모든 문서는 인덱스에 포함됩니다. 행 집합을 평면화 또는 필터링 하는 쿼리를 정의 하거나 쿼리를 비워 둘 수 있습니다. 이 마법사에서는 쿼리가 필요 하지 않습니다.|
| [**Azure Blob Storage**](search-howto-indexing-azure-blob-storage.md) |요구 사항은 스토리지 계정 및 컨테이너를 포함합니다. 필요에 따라 그룹화 목적으로 Blob 이름이 가상 명명 규칙을 따르는 경우 이름의 가상 디렉터리 부분을 컨테이너의 폴더로 지정할 수 있습니다. 자세한 내용은 [Blob Storage 인덱싱](search-howto-indexing-azure-blob-storage.md)을 참조하세요. |
| [**Azure Table Storage**](search-howto-indexing-azure-tables.md) |요구 사항은 스토리지 계정 및 테이블 이름을 포함합니다. 필요에 따라 쿼리를 지정하여 테이블의 하위 집합을 검색할 수 있습니다. 자세한 내용은 [Table Storage 인덱싱](search-howto-indexing-azure-tables.md)을 참조하세요. |

## <a name="wizard-output"></a>마법사 출력

마법사는 내부적으로 다음 개체를 만들고, 구성 하 고, 호출 합니다. 마법사를 실행 한 후 포털 페이지에서 해당 출력을 찾을 수 있습니다. 서비스의 개요 페이지에는 인덱스, 인덱서, 데이터 원본 및 기술력과 목록이 있습니다. 인덱스 정의는 포털의 전체 JSON에서 볼 수 있습니다. 다른 정의의 경우 [REST API](/rest/api/searchservice/) 를 사용 하 여 특정 개체를 가져올 수 있습니다.

| Object | Description | 
|--------|-------------|
| [데이터 원본](/rest/api/searchservice/create-data-source)  | 자격 증명을 포함 하 여 원본 데이터에 대 한 연결 정보를 유지 합니다. 데이터 원본 개체는 인덱서에 독점적으로 사용 됩니다. | 
| [Index](/rest/api/searchservice/create-index) | 전체 텍스트 검색 및 기타 쿼리에 사용 되는 실제 데이터 구조입니다. | 
| [기술 집합](/rest/api/searchservice/create-skillset) | 이미지 파일의 정보를 분석 하 고 추출 하는 것을 포함 하 여 콘텐츠를 조작, 변형 및 셰이핑 하기 위한 전체 지침을 포함 합니다. 매우 간단 하 고 제한 된 구조체를 제외 하 고 보강를 제공 하는 Cognitive Services 리소스에 대 한 참조를 포함 합니다. 필요에 따라 기술 자료 저장소 정의가 포함 될 수도 있습니다.  | 
| [인덱서](/rest/api/searchservice/create-indexer)  | 오류 처리 및 64 인코딩에 대 한 데이터 원본, 대상 인덱스, 선택적 기술, 선택적 일정 및 선택적 구성 설정을 지정 하는 구성 개체입니다. |


## <a name="how-to-start-the-wizard"></a>마법사를 시작 하는 방법

데이터 가져오기 마법사는 서비스 개요 페이지의 명령 모음에서 시작 됩니다.

1. [Azure Portal](https://portal.azure.com)의 대시보드에서 검색 서비스 페이지를 열거나 서비스 목록에서 [서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

2. 맨 위에 있는 서비스 개요 페이지에서 **데이터 가져오기**를 클릭합니다.

   ![포털에서 데이터 가져오기 명령](./media/search-import-data-portal/import-data-cmd2.png "데이터 가져오기 마법사 시작")

Azure Cosmos DB, Azure SQL Database, SQL Managed Instance 및 Azure Blob storage를 비롯 한 다른 Azure 서비스에서 **데이터 가져오기를** 시작할 수도 있습니다. 서비스 개요 페이지의 왼쪽 탐색 창에서 **Azure Cognitive Search 추가** 를 찾습니다.

<a name="index-definition"></a>

## <a name="how-to-edit-or-finish-an-index-schema-in-the-wizard"></a>마법사에서 인덱스 스키마를 편집 하거나 종료 하는 방법

마법사에서 불완전 한 인덱스를 생성 합니다 .이 인덱스는 입력 데이터 원본에서 가져온 문서로 채워집니다. 함수 인덱스의 경우 다음 요소를 정의했는지 확인합니다.

1. 필드 목록이 완성 되었습니까? 샘플링에서 누락 된 새 필드를 추가 하 고 검색 환경에 값을 추가 하지 않거나 [필터 식](search-query-odata-filter.md) 또는 [점수 매기기 프로필](index-add-scoring-profiles.md)에 사용 되지 않는 모든 필드를 제거 합니다.

1. 들어오는 데이터에 적합 한 데이터 형식 입니까? Azure Cognitive Search는 [EDM (엔터티 데이터 모델) 데이터 형식을](/rest/api/searchservice/supported-data-types)지원 합니다. Azure SQL 데이터의 경우 해당 값을 레이아웃 하는 [매핑 차트가](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping) 있습니다. 자세한 배경 정보는 [필드 매핑 및 변환](search-indexer-field-mappings.md)을 참조 하세요.

1. *키*로 사용할 수 있는 필드가 하나 있나요? 이 필드는 Edm 이어야 하며 문서를 고유 하 게 식별 해야 합니다. 관계형 데이터의 경우 기본 키에 매핑될 수 있습니다. Blob의 경우이 될 수 있습니다 `metadata-storage-path` . 필드 값에 공백 또는 대시를 포함하는 경우 **고급 옵션**의 **인덱서 만들기** 단계에서 **Base-64 인코딩 키** 옵션을 설정하여 이러한 문자에 대한 유효성 검사를 비활성화해야 합니다.

1. 특성을 설정 하 여 인덱스에서 필드가 사용 되는 방법을 결정 합니다. 

   특성은 인덱스의 필드에 대 한 물리적 식을 결정 하기 때문에이 단계를 수행 합니다. 나중에 프로그래밍 방식으로 특성을 변경 하려는 경우에는 항상 인덱스를 삭제 하 고 다시 작성 해야 합니다. **검색** 가능 하 고 **검색할** 수 있는 것과 같은 핵심 특성은 [저장소에 미치는 영향을 무시](search-what-is-an-index.md#index-size)합니다. 필터를 사용 하도록 설정 하 고 확인 기를 사용 하면 저장소 요구 사항이 증가 합니다. 
   
   + **검색 가능은** 전체 텍스트 검색을 가능 하 게 합니다. 자유 형식 쿼리 또는 쿼리 식에 사용되는 모든 필드에는 이 특성이 있어야 합니다. **검색 가능**으로 표시한 각 필드에 대해 반전된 인덱스가 만들어집니다.

   + 검색 가능은 검색 결과의 필드 **를 반환 합니다** . 검색 결과에 콘텐츠를 제공하는 모든 필드에는 이 특성이 있어야 합니다. 이 필드를 설정해도 인덱스 크기는 두드러지게 영향을 받지 않습니다.

   + 필터링 가능을 사용 하면 필드를 필터 식에서 **참조할 수 있습니다** . **$filter** 식에 사용되는 모든 필드는 이 특성이 있어야 합니다. 필터 식은 정확한 일치를 위한 것입니다. 텍스트 문자열은 그대로 유지되므로 축자 콘텐츠를 포함하기 위한 추가 스토리지가 필요합니다.

   + **패싯 가능** 는 패싯 탐색에 대해 필드를 사용 하도록 설정 합니다. **필터링 가능**으로도 표시되는 필드만 **패싯 가능**으로 표시될 수 있습니다.

   + **정렬 가능** -정렬에 필드를 사용할 수 있습니다. **$Orderby** 식에 사용되는 모든 필드는 이 특성이 있어야 합니다.

1. [어휘 분석이](search-lucene-query-architecture.md#stage-2-lexical-analysis)필요 한가요? **검색 가능한**Edm의 경우 언어에 대 한 인덱싱 및 쿼리를 원하는 경우 **분석기** 를 설정할 수 있습니다. 

   기본값은 표준 Lucene이지만 불규칙한 명사 및 동사 형태를 확인하는 것과 같은 고급 어휘 처리에 Microsoft의 분석기를 사용하려는 경우 Microsoft 영어를 선택할 수도 있습니다.**** 포털에는 언어 분석기만 지정할 수 있습니다. 사용자 지정 분석기 또는 키워드, 패턴 등과 같은 언어 이외의 분석기를 사용 하 여 프로그래밍 방식으로 수행 해야 합니다. 분석기에 대 한 자세한 내용은 [언어 분석기 추가](search-language-support.md)를 참조 하세요.

1. 자동 완성 또는 제안 된 결과 형식의 형식 미리 기능이 필요 한가요? **확인 기** 을 선택 하 여 선택한 필드에 대해 [미리 쿼리 제안 및 자동 완성](index-add-suggesters.md) 기능을 사용 하도록 설정 합니다. 확인 기는 인덱스에서 토큰화 된 용어 수에 추가 하므로 더 많은 저장소를 사용 합니다.


## <a name="next-steps"></a>다음 단계

마법사의 이점과 제한 사항을 이해 하는 가장 좋은 방법은 단계별로 실행 하는 것입니다. 다음 빠른 시작에서는 각 단계를 안내 합니다.

> [!div class="nextstepaction"]
> [Azure Portal를 사용 하 여 Azure Cognitive Search 인덱스 만들기](search-get-started-portal.md)