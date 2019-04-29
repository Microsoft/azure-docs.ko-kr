---
title: Azure Portal을 사용하여 검색 인덱스로 데이터 가져오기 - Azure Search
description: Azure Portal에서 데이터 가져오기 마법사를 사용하여 Azure VM에 있는 Cosmos DB, Blob Storage, 테이블 스토리지, SQL Database 및 SQL Server에서 Azure 데이터를 크롤링하는 방법을 알아봅니다.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: fcb1e4f32608a1c83b653984dfa066da38e7c451
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871117"
---
# <a name="import-data-wizard-for-azure-search"></a>Azure Search의 데이터 가져오기 마법사

Azure Portal에는 인덱스에 데이터를 로드하기 위한 **데이터 가져오기** 마법사가 Azure Search 대시보드에 제공됩니다. 마법사는 내부적으로 *데이터 원본*, *인덱스* 및 *인덱서*를 구성하고 호출하여 인덱싱 프로세스의 여러 단계를 다음과 같이 자동화합니다. 

* 동일한 Azure 구독에서 외부 데이터 원본에 연결합니다.
* 필요에 따라 구조화되지 않은 데이터에서 텍스트를 추출하기 위해 광학 문자 인식 또는 자연어 처리를 통합합니다.
* 외부 데이터 원본의 데이터 샘플링 및 메타데이터에 따라 인덱스를 생성합니다.
* 검색 가능한 콘텐츠에서 데이터 원본을 크롤링하고, JSON 문서를 직렬화한 후 인덱스로 로드합니다.

마법사는 미리 정의된 인덱스에 연결하거나 기존 인덱서를 실행할 수 없지만, 마법사 내에서 필요한 구조 및 동작을 지원하도록 새 인덱스 또는 인덱서를 구성할 수 있습니다.

Azure Search를 처음 사용하세요? [빠른 시작: 포털 도구를 사용하여 가져오기, 인덱싱 및 쿼리](search-get-started-portal.md)를 단계별로 진행하여 **데이터 가져오기** 및 기본 제공 realestate 샘플 데이터 세트로 가져오기 및 인덱싱을 시험 사용해봅니다.

## <a name="start-importing-data"></a>데이터 가져오기 시작

이 섹션에서는 마법사를 시작하는 방법을 설명하고 각 단계의 개략적인 개요를 제공합니다.

