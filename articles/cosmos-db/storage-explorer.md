---
title: Azure Storage Explorer를 사용하여 Azure Cosmos DB 리소스 관리
description: Azure Storage Explorer를 사용하여 Azure Cosmos DB에 연결하고 리소스를 관리하는 방법을 알아봅니다.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: d1948ae186662c7f60f4d49c19a4d48b424a38f7
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047482"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>Azure Storage Explorer 를 사용하여 데이터 작업

Azure Storage Explorer에서 Azure Cosmos DB를 사용하면 사용자는 Azure Cosmos DB 엔터티를 관리하고, 데이터를 조작하고, Storage Blob 및 큐와 같은 다른 Azure 엔터티와 함께 저장 프로시저 및 트리거를 업데이트할 수 있게 됩니다. 이제 동일한 도구를 사용하여 다양한 Azure 엔터티를 한 곳에서 관리할 수 있습니다. 현재 Azure Storage Explorer는 SQL, MongoDB, Graph 및 Table API용으로 구성된 Cosmos 계정을 지원합니다.


## <a name="prerequisites"></a>사전 요구 사항

SQL API 또는 Azure Cosmos DB의 MongoDB API가 있는 Cosmos 계정 계정이 없는 경우 [Azure Cosmos DB: .NET 및 Azure Portal을 사용하여 SQL API 웹앱 빌드](create-sql-api-dotnet.md)의 설명에 따라 Azure Portal에서 계정을 만들 수 있습니다.

## <a name="installation"></a>설치

[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)에서 최신 Azure Storage Explorer 비트를 설치하세요. 이제 Windows, Linux 및 MAC 버전이 지원됩니다.

## <a name="connect-to-an-azure-subscription"></a>Azure 구독에 연결

1. **Azure Storage 탐색기**를 설치한 후 다음 이미지에 나와 있는 것 처럼 왼쪽에 있는 **플러그** 인 아이콘을 선택 합니다.

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="연결 하려면 플러그 인 아이콘을 선택 합니다.":::

2. **Azure 계정 추가**를 선택한 다음, **로그인**을 선택합니다.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="필요한 Azure 구독에 연결":::

2. **Azure 로그인** 대화 상자에서 **로그인**을 선택 하 고 Azure 자격 증명을 입력 합니다.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Azure 구독에 로그인 합니다.":::

3. 목록에서 구독을 선택 하 고 **적용**을 선택 합니다.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="목록에서 필터링 할 구독 ID를 선택 합니다.":::

    탐색기 창이 업데이트되고, 선택한 구독에서 계정이 표시됩니다.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="사용 가능한 목록에서 Azure Cosmos DB 계정 선택":::

    **Cosmos DB 계정**을 Azure 구독에 성공적으로 연결했습니다.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>연결 문자열을 사용하여 Azure Cosmos DB에 연결

Azure Cosmos DB에 연결하는 또 다른 방법은 연결 문자열을 사용하는 것입니다. 연결 문자열을 사용하여 연결하려면 다음 단계를 따르세요.

1. 왼쪽 트리에서 **로컬 및 연결**을 찾아 **Azure Cosmos DB 계정**을 마우스 오른쪽 단추로 클릭한 후 **Azure Cosmos DB에 연결...** 을 선택합니다.

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="연결 문자열을 사용하여 Azure Cosmos DB에 연결":::

