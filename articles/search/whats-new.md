---
title: 새 기능 공지
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에 대 한 Azure Search 서비스 이름을 포함 하 여 새로운 기능 및 향상 된 기능에 대 한 공지입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: d0e0e8a5aa3a3e43997e3f9512525be9f51d2018
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934859"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure Cognitive Search의 새로운 기능

서비스의 새로운 기능에 대해 알아봅니다. 서비스를 최신 상태로 유지 하려면이 페이지에 책갈피를 설정 합니다.

<a name="new-service-name"></a>

## <a name="new-service-name"></a>새 서비스 이름

이제 핵심 작업에서 확장 된 (선택 사항) 인식 기술 및 AI 처리 사용을 반영 하기 위해 Azure Search가 **Azure Cognitive Search** 로 이름이 변경 되었습니다. API 버전, NuGet 패키지, 네임 스페이스 및 엔드포인트은 변경 되지 않습니다. 신규 및 기존 검색 솔루션은 서비스 이름 변경의 영향을 받지 않습니다.

## <a name="feature-announcements"></a>기능 알림

### <a name="february-2020"></a>2 월 2020

+ [PII 검색 (미리 보기)](cognitive-search-skill-pii-detection.md) 은 입력 텍스트에서 개인 식별이 가능한 정보를 추출 하 고 다양 한 방법으로 해당 텍스트를 마스킹할 수 있는 옵션을 제공 하는 인덱싱 중에 사용 되는 인식 기술입니다.

+ 사용자 [지정 엔터티 조회 (미리 보기)](cognitive-search-skill-custom-entity-lookup.md ) 는 사용자가 정의한 단어 및 구 목록에서 텍스트를 검색 합니다. 이 목록을 사용 하 여 일치 하는 엔터티가 있는 모든 문서의 레이블을 표시 합니다. 이 기술은 유사 하지만 정확 하지 않은 일치 항목을 찾기 위해 적용할 수 있는 유사 항목 일치도 지원 합니다. 

### <a name="january-2020"></a>2020년 1월

+ [고객이 관리 하는 암호화 키](search-security-manage-encryption-keys.md) 가 이제 일반 공급 됩니다. REST를 사용 하는 경우 `api-version=2019-05-06`를 사용 하 여 기능에 액세스할 수 있습니다. 관리 코드의 경우에는 기능이 미리 보기 상태를 벗어나는 경우에도 올바른 패키지는 [.NET SDK 버전 8.0-미리 보기](search-dotnet-sdk-migration-version-9.md) 입니다. 

+ 검색 서비스에 대 한 개인 액세스는 현재 미리 보기로 제공 되는 두 가지 메커니즘을 통해 제공 됩니다.

  + 관리 REST API `api-version=2019-10-01-Preview`를 사용 하 여 서비스를 만들어 특정 IP 주소에 대 한 액세스를 제한할 수 있습니다. Preview API에는 [Createorupdate api](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate)의 새로운 **IpRule** 및 **networkruleset 집합** 속성이 있습니다. 이 미리 보기 기능은 선택 된 지역에서 사용할 수 있습니다. 자세한 내용은 [How to use The Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)항목을 참조 하세요.

  + 현재는 제한 된 액세스 미리 보기를 통해 사용할 수 있으며, 동일한 가상 네트워크에 있는 클라이언트의 연결을 위해 Azure 개인 엔드포인트을 지 원하는 Azure Search 서비스를 프로 비전 할 수 있습니다. 자세한 내용은 [보안 연결을 위한 개인 엔드포인트 만들기](service-create-private-endpoint.md)를 참조 하세요.

### <a name="december-2019"></a>2019년 12월

+ [앱 만들기 (미리 보기)](search-create-app-portal.md) 는 다운로드 가능한 HTML 파일을 생성 하는 포털의 새 마법사입니다. 이 파일에는 검색 서비스의 인덱스에 바인딩된 운영 "localhost" 스타일 웹 앱을 렌더링 하는 포함 된 스크립트가 포함 되어 있습니다. 페이지는 마법사에서 구성할 수 있으며 검색 표시줄, 결과 영역, 사이드바 탐색 및 typeahead.js 쿼리 지원을 포함할 수 있습니다. HTML 오프 라인을 수정 하 여 워크플로 또는 모양을 확장 하거나 사용자 지정할 수 있습니다.

+ [안전한 연결을 위한 개인 엔드포인트 만들기 (미리 보기)](service-create-private-endpoint.md) 는 검색 서비스에 대 한 보안 연결을 위한 개인 링크를 설정 하는 방법을 설명 합니다. 이 미리 보기 기능은 요청 시 사용할 수 있으며 솔루션의 일부로 [Azure 개인 링크](../private-link/private-link-overview.md) 및 [azure Virtual Network](../virtual-network/virtual-networks-overview.md) 를 사용 합니다.

### <a name="november-2019---ignite-conference"></a>11 월 2019-Ignite 회의

+ [증분 보강 (미리 보기)](cognitive-search-incremental-indexing-conceptual.md) 는 이미 처리 된 콘텐츠를 손실 하지 않고 특정 단계 또는 단계에 대 한 작업을 수행할 수 있도록 보강 파이프라인에 캐싱 및 statefullness을 추가 합니다. 이전에는 보강 파이프라인에 대 한 모든 변경에 전체 다시 작성이 필요 했습니다. 증분 보강을 사용 하면 비용이 많이 드는 분석 (특히 이미지 분석)의 출력이 보존 됩니다.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [문서 추출 (미리 보기)](cognitive-search-skill-document-extraction.md) 은 기술 내에서 파일의 콘텐츠를 추출할 수 있는 인덱싱 중에 사용 되는 인식 기술입니다. 이전에는 문서 크랙 기술 실행 되기 전에 발생 했습니다. 이 기술 외에도 기술 실행 내에서이 작업을 수행할 수 있습니다.

+ [텍스트 번역](cognitive-search-skill-text-translation.md) 은 텍스트를 평가 하는 인덱싱 중에 사용 되는 인식 기술 이며, 각 레코드에 대해 지정 된 대상 언어로 변환 된 텍스트를 반환 합니다.

+ [Power BI 템플릿은](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) Power BI desktop의 기술 자료 저장소에서 보강 콘텐츠를 시각화 하 고 분석할 수 있습니다. 이 템플릿은 [데이터 가져오기 마법사](knowledge-store-create-portal.md)를 통해 만든 Azure table 프로젝션 용으로 설계 되었습니다.

+ [Azure Data Lake Storage Gen2 (미리 보기)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (미리](search-howto-index-cosmosdb.md)보기) 및 [Cosmos DB Cassandra API (미리 보기)](search-howto-index-cosmosdb.md) 는 이제 인덱서에 지원 됩니다. [이 양식을](https://aka.ms/azure-cognitive-search/indexer-preview)사용 하 여 등록할 수 있습니다. 미리 보기 프로그램에 동의 하면 확인 전자 메일을 받게 됩니다.

### <a name="july-2019"></a>2019년 7월

+ 일반적으로 [Azure Government 클라우드에서](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search)사용할 수 있습니다.

## <a name="service-updates"></a>서비스 업데이트

Azure Cognitive Search에 대 한 [서비스 업데이트 공지](https://azure.microsoft.com/updates/?product=search&status=all) 는 azure 웹 사이트에서 찾을 수 있습니다.