1. [Azure Portal](https://portal.azure.com)의 대시보드에서 검색 서비스 페이지를 열거나 서비스 목록에서 [서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

2. 맨 위에 있는 서비스 개요 페이지에서 **데이터 가져오기**를 클릭합니다.

   ![포털의 데이터 가져오기 명령](./media/search-import-data-portal/import-data-cmd2.png "데이터 가져오기 마법사 시작")

   > [!NOTE]
   > Azure Cosmos DB, Azure SQL Database 및 Azure Blob 스토리지를 비롯한 다른 Azure 서비스에서 **데이터 가져오기**를 시작할 수 있습니다. 서비스 개요 페이지의 왼쪽 탐색 창에서 **Azure Search 추가**를 찾아보세요.

3. 마법사에서 **데이터에 연결** 단계가 열립니다. 여기서 가져오기에 사용할 외부 데이터 원본을 선택할 수 있습니다. 이 단계에 대해 알아야 하는 몇 가지 사항이 있습니다. 자세한 내용은 [데이터 원본 입력](#data-source-inputs) 섹션을 읽어보세요.

   ![포털에서 데이터 가져오기 마법사](./media/search-import-data-portal/import-data-wizard-startup.png "Azure Search의 데이터 가져오기 마법사")

4. 다음은 이미지 파일에 텍스트의 OCR(광학 문자 인식)을 포함하려고 하거나 구조화되지 않은 데이터에 대한 텍스트 분석을 수행하려는 경우를 위한 **Cognitive Search 추가** 단계입니다. Cognitive Services의 AI 알고리즘은 이 작업을 위해 끌어온 것입니다. 이 단계는 다음 두 부분으로 구분됩니다.
  
   첫째, Azure Search 기술 세트에 [Cognitive Services 리소스를 연결](cognitive-search-attach-cognitive-services.md)합니다.
  
   둘째, 기술 세트에 포함할 AI 강화를 선택합니다. 연습 데모를 보려면 이 [빠른 시작](cognitive-search-quickstart-blob.md)을 참조하세요.

   데이터를 가져오기만 하려면 이 단계를 건너뛰고 인덱스 정의로 직접 이동합니다.

5. 다음은 **대상 인덱스 사용자 지정**입니다. 여기서 마법사에 제공되는 인덱스 스키마를 수락하거나 수정할수 있습니다. 마법사는 외부 데이터 원본에서 데이터를 샘플링하고 메타데이터를 읽으면서 필드 및 데이터 형식을 유추합니다.

   각 필드에 대해 [인덱스 특성을 선택](#index-definition)하여 특정 동작을 사용하도록 설정합니다. 아무 특성도 선택하지 않으면 인덱스를 사용할 수 없습니다. 

6. 다음은 이 마법사의 결과인 **인덱서 만들기**입니다. 인덱서는 외부 Azure 데이터 원본에서 검색 가능한 데이터 및 메타데이터를 추출하는 크롤러입니다. 지금까지 마법사의 각 단계를 이동해 가면서 데이터 원본을 선택하고 기술 세트(선택 사항) 및 인덱스를 연결하여 인덱서를 구성했습니다.

   인덱서 이름을 지정하고 **제출**을 클릭하여 가져오기 프로세스를 시작합니다. 

**인덱서** 목록에서 인덱서를 클릭하여 포털에서 인덱싱을 모니터링할 수 있습니다. 문서를 로드할 때 정의한 인덱스에 대한 문서 수가 증가합니다. 종종 포털 페이지에서 가장 최근 업데이트를 선택하는 데 몇 분이 걸릴 수 있습니다.

첫 번째 문서가 로드되는 즉시 인덱스는 쿼리할 준비가 됩니다. 이 작업에 대해 [검색 탐색기](search-explorer.md)를 사용할 수 있습니다.

<a name="data-source-inputs"></a>

## <a name="data-source-inputs"></a>데이터 원본 입력

**데이터 가져오기** 마법사는 외부 데이터 원본에 대한 연결 정보를 지정하는 영구 데이터 원본 개체를 만듭니다. 데이터 원본 개체는 [인덱서](search-indexer-overview.md)와 배타적으로 사용되며 다음 데이터 원본에 대해 만들 수 있습니다. 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md)([Cognitive Search](cognitive-search-concept-intro.md) 파이프라인에 대해 지원되지 않음)

일반 데이터 세트는 필수 입력입니다. 단일 테이블, 데이터베이스 뷰 또는 동등한 데이터 구조에서만 가져올 수 있습니다. 

마법사를 실행 하기 전에이 데이터 구조를 만들어야 하 고 콘텐츠를 포함 해야 합니다. 실행 되지 않고 합니다 **데이터 가져오기** 빈 데이터 원본에 대해 마법사.

|  선택 | 설명 |
| ---------- | ----------- |
| **기존 데이터 원본** |검색 서비스에 정의된 인덱서가 이미 있는 경우 다른 가져오기에 기존 데이터 원본 정의를 선택할 수 있습니다. Azure Search에서 데이터 원본 개체는 인덱서에서만 사용됩니다. 데이터 원본 개체는 프로그래밍 방식으로 또는 **데이터 가져오기** 마법사를 통해 만들 수 있습니다.|
| **샘플**| Azure Search는 Azure Search의 가져오기 및 쿼리 요청을 배우는 데 사용할 수 있는 무료 공용 Azure SQL Database를 포함합니다. [빠른 시작: 포털 도구를 사용하여 가져오기, 인덱싱 및 쿼리](search-get-started-portal.md) 자습서에서 자세한 설명을 참고하세요. |
| **Azure SQL Database** |읽기 권한이 포함된 데이터베이스 사용자에 대한 서비스 이름, 자격 증명 및 데이터베이스 이름은 페이지에 또는 ADO.NET 연결 문자열을 통해 지정될 수 있습니다. 연결 문자열 옵션을 선택하여 속성을 보거나 사용자 지정합니다. <br/><br/>행 집합을 제공하는 테이블 또는 보기는 페이지에 지정되어야 합니다. 연결에 성공한 후에 이 옵션이 나타나면 선택할 수 있는 드롭다운 목록을 제공합니다. |
| **Azure VM에서 SQL Server** |정규화된 서비스 이름, 사용자 ID와 암호 및 데이터베이스를 연결 문자열로 지정합니다. 이 데이터 원본을 사용하려면 연결을 암호화하는 로컬 저장소에 미리 인증서를 설치해야 합니다. 자세한 내용은 [Azure Search에 SQL VM 연결](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)을 참조하세요. <br/><br/>행 집합을 제공하는 테이블 또는 보기는 페이지에 지정되어야 합니다. 연결에 성공한 후에 이 옵션이 나타나면 선택할 수 있는 드롭다운 목록을 제공합니다. |
| **Cosmos DB** |요구 사항은 계정, 데이터베이스 및 컬렉션을 포함합니다. 컬렉션의 모든 문서는 인덱스에 포함됩니다. 평면화 하거나 행 집합을 필터링 하는 쿼리를 정의할 수도 있고 쿼리를 비워 둡니다. 이 마법사에서 쿼리를 필요 하지 않습니다.|
| **Azure Blob Storage** |요구 사항은 저장소 계정 및 컨테이너를 포함합니다. 필요에 따라 그룹화 목적으로 Blob 이름이 가상 명명 규칙을 따르는 경우 이름의 가상 디렉터리 부분을 컨테이너의 폴더로 지정할 수 있습니다. 자세한 내용은 [Blob Storage 인덱싱](search-howto-indexing-azure-blob-storage.md)을 참조하세요. |
| **Azure Table Storage** |요구 사항은 저장소 계정 및 테이블 이름을 포함합니다. 필요에 따라 쿼리를 지정하여 테이블의 하위 집합을 검색할 수 있습니다. 자세한 내용은 [Table Storage 인덱싱](search-howto-indexing-azure-tables.md)을 참조하세요. |


<a name="index-definition"></a>

## <a name="index-attributes"></a>인덱스 특성

**데이터 가져오기** 마법사는 입력 데이터 원본에서 가져온 문서로 채워지는 인덱스를 생성합니다. 

함수 인덱스의 경우 다음 요소를 정의했는지 확인합니다.

1. 한 필드는 각 문서를 고유하게 식별하는 데 사용되는 **Key**로 표시되어야 합니다. **Key**는 *Edm.string*이어야 합니다. 

   필드 값에 공백 또는 대시를 포함하는 경우 **고급 옵션**의 **인덱서 만들기** 단계에서 **Base-64 인코딩 키** 옵션을 설정하여 이러한 문자에 대한 유효성 검사를 비활성화해야 합니다.

1. 다음과 같이 각 필드에 대한 인덱스 특성을 설정합니다. 특성을 선택하지 않으면 필수 키 필드를 제외하고 인덱스는 기본적으로 비어 있습니다. 최소한 각 필드에 대해 이러한 특성 중 하나 이상을 선택합니다.
   
   + **검색 가능**은 검색 결과에 필드를 반환합니다. 검색 결과에 콘텐츠를 제공하는 모든 필드에는 이 특성이 있어야 합니다. 이 필드를 설정해도 인덱스 크기는 두드러지게 영향을 받지 않습니다.
   + **필터링 가능**을 선택하면 필드를 필터 식에서 참조할 수 있습니다. **$filter** 식에 사용되는 모든 필드는 이 특성이 있어야 합니다. 필터 식은 정확한 일치를 위한 것입니다. 텍스트 문자열은 그대로 유지되므로 축자 콘텐츠를 포함하기 위한 추가 스토리지가 필요합니다.
   + **검색 가능**을 선택하면 전체 텍스트를 검색할 수 있습니다. 자유 형식 쿼리 또는 쿼리 식에 사용되는 모든 필드에는 이 특성이 있어야 합니다. **검색 가능**으로 표시한 각 필드에 대해 반전된 인덱스가 만들어집니다.

1. 필요에 따라 다음 특성을 설정합니다.

   + **정렬 가능**을 선택하면 필드를 정렬에 사용할 수 있습니다. **$Orderby** 식에 사용되는 모든 필드는 이 특성이 있어야 합니다.
   + **패싯 가능**을 통해 필드를 패싯 탐색에 사용할 수 있습니다. **필터링 가능**으로도 표시되는 필드만 **패싯 가능**으로 표시될 수 있습니다.

1. 언어 강화 인덱싱 및 쿼리를 원하는 경우 **분석기**를 설정합니다. 기본값은 표준 Lucene이지만 불규칙한 명사 및 동사 형태를 확인하는 것과 같은 고급 어휘 처리에 Microsoft의 분석기를 사용하려는 경우 Microsoft 영어를 선택할 수도 있습니다.

   + **분석기** 목록을 사용하도록 설정하려면 **검색 가능**을 선택합니다.
   + 목록에 제공된 분석기를 선택합니다. 
   
   언어 분석기만이 이번에 지정될 수 있습니다. 사용자 지정 분석기 또는 키워드, 패턴, 등과 같은 비언어 분석기를 사용하려면 코드가 필요합니다. 분석기에 대한 자세한 내용은 [여러 언어로 문서의 인덱스 만들기](search-language-support.md)를 참조하세요.

1. 선택한 필드에 미리 입력 쿼리 제안을 사용하도록 설정하려면 **제안기** 확인란을 선택합니다.


## <a name="next-steps"></a>다음 단계
인덱서에 대해 자세히 알아보려면 다음 링크를 검토합니다.

* [Azure SQL Database 인덱싱](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB 인덱싱](search-howto-index-cosmosdb.md)
* [Blob Storage 인덱싱](search-howto-indexing-azure-blob-storage.md)
* [Table Storage 인덱싱](search-howto-indexing-azure-tables.md)