2. 현재 SQL 및 테이블 API만 지원합니다. API를 선택 하 고, **연결 문자열**을 붙여넣고, **계정 레이블**을 입력 하 고, **다음** 을 선택 하 여 요약을 확인 한 **다음 연결을 선택 하** 여 Azure Cosmos DB 계정에 연결 합니다. 기본 연결 문자열을 검색 하는 방법에 대 한 자세한 내용은 [연결 문자열 가져오기](manage-with-powershell.md#list-keys)를 참조 하세요.

    :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="연결 문자열 입력":::

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>로컬 에뮬레이터를 사용하여 Azure Cosmos DB에 연결

다음 단계를 사용하여 현재 SQL 계정만 지원하는 에뮬레이터로 Azure Cosmos DB에 연결합니다.

1. 에뮬레이터를 설치하고 시작합니다. 에뮬레이터를 설치하는 방법은 [Cosmos DB 에뮬레이터](https://docs.microsoft.com/azure/cosmos-db/local-emulator)를 참조하세요.

2. 왼쪽 트리에서 **로컬 및 연결**을 찾아 **Cosmos DB 계정**을 마우스 오른쪽 단추로 클릭한 후 **Cosmos DB 에뮬레이터에 연결...** 을 선택합니다.

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="에뮬레이터에서 Azure Cosmos DB에 연결":::

3. 현재 SQL API만 지원합니다. **연결 문자열**을 붙여넣고, **계정 레이블**을 입력 하 고, **다음** 을 선택 하 여 요약을 확인 한 **다음 연결을 선택 하** 여 Azure Cosmos DB 계정에 연결 합니다. 기본 연결 문자열을 검색 하는 방법에 대 한 자세한 내용은 [연결 문자열 가져오기](manage-with-powershell.md#list-keys)를 참조 하세요.

    :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="에뮬레이터 대화 상자에서 Cosmos DB에 연결":::


## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB 리소스 관리

다음을 수행하여 Azure Cosmos DB 계정을 관리할 수 있습니다.
* Azure Portal에서 계정 열기
* 빠른 액세스 목록에 리소스 추가
* 리소스 검색 및 새로 고침
* 데이터베이스 만들기 및 삭제
* 컬렉션 만들기 및 삭제
* 문서 만들기, 편집, 삭제 및 필터링
* 저장 프로시저, 트리거 및 사용자 정의 함수 관리

### <a name="quick-access-tasks"></a>빠른 액세스 작업

탐색기 창에서 구독을 마우스 오른쪽 단추로 클릭하여 다양한 바로 가기 작업을 수행할 수 있습니다.

* Azure Cosmos DB 계정 또는 데이터베이스를 마우스 오른쪽 단추로 클릭하여 **포털에서 열기**를 선택하면 Azure Portal을 통해 브라우저에서 리소스를 관리할 수 있습니다.

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="포털에서 열기":::

* Azure Cosmos DB 계정, 데이터베이스, 컬렉션 등을 **빠른 액세스**에 추가할 수도 있습니다.
* **여기에서 검색**을 선택하면 선택한 경로에서 키워드 검색이 가능합니다.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="여기에서 검색":::

### <a name="database-and-collection-management"></a>데이터베이스 및 컬렉션 관리

#### <a name="create-a-database"></a>데이터베이스 만들기

- Azure Cosmos DB 계정을 마우스 오른쪽 단추로 클릭하고 **데이터베이스 만들기**를 선택하고 데이터베이스 이름을 입력한 후 **Enter** 키를 눌러 완료합니다.

  :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Azure Cosmos 계정에서 데이터베이스 만들기":::

#### <a name="delete-a-database"></a>데이터베이스 삭제

- 데이터베이스를 마우스 오른쪽 단추로 클릭 하 고 **데이터베이스 삭제**를 선택한 다음 팝업 창에서 **예** 를 선택 합니다. 데이터베이스 노드가 삭제되고, Azure Cosmos DB 계정이 자동으로 새로 고쳐집니다.

  :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="첫 번째 데이터베이스 삭제":::

  :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="두 번째 데이터베이스 삭제":::

#### <a name="create-a-collection"></a>컬렉션 만들기

1. 데이터베이스를 마우스 오른쪽 단추로 클릭 하 고 **컬렉션 만들기**를 선택한 후 **컬렉션 ID**, **저장소 용량**등과 같은 정보를 제공 합니다. **확인** 을 클릭 하 여 완료 합니다.

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="데이터베이스에서 첫 번째 컬렉션 만들기":::

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="데이터베이스에서 두 번째 컬렉션 만들기":::

2. 파티션 키를 지정할 수 있도록 **제한 없음** 을 선택 하 고 **확인** 을 선택 하 여 완료 합니다.

    컬렉션을 만들 때 파티션 키가 사용된 경우, 컬렉션을 만들고 나면 컬렉션에 대해 파티션 키 값을 변경할 수 없습니다.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="파티션 키 구성":::

#### <a name="delete-a-collection"></a>컬렉션 삭제

- 컬렉션을 마우스 오른쪽 단추로 클릭 하 고 **컬렉션 삭제**를 선택한 다음 팝업 창에서 **예** 를 선택 합니다.

    컬렉션 노드가 삭제되고, 데이터베이스가 자동으로 새로 고쳐집니다.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="컬렉션 중 하나 삭제":::

### <a name="document-management"></a>문서 관리

#### <a name="create-and-modify-documents"></a>문서 만들기 및 수정

- 새 문서를 만들려면 왼쪽 창에서 **문서** 를 열고 **새 문서**를 선택한 다음 오른쪽 창에서 콘텐츠를 편집 하 고 **저장**을 선택 합니다. 기존 문서를 업데이트 한 후 **저장**을 선택할 수도 있습니다. **취소**를 클릭하여 변경 내용을 취소할 수 있습니다.

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="새 문서 만들기":::

#### <a name="delete-a-document"></a>문서 삭제

- 선택한 문서를 삭제하려면 **삭제** 단추를 클릭합니다.

#### <a name="query-for-documents"></a>문서에 대한 쿼리

- [SQL 쿼리](how-to-sql-query.md) 를 입력 하 여 문서 필터를 편집한 다음 **적용**을 선택 합니다.

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="특정 문서에 대 한 쿼리":::

### <a name="graph-management"></a>그래프 관리

#### <a name="create-and-modify-vertex"></a>꼭짓점 만들기 및 수정

1. 새 꼭 짓 점을 만들려면 왼쪽 창에서 **그래프** 를 열고, **새 꼭 짓 점**을 선택 하 고, 내용을 편집한 다음, **확인**을 선택 합니다.
2. 기존 꼭 짓 점을 수정 하려면 오른쪽 창에서 펜 아이콘을 선택 합니다.

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="그래프의 꼭 짓 점 수정":::

#### <a name="delete-a-graph"></a>그래프 삭제

- 꼭 짓 점을 삭제 하려면 꼭 짓 점 이름 옆에 있는 휴지통 아이콘을 선택 합니다.

#### <a name="filter-for-graph"></a>그래프에 대한 필터

- [Gremlin 쿼리](gremlin-support.md) 를 입력 한 다음 **필터 적용**을 선택 하 여 그래프 필터를 편집 합니다.

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Graph 쿼리 실행":::

### <a name="table-management"></a>테이블 관리

#### <a name="create-and-modify-table"></a>테이블 만들기 및 수정

1. 새 테이블을 만들려면 왼쪽 창에서 **엔터티** 를 열고 **추가**를 선택 하 고, **엔터티 추가** 대화 상자에서 콘텐츠를 편집 하 고, **속성 추가**단추를 클릭 하 여 속성을 추가 하 고, **삽입**을 선택 합니다.
2. 테이블을 수정 하려면 **편집**을 선택 하 고 콘텐츠를 수정한 다음 **업데이트**를 선택 합니다.

   :::image type="content" source="./media/storage-explorer/table.png" alt-text="테이블 만들기 및 수정":::

#### <a name="import-and-export-table"></a>테이블 가져오기 및 내보내기

1. 가져오려면 **가져오기** 단추를 선택 하 고 기존 테이블을 선택 합니다.
2. 내보내려면 **내보내기** 단추를 선택 하 고 대상을 선택 합니다.

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="테이블 가져오기 또는 내보내기":::

#### <a name="delete-entities"></a>엔터티 삭제

- 엔터티를 선택 하 고 **삭제**단추를 선택 합니다.

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="테이블 삭제":::

#### <a name="query-table"></a>쿼리 테이블

- 쿼리 **단추를 클릭 하** 고 쿼리 조건을 입력 한 다음 **쿼리 실행** 단추를 선택 합니다. **쿼리 닫기** 단추를 클릭하여 쿼리 창을 닫습니다.

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="테이블에서 데이터 쿼리":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>저장 프로시저, 트리거 및 UDF 관리

* 저장 프로시저를 만들려면 왼쪽 트리에서 **저장 프로시저**를 마우스 오른쪽 단추로 클릭 하 고, **저장 프로시저 만들기**를 선택 하 고, 왼쪽에 이름을 입력 하 고, 오른쪽 창에 저장 프로시저 스크립트를 입력 한 다음, **만들기**를 선택 합니다.
* 또한 업데이트를 두 번 클릭 하 고 업데이트를 클릭 한 다음 **업데이트** 를 클릭 하 여 저장 **하거나 취소를 선택 하** 여 변경 내용을 취소 하 여 기존 저장 프로시저를 편집할 수도 있습니다.

  :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="저장 프로시저 만들기 및 관리":::

* **트리거** 및 **UDF**에 대한 작업은 **저장 프로시저**에 대한 작업과 비슷합니다.

## <a name="troubleshooting"></a>문제 해결

[Azure Storage Explorer에서 Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/storage-explorer)는 Windows, macOS 또는 Linux에서 Sovereign 클라우드 및 Azure에 호스팅되는 Azure Cosmos DB 계정에 연결할 수 있게 해주는 독립 실행형 앱입니다. 사용자는 Azure Cosmos DB 엔터티를 관리하고, 데이터를 조작하고, Storage Blob 및 큐와 같은 다른 Azure 엔터티와 함께 저장된 프로시저 및 트리거를 업데이트할 수 있게 됩니다.

Storage Explorer의 Azure Cosmos DB에서 나타나는 일반적인 문제에 대한 솔루션입니다.

### <a name="sign-in-issues"></a>로그인 문제

계속하기 전에 애플리케이션을 다시 시작하고 문제가 해결될 수 있는지 확인합니다.

#### <a name="self-signed-certificate-in-certificate-chain"></a>인증서 체인의 자체 서명된 인증서

이 오류가 표시되는 원인에는 몇 가지가 있는데, 가장 일반적인 두 가지는 다음과 같습니다.

+ 사용자는 *투명 프록시*뒤에 있습니다. 즉, it 부서와 같은 누군가가 HTTPS 트래픽을 가로채 고 암호를 해독 한 다음 자체 서명 된 인증서를 사용 하 여 암호화 하는 것을 의미 합니다.

+ 수신 하는 HTTPS 메시지에 자체 서명 된 TLS/SSL 인증서를 삽입 하는 바이러스 백신 소프트웨어와 같은 소프트웨어를 실행 하 고 있습니다.

Storage Explorer가 이러한 "자체 서명된 인증서" 중 하나를 발견하면 수신하는 HTTPS 메시지가 변조되었는지 여부를 더 이상 알지 못합니다. 자체 서명된 인증서의 복사본을 갖고 있는 경우 Storage Explorer에 해당 인증서를 신뢰하도록 지시할 수 있습니다. 인증서를 주입하는 주체가 누구인지 모른다면 다음 단계를 수행하여 알아낼 수 있습니다.

1. OpenSSL 설치
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html)(라이트 버전도 사용 가능)
     - Mac 및 Linux: 운영 체제에 포함되어야 합니다.
2. OpenSSL 실행
    - Windows: 설치 디렉터리, **/bin/** 으로 이동한 다음, **openssl.exe**를 두 번 클릭합니다.
    - Mac 및 Linux: 터미널에서 **openssl**을 실행합니다.
3. `s_client -showcerts -connect microsoft.com:443` 실행
4. 자체 서명된 인증서를 찾습니다. 어떤 인증서가 자체 서명된 것인지 확실하지 않은 경우 제목(“s:”)과 발급자(“i:”)가 같은 위치를 찾습니다.
5.  자체 서명된 인증서를 찾았으면 각 인증서에 대해 **-----BEGIN CERTIFICATE-----** 부터 **-----END CERTIFICATE-----** 까지 모든 줄을 복사하여 새 .cer 파일에 붙여넣습니다.
6.  Storage 탐색기를 연 후 **Edit**  >  **SSL 인증서**편집  >  **인증서 가져오기**로 이동 합니다. 파일 선택기를 사용하여, 만든 .cer 파일을 찾아서 선택하고 엽니다.

위의 단계를 사용하여 자체 서명된 인증서를 찾을 수 없는 경우 사용자 의견을 보내 도움을 받을 수 있습니다.

#### <a name="unable-to-retrieve-subscriptions"></a>구독을 검색할 수 없음

성공적으로 로그인한 후 구독을 검색할 수 없습니다.

- [Azure Portal](https://portal.azure.com/) 에 로그인 하 여 계정에 구독에 대 한 액세스 권한이 있는지 확인 합니다.
- 올바른 환경([Azure](https://portal.azure.com/), [Azure China](https://portal.azure.cn/), [Azure Germany](https://portal.microsoftazure.de/), [Azure US Government](https://portal.azure.us/) 또는 사용자 지정 환경/Azure Stack)을 사용하여 로그인했는지 확인합니다.
- 프록시 뒤에 있는 경우 Storage Explorer 프록시를 제대로 구성했는지 확인합니다.
- 계정을 제거하고 다시 추가해 봅니다.
- 홈 디렉터리(즉, C:\Users\ContosoUser)에서 다음 파일을 삭제한 다음, 계정을 다시 추가합니다.
  - .adalcache
  - .devaccounts
  - .extaccounts
- 로그인할 때 개발자 도구 콘솔(F12 키)에서 오류 메시지를 확인합니다.

:::image type="content" source="./media/storage-explorer/console.png" alt-text="개발자 도구 콘솔에서 오류를 확인 합니다.":::

#### <a name="unable-to-see-the-authentication-page"></a>인증 페이지를 볼 수 없음

인증 페이지를 볼 수 없습니다.

- 연결 속도에 따라 로그인 페이지가 로드되는 데 시간이 걸릴 수 있으므로 인증 대화 상자를 닫기 전에 1분 이상 기다립니다.
- 프록시 뒤에 있는 경우 Storage Explorer 프록시를 제대로 구성했는지 확인합니다.
- F12 키를 눌러 개발자 콘솔을 가져옵니다. 개발자 콘솔의 응답을 보고 인증이 작동하지 않는 이유에 대한 단서를 찾을 수 있는지 확인합니다.

#### <a name="cannot-remove-account"></a>계정을 제거할 수 없음

계정을 제거할 수 없거나 재인증 링크가 작동하지 않습니다.

- 홈 디렉터리에서 다음 파일을 삭제한 다음, 계정을 다시 추가합니다.
  - .adalcache
  - .devaccounts
  - .extaccounts
- SAS 연결 Storage 리소스를 제거하려면 다음을 삭제합니다.
  - Windows - %AppData%/StorageExplorer 폴더
  - Mac - /Users/<your_name>/Library/Application SUpport/StorageExplorer
  - Linux - ~/.config/StorageExplorer
  - 이러한 파일을 삭제하면 **모든 자격 증명을 다시 입력해야 합니다**.


### <a name="httphttps-proxy-issue"></a>Http/Https 프록시 문제

ASE에서 http/https 프록시를 구성할 때 왼쪽 트리에서 Azure Cosmos DB 노드를 나열할 수 없습니다. 이 시점에서 Azure Portal에서 Azure Cosmos DB 데이터 탐색기를 해결 방법으로 사용할 수 있습니다.

### <a name="development-node-under-local-and-attached-node-issue"></a>“로컬 및 연결” 노드 중 “개발” 노드 문제

왼쪽 트리의 "로컬 및 연결" 노드에서 "개발" 노드를 선택한 후 응답이 없습니다.  이는 정상적인 동작입니다. Azure Cosmos DB 로컬 에뮬레이터는 다음 릴리스에서 지원됩니다.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="개발 노드":::

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>“로컬 및 연결” 노드의 Azure Cosmos DB 계정 연결 오류

“로컬 및 연결” 노드에서 Azure Cosmos DB 계정을 연결한 후 아래 오류가 표시되는 경우, 올바른 연결 문자열을 사용하고 있는지 확인합니다.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="로컬 및 연결 노드에서 Azure Cosmos DB 연결 오류":::

### <a name="expand-azure-cosmos-db-node-error"></a>Azure Cosmos DB 노드 확장 오류

왼쪽의 트리 노드를 확장하는 동안 다음 오류가 나타날 수 있습니다.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Azure Cosmos DB 노드 확장 오류":::

다음과 같은 제안 사항을 시도해 봅니다.

- Azure Cosmos DB 계정이 프로비전 진행 중인지 확인하고 계정을 성공적으로 생성될 때 다시 시도합니다.
- 계정이 노드 “빠른 액세스” 노드 또는 “로컬 및 연결” 노드에 있는 경우 해당 계정이 삭제되었는지 확인합니다. 이 경우 노드를 수동으로 제거해야 합니다.

## <a name="next-steps"></a>다음 단계

* Azure Storage 탐색기에서 Azure Cosmos DB를 사용하는 방법에 대해 설명하는 다음 비디오를 시청하세요. [Use Azure Cosmos DB in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)(Azure Storage 탐색기에서 Azure Cosmos DB 사용)
* [Storage Explorer 시작](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)에서 Storage Explorer에 대해 자세히 알아보고 더 많은 서비스에 연결합니다.
