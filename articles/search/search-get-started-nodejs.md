---
title: Node.js에서 Azure Search 시작 - Azure Search
description: Node.js를 프로그래밍 언어로 사용하여 사용자 지정 Azure에서 호스트된 클라우드 검색 서비스의 검색 애플리케이션을 빌드하는 과정을 안내합니다.
author: jj09
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/26/2017
ms.author: jjed
ms.custom: seodec2018
ms.openlocfilehash: 1b37b3c52abd3750c3452a46bdf5b0c5954de4dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61289206"
---
# <a name="get-started-with-azure-search-in-nodejs"></a>Node.js에서 Azure Search 시작
> [!div class="op_single_selector"]
> * [포털](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

검색 환경에 Azure Search를 사용하는 사용자 지정 Node.js 검색 애플리케이션을 빌드하는 방법에 대해 알아봅니다. 이 자습서에서는 [Azure Search 서비스 REST API](https://msdn.microsoft.com/library/dn798935.aspx)를 사용하여 이 연습에서 사용되는 개체 및 작업을 생성합니다.

이 코드를 개발하고 테스트하는 데에는 [Node.js](https://Nodejs.org) 및 NPM, [Sublime Text 3](https://www.sublimetext.com/3) 및 Windows 8.1의 Windows PowerShell이 사용되었습니다.

이 샘플을 실행하려면 [Azure Portal](https://portal.azure.com)에서 등록할 수 있는 Azure Search 서비스가 있어야 합니다. 단계별 지침은 [포털에서 Azure Search 서비스 만들기](search-create-service-portal.md)를 참조하세요.

## <a name="about-the-data"></a>데이터 정보
이 샘플 애플리케이션에서는 데이터 집합 크기를 줄이기 위해 Rhode Island 주에 대해 필터링된 [USGS(United States Geological Services)](https://geonames.usgs.gov/domestic/download_data.htm)의 데이터를 사용합니다. 이 데이터를 사용하여 병원 및 학교와 같은 랜드마크 빌딩뿐만 아니라 강, 호수, 산 등의 지질학적 특징을 반환하는 검색 애플리케이션을 빌드합니다.

이 애플리케이션에서 **DataIndexer** 프로그램은 [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx) 구문을 사용하여 인덱스를 빌드 및 로드하며, 이를 통해 Azure SQL Database에서 필터링된 USGS 데이터 세트를 검색합니다. 온라인 데이터 원본에 대한 자격 증명 및 연결 정보는 프로그램 코드에서 제공됩니다. 추가 구성은 필요하지 않습니다.

> [!NOTE]
> 무료 가격 책정 계층의 문서 제한(10,000개) 미만으로 유지하기 위해 이 데이터 세트에 필터를 적용했습니다. 표준 계층을 사용하는 경우에는 이 제한이 적용되지 않습니다. 각 가격 책정 계층의 용량에 대한 자세한 내용은 [Search 서비스 제한](search-limits-quotas-capacity.md)을 참조하세요.
> 
> 

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Azure Search 서비스의 서비스 이름 및 api-key 찾기
서비스를 만든 후 포털로 돌아가서 URL 또는 `api-key`를 가져옵니다. Search 서비스에 연결하려면 URL과 호출을 인증할 `api-key` 가 둘 다 있어야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 점프 모음에서 **검색 서비스**를 클릭하여 구독에 프로비전된 모든 Azure Search 서비스를 나열합니다.
3. 사용하려는 서비스를 선택합니다.
4. 서비스 대시보드에는 관리 키에 액세스할 수 있는 키 아이콘과 같은 필수 정보에 대한 타일이 표시됩니다.
5. 서비스 URL, 관리 키 및 쿼리 키를 복사합니다. 세 항목 모두 나중에 config.js 파일에 추가할 때 필요합니다.

## <a name="download-the-sample-files"></a>샘플 파일 다운로드
다음 방법 중 하나를 사용하여 샘플을 다운로드합니다.

1. [search-node-indexer-demo](https://github.com/Azure-Samples/search-node-indexer-demo)로 이동합니다.
2. **Download ZIP**을 클릭하고 .zip 파일을 저장한 다음 포함된 모든 파일을 추출합니다.

이후의 모든 파일 수정 및 실행 문은 이 폴더의 파일에 대해 수행됩니다.

## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>검색 서비스 URL 및 api-key로 config.js를 업데이트합니다.
앞에서 복사한 URL 및 api-key를 사용하여 구성 파일에서 URL, admin-key 및 query-key를 지정합니다.

관리 키는 인덱스 만들기 또는 삭제 및 문서 로드를 포함하여 서비스 작업에 대한 모든 권한을 부여합니다. 반면, 쿼리 키는 읽기 전용 작업용이며, 일반적으로 Azure Search에 연결하는 클라이언트 애플리케이션에서 사용됩니다.

이 샘플에는 클라이언트 애플리케이션에서 쿼리 키를 사용하는 모범 사례를 보강하는 데 도움이 되는 쿼리 키가 포함되어 있습니다.

다음 스크린샷에서는 텍스트 편집기에서 열려 있는 **config.js**를 보여 줍니다. 검색 서비스에 유효한 값으로 파일을 업데이트할 위치를 알 수 있도록 관련 항목이 경계선으로 구분되어 있습니다.

![][5]

## <a name="host-a-runtime-environment-for-the-sample"></a>샘플에 대한 런타임 환경 호스트
이 샘플에는 npm을 사용하여 전역적으로 설치할 수 있는 HTTP 서버가 필요합니다.

PowerShell 창에서 다음 명령을 실행합니다.

1. **package.json** 파일이 들어 있는 폴더로 이동합니다.
2. `npm install`을 입력합니다.
3. `npm install -g http-server`을 입력합니다.

## <a name="build-the-index-and-run-the-application"></a>인덱스 빌드 및 애플리케이션 실행
1. `npm run indexDocuments`을 입력합니다.
2. `npm run build`을 입력합니다.
3. `npm run start_server`을 입력합니다.
4. 브라우저에서 `http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>USGS 데이터 검색
USGS 데이터 집합에는 Rhode Island 주와 관련된 레코드가 포함되어 있습니다. 빈 검색 상자에서 **검색**을 클릭하면 기본적으로 상위 50개 항목을 가져옵니다.

검색 용어를 입력하면 검색 엔진이 실행됩니다. 지역 이름을 입력해 봅니다. “Roger Williams”는 Rhode Island의 최초 주지사였습니다. 유명한 공원, 빌딩 및 학교가 그의 이름을 따라 이름을 지었습니다.

![][9]

다음과 같은 용어를 입력해 볼 수도 있습니다.

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>다음 단계
이것은 Node.js 및 USGS 데이터 세트를 기반으로 하는 첫 번째 Azure Search 자습서입니다. 앞으로 이 자습서를 확장하여 사용자 지정 솔루션에서 사용할 수 있는 추가 검색 기능을 보여 드릴 예정입니다.

Azure Search에 대한 약간의 배경 지식이 이미 있는 경우 이 샘플을 기반으로 suggesters(사전 입력 또는 자동 완성 쿼리), 필터 및 패싯 탐색을 시작할 수 있습니다. 또한 사용자가 결과 페이지를 차례로 탐색할 수 있도록 개수를 추가하고 문서를 일괄 처리하여 검색 결과 페이지를 개선할 수 있습니다.

Azure Search를 처음 사용하세요? 다른 자습서를 통해 만들 수 있는 항목에 대한 이해를 높여 보세요. 더 많은 리소스를 보려면 [설명서 페이지](https://azure.microsoft.com/documentation/services/search/) 를 방문하세요. 

<!--Image references-->
[1]: ./media/search-get-started-Nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-Nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-Nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-Nodejs/AzSearch-Nodejs-configjs.png
[9]: ./media/search-get-started-Nodejs/rogerwilliamsschool.png
