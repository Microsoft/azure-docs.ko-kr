---
title: '자습서: Azure Data Catalog에서 데이터 자산 등록'
description: 이 자습서에서는 Azure Data Catalog에 데이터 자산을 등록하는 방법을 설명합니다.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: tutorial
ms.date: 08/01/2019
ms.openlocfilehash: 66bacdf27b2ef9cf624b645cb6e4c123d9c3fa2a
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135881"
---
# <a name="tutorial-register-data-assets-in-azure-data-catalog"></a>자습서: Azure Data Catalog에서 데이터 자산 등록

이 자습서에서는 등록 도구를 사용하여 데이터베이스 샘플의 데이터 자산을 카탈로그에 등록합니다. 등록은 데이터 원본 및 포함된 자산에서 이름, 유형 및 위치와 같은 주요 구조적 메타데이터를 추출하고 카탈로그에 메타데이터를 복사하는 과정입니다. 데이터 원본 및 데이터 자산은 있는 위치에 유지되지만 메타데이터는 보다 쉽게 검색 및 이해할 수 있도록 카탈로그를 통해 사용됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * 데이터 자산 등록 
> * 데이터 자산 검색
> * 데이터 자산에 주석 추가
> * 데이터 자산에 연결
> * 데이터 자산 관리
> * 데이터 자산 삭제

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 [빠른 시작](register-data-assets-tutorial.md)을 완료해야 합니다.

* [Microsoft Azure](https://azure.microsoft.com/) 구독
* 고유한 [Azure Active Directory 테넌트](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)가 있어야 합니다.

데이터 카탈로그를 설정하려면 Azure 구독의 소유자 또는 공동 소유자여야 합니다.

## <a name="register-data-assets"></a>데이터 자산 등록

### <a name="register-a-data-source"></a>데이터 원본 등록

Azure SQL Database용 [데이터베이스 샘플](../azure-sql/database/single-database-create-quickstart.md)에서 데이터 자산(테이블)을 등록하지만 사용자의 역할과 관련된 친숙한 데이터로 작업하는 것을 선호하는 경우 지원되는 모든 데이터 원본을 사용할 수 있습니다. 지원되는 데이터 원본 목록은 [지원되는 데이터 원본](data-catalog-dsr.md)을 참조하세요.

이 자습서에서 사용하는 데이터베이스 이름은 *RLSTest*입니다.

이제 Azure Data Catalog를 사용하여 데이터베이스 샘플의 데이터 자산을 등록할 수 있습니다.

1. [Azure Data Catalog 홈 페이지](http://azuredatacatalog.com) 로 이동하고 **데이터 게시**를 선택합니다.

   ![Azure Data Catalog - 데이터 게시 단추](media/register-data-assets-tutorial/data-catalog-publish-data.png)

2. **애플리케이션 시작**을 선택하여 컴퓨터에서 등록 도구를 다운로드, 설치 및 실행합니다.

   ![Azure Data Catalog - 시작 단추](media/register-data-assets-tutorial/data-catalog-launch-application.png)

3. **시작** 페이지에서 **로그인**을 선택하고 자격 증명을 입력합니다.

    ![Azure Data Catalog - 시작 페이지](media/register-data-assets-tutorial/data-catalog-welcome-dialog.png)

4. **Microsoft Azure Data Catalog** 페이지에서 **SQL Server**, **다음**을 차례로 선택합니다.

    ![Azure Data Catalog - 데이터 원본](media/register-data-assets-tutorial/data-catalog-data-sources.png)

5. Azure SQL Database의 데이터베이스 샘플에 대한 SQL Server 연결 속성을 입력하고 **연결**을 선택합니다.

   ![Azure Data Catalog - SQL Server 연결 설정](media/register-data-assets-tutorial/data-catalog-sql-server-connection.png)

6. 데이터 자산의 메타데이터를 등록합니다. 이 예제에서는 샘플 네임스페이스에서 **제품** 개체를 등록합니다.

   1. **서버 계층** 트리에서 데이터베이스 샘플을 확장하고 **SalesLT**를 선택합니다.

   2. Ctrl 키를 누른 채 **Product**, **ProductCategory**, **ProductDescription** 및 **ProductPhoto**를 선택합니다.

   3. **선택 항목을 이동시키는 화살표**( **>** )를 선택합니다. 이 작업으로 인해 선택한 모든 개체가 **등록할 개체** 목록으로 이동합니다.

      ![Azure Data Catalog 자습서 - 개체 찾기 및 선택](media/register-data-assets-tutorial/data-catalog-server-hierarchy.png)

   4. **미리 보기 포함** 을 선택하여 데이터의 스냅샷 미리 보기를 포함하도록 합니다. 스냅샷은 각 테이블에서 최대 20개의 레코드를 포함하고 카탈로그에 복사됩니다.

   5. **데이터 프로필 포함** 을 선택하여 데이터 프로필에 대한 개체 통계의 스냅샷을 포함합니다(예: 열에 대한 최소값, 최대값 및 평균값, 행수).

   6. **태그 추가** 필드에 **판매, 제품, azure sql**을 입력합니다. 이 작업은 데이터 자산에 대한 검색 태그를 추가합니다. 태그는 사용자가 등록된 데이터 원본을 찾을 수 있는 훌륭한 방법입니다.

   7. 이 데이터에 대한 **전문가** 의 이름을 지정합니다(선택 사항).

      ![Azure Data Catalog 자습서- 등록할 개체](media/register-data-assets-tutorial/data-catalog-objects-register.png)

   8. **등록**을 선택합니다. Azure 데이터 카탈로그는 선택한 개체를 등록합니다. 이 연습에서는 데이터베이스 샘플에서 선택한 개체가 등록됩니다. 등록 도구는 데이터 자산에서 메타데이터를 추출하고 데이터를 Azure Data Catalog 서비스로 복사합니다. 데이터는 현재 있는 위치에 유지됩니다. 데이터에 대한 관리자의 제어와 원래 시스템의 정책 적용은 유지됩니다.

      ![Azure Data Catalog - 등록된 개체](media/register-data-assets-tutorial/data-catalog-registered-objects.png)

    9. 등록된 데이터 원본 개체를 보려면 **포털 보기**를 선택합니다. Azure Data Catalog 포털의 그리드 보기에서 네 개의 테이블 및 데이터베이스가 모두 표시되는지 확인합니다(검색 창이 정리되었는지 확인).

       ![Azure Data Catalog 포털에 있는 개체](media/register-data-assets-tutorial/data-catalog-view-portal.png)

이 연습에서는 조직 전체의 사용자가 쉽게 찾을 수 있도록 Azure SQL Database에 대한 데이터베이스 샘플에서 개체를 등록했습니다.

다음 연습에서는 등록된 데이터 자산을 검색하는 방법을 배웁니다.

## <a name="discover-data-assets"></a>데이터 자산 검색

Azure Data Catalog에서 검색은 검색 및 필터링이라는 두 가지 기본 메커니즘을 사용합니다.

검색은 직관적이고 강력하게 설계되어 있습니다. 기본적으로 검색 용어는 사용자가 제공한 주석을 포함하여 카탈로그의 모든 속성과 일치합니다.

필터링은 검색을 보완하도록 설계되었습니다. 전문가, 데이터 원본 유형, 개체 유형 및 태그와 같은 특정 특성을 선택하여 일치하는 데이터 자산을 볼 수 있으며 일치하는 자산으로 검색 결과를 제한할 수 있습니다.

검색 및 필터링 조합을 사용하여 Azure Data Catalog에 등록된 데이터 원본을 빠르게 탐색할 수 있습니다.

이 연습에서는 Azure Data Catalog 포털을 사용하여 이전 연습에서 등록한 데이터 자산을 검색합니다. 검색 구문에 대한 자세한 내용은 [Data Catalog Search 구문 참조](/rest/api/datacatalog/#search-syntax-reference) 를 참조하세요.

다음은 카탈로그에서 데이터 자산을 검색하기 위한 몇 가지 예입니다.  

### <a name="discover-data-assets-with-basic-search"></a>기본 검색을 사용하여 데이터 자산 검색

기본 검색을 사용하면 하나 이상의 검색 용어를 사용하여 카탈로그를 검색할 수 있습니다. 결과는 지정된 하나 이상의 용어와 속성에 대해 일치하는 모든 자산입니다.

1. Azure Data Catalog 포털에서 **홈**을 선택합니다. 웹 브라우저를 닫은 경우 [Azure Data Catalog 홈 페이지](https://www.azuredatacatalog.com)로 이동합니다.

2. 검색 상자에 `product` 를 입력하고 **ENTER**를 누릅니다.

   ![Azure Data Catalog - 기본 텍스트 검색](media/register-data-assets-tutorial/data-catalog-basic-text-search.png)

3. 결과에 네 개의 테이블 및 데이터베이스가 모두 표시되는지 확인합니다. 다음 이미지와 같이 도구 모음에 있는 단추를 선택하여 **그리드 보기**와 **목록 보기** 사이에서 전환할 수 있습니다. **강조** 옵션이 **켜져** 있기 때문에 검색 키워드가 검색 결과에서 강조 표시됩니다. 검색 결과에서 **페이지당 결과** 수를 지정할 수도 있습니다.

   ![Azure Data Catalog - 기본 텍스트 검색 결과](media/register-data-assets-tutorial/data-catalog-basic-text-search-results.png)

   **검색** 패널은 왼쪽에, **속성** 패널은 오른쪽에 있습니다. **검색** 패널에서 검색 조건을 변경하고 결과를 필터링할 수 있습니다. **속성** 패널에는 그리드 또는 목록에서 선택한 개체의 속성이 표시됩니다.

4. 검색 결과에서 **제품**을 선택합니다. **미리 보기**, **열**, **데이터 프로필** 및 **설명서** 탭을 선택하거나 화살표를 선택하여 아래쪽 창을 확장합니다.  

   ![Azure Data Catalog - 아래쪽 창](media/register-data-assets-tutorial/data-catalog-data-asset-preview.png)

   **미리 보기** 탭에서, **상품** 테이블에 있는 데이터의 미리 보기가 표시됩니다.

5. **열** 탭을 선택하여 데이터 자산에서 열에 대한 세부 정보(예: **이름** 및 **데이터 형식**)를 찾습니다.

6. **데이터 프로필** 탭을 선택하여 데이터 자산에서 데이터의 프로파일링(예: 행 수, 데이터 크기 또는 열의 최솟값)을 확인합니다.

### <a name="discover-data-assets-with-property-scoping"></a>속성 범위로 데이터 자산 검색

속성 범위를 지정하면 검색 용어가 지정된 속성과 일치하는 데이터 자산을 검색하는 데 도움이 됩니다.

1. **필터**의 **개체 유형** 아래에 있는 **테이블** 필터를 지웁니다.  

2. 검색 상자에 `tags:product` 를 입력하고 **ENTER**를 누릅니다. 데이터 카탈로그를 검색하는 데 사용할 수 있는 모든 속성에 대해서는 [Data Catalog 검색 구문 참조](/rest/api/datacatalog/#search-syntax-reference) 를 참조하세요.

3. 결과에 테이블 및 데이터베이스가 모두 표시되는지 확인합니다.  

   ![Data Catalog - 검색 결과 속성 범위 지정](media/register-data-assets-tutorial/data-catalog-property-scoping-results.png)

### <a name="save-the-search"></a>검색 저장

1. **현재 검색** 섹션의 **검색** 창에서 검색 이름을 입력하고 **저장**을 선택합니다.

   ![Azure Data Catalog - 검색 저장](media/register-data-assets-tutorial/data-catalog-save-search.png)

2. 저장한 검색이 **저장된 검색**아래에 표시되는지 확인합니다.

3. 저장된 검색에서 수행할 수 있는 작업 중 하나를 선택합니다(**이름 바꾸기**, **삭제**, **기본값으로 저장** 검색).

### <a name="grouping-with-parentheses"></a>괄호로 그룹화

괄호로 그룹화하여 특별히 부울 연산자와 함께 논리적 격리를 수행하도록 쿼리의 일부를 그룹화할 수 있습니다.

1. 검색 상자에 `name:product AND (tags:product AND objectType:table)` 를 입력하고 **ENTER**를 누릅니다.

2. 검색 결과에 **상품** 테이블만이 표시되는지 확인합니다.

   ![Azure Data Catalog - 검색 그룹화](media/register-data-assets-tutorial/data-catalog-grouping-search.png)

### <a name="comparison-operators"></a>비교 연산자

비교 연산자를 사용하면 숫자 및 날짜 데이터 유형이 있는 속성에 대한 일치가 아닌 비교를 사용할 수 있습니다.

1. 검색 상자에 `lastRegisteredTime:>"06/09/2016"`를 입력합니다.

2. **개체 유형**에 있는 **테이블** 필터를 지웁니다.

3. **Enter**키를 누릅니다.

4. **Product**, **ProductCategory** 및 **ProductDescription** 테이블 및 검색 결과에 등록된 SQL 데이터베이스가 표시되는지 확인합니다.

   ![Azure Data Catalog - 비교 검색 결과](media/register-data-assets-tutorial/data-catalog-comparison-operator-results.png)

데이터 자산 검색에 대한 자세한 내용은 [데이터 자산을 검색하는 방법](data-catalog-how-to-discover.md) 을 참조하세요. 검색 구문에 대한 자세한 내용은 [Data Catalog 검색 구문 참조](/rest/api/datacatalog/#search-syntax-reference) 를 참조하세요.

## <a name="annotate-data-assets"></a>데이터 자산에 주석 추가

이 연습에서는 Azure Data Catalog 포털을 사용하여 카탈로그의 기존 데이터 자산에 주석을 추가(설명, 태그, 전문가 등의 정보를 추가)합니다. 주석은 등록하는 동안 데이터 원본에서 추출한 구조 메타데이터를 보완합니다. 주석을 사용하면 데이터 자산 검색 및 이해가 훨씬 더 쉬워집니다.

이 연습에서는 단일 데이터 자산(ProductPhoto)에 주석을 지정합니다. ProductPhoto 데이터 자산에 친숙한 이름과 설명을 추가합니다.  

1. [Azure Data Catalog 홈 페이지](https://www.azuredatacatalog.com)로 이동하고 `tags:product`로 검색하여 등록한 데이터 자산을 찾습니다.

2. 검색 결과에서 **ProductModel**을 선택합니다.  

3. **식별 이름**에 **Product images**를 입력하고 **설명**에 **Product photos for marketing materials**를 입력합니다.

    ![Azure Data Catalog - ProductPhoto 설명](media/register-data-assets-tutorial/data-catalog-productmodel-description.png)

    **설명** 은 선택한 데이터 자산을 사용하는 이유와 방법을 다른 사람이 검색하고 이해할 수 있도록 합니다. 더 많은 태그를 추가하고 열을 볼 수도 있습니다. 카탈로그에 추가한 설명이 포함된 메타데이터를 사용하여 데이터 원본을 검색 및 필터링할 수 있습니다.

이 페이지에서 다음 단계를 수행할 수도 있습니다.

* 데이터 자산에 대한 전문가를 추가합니다. **전문가** 영역에서 **추가**를 선택합니다.

* 데이터 세트 수준에 태그를 추가합니다. **태그** 영역에서 **추가**를 선택합니다. 태그는 사용자 태그 또는 용어집 태그일 수 있습니다. Data Catalog의 표준 버전에는 카탈로그 관리자가 중앙 비즈니스 분류를 정의하는 데 도움이 되는 비즈니스 용어집이 포함되어 있습니다. 그런 다음 카탈로그 사용자는 용어집 용어를 사용하여 데이터 자산에 주석을 추가할 수 있습니다. 자세한 내용은 [관리 태그 지정을 위해 비즈니스 용어집을 설정하는 방법](data-catalog-how-to-business-glossary.md)

* 열 수준에 태그를 추가합니다. 주석을 추가하려는 열의 **태그** 아래에서 **추가**를 선택합니다.

* 열 수준에 설명을 추가합니다. 열에 **설명** 을 입력합니다. 데이터 원본에서 추출된 설명 메타데이터를 볼 수도 있습니다.

* 사용자에게 데이터 자산에 대한 액세스를 요청하는 방법을 보여주는 **액세스 요청** 정보를 추가합니다.
  
* **설명서** 탭을 선택하고 데이터 자산에 대한 설명서를 제공합니다. Azure Data Catalog 설명서를 사용하면 데이터 카탈로그를 콘텐츠 리포지토리로 사용하여 데이터 자산의 전체 설명을 만들 수 있습니다.
  
또한 여러 데이터 자산에 주석을 추가할 수도 있습니다. 예를 들어 등록된 데이터 자산을 모두 선택하고 이에 대한 전문가를 지정할 수 있습니다.

![Azure Data Catalog - 여러 데이터 자산에 주석 추가](media/register-data-assets-tutorial/data-catalog-multi-select-annotate.png)

Azure Data Catalog는 주석에 대한 크라우드 소싱 접근 방식을 지원합니다. 모든 Data Catalog 사용자는 태그(사용자 또는 용어집), 설명 및 기타 메타데이터를 추가할 수 있습니다. 이 작업을 수행하면 사용자가 데이터 자산 및 해당 용도에 대한 큐브 뷰를 추가하고 다른 사용자와 해당 큐브 뷰를 공유합니다.

데이터 자산에 주석을 추가하는 방법에 대한 자세한 내용은 [데이터 자산에 주석을 추가하는 방법](data-catalog-how-to-annotate.md) 을 참조하세요.

## <a name="connect-to-data-assets"></a>데이터 자산에 연결

이 연습에서는 연결 정보를 사용하여 통합 클라이언트 도구(Excel)와 통합되지 않은 도구(SQL Server Management Studio)에서 데이터 자산을 엽니다.

> [!NOTE]
> Azure Data Catalog를 통해 실제 데이터 원본에 액세스할 수 있는 것은 아닙니다. 단지 사용자가 더 쉽게 검색하고 이해하는 데 도움이 됩니다. 데이터 원본에 연결할 때 선택한 클라이언트 애플리케이션은 Windows 자격 증명을 사용하거나 필요에 따라 자격 증명에 대한 메시지를 표시합니다. 이전에 데이터 원본에 대해 액세스가 허용되지 않은 경우 연결하기 전에 액세스가 허용되어야 합니다.

### <a name="connect-to-a-data-asset-from-excel"></a>Excel에서 데이터 자산에 연결

1. 검색 결과에서 **상품** 을 선택합니다. 도구 모음에서 **다음에서 열기**를 선택하고 **Excel**을 선택합니다.

   ![Azure Data Catalog - 데이터 자산에 연결](media/register-data-assets-tutorial/data-catalog-connect1.png)

2. 다운로드 팝업 창에서 **열기**를 선택합니다. 이 환경은 브라우저에 따라 다를 수 있습니다.

3. **Microsoft Excel 보안 공지** 창에서 **사용**을 선택합니다.

   ![Azure Data Catalog - Excel 보안 팝업](media/register-data-assets-tutorial/data-catalog-excel-security-popup.png)

4. **데이터 가져오기** 대화 상자에서 기본값을 유지하고 **확인**을 선택합니다.

   ![Azure Data Catalog - Excel 가져오기 데이터](media/register-data-assets-tutorial/data-catalog-excel-import-data.png)

5. 데이터 원본을 Excel에서 봅니다.

   ![Azure Data Catalog - Excel의 제품 테이블](media/register-data-assets-tutorial/data-catalog-connect2.png)

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

이 연습에서는 Azure Data Catalog를 사용하여 검색된 데이터 자산에 연결했습니다. Azure Data Catalog 포털에서 **열기** 메뉴에 통합된 클라이언트 애플리케이션을 사용하여 직접 연결할 수 있습니다. 자산 메타데이터에 포함된 연결 위치 정보를 사용하여 선택한 애플리케이션과 연결할 수도 있습니다. 예를 들어 SQL Server Management Studio를 사용하여 Azure SQL Database에 연결하고 이 자습서에 등록된 데이터 자산의 데이터에 액세스할 수 있습니다.

1. **SQL Server Management Studio**를 엽니다.

2. **서버에 연결** 대화 상자에서 Azure Data Catalog 포털의 **속성** 창에 있는 서버 이름을 입력합니다.

3. 적절한 인증 및 자격 증명을 사용하여 데이터 자산에 액세스합니다. 액세스 권한이 없는 경우 **액세스 요청** 필드의 정보를 사용하여 가져옵니다.

   ![Azure Data Catalog - 액세스 요청](media/register-data-assets-tutorial/data-catalog-request-access.png)

**연결 문자열 보기**를 선택하여 ADO.NET, ODBC 및 OLEDB 연결 문자열을 보고 애플리케이션에서 사용하기 위해 클립보드로 복사합니다.

## <a name="manage-data-assets"></a>데이터 자산 관리

이 단계에서는 데이터 자산에 대한 보안을 설정하는 방법을 표시합니다. Data Catalog는 사용자가 데이터 자체에 액세스할 권한을 부여하지 않습니다. 데이터 원본의 소유자가 데이터 액세스를 제어합니다.

Data Catalog를 사용하여 데이터 원본을 검색하고 카탈로그에 등록된 원본과 관련된 메타데이터를 볼 수 있습니다. 그러나 데이터 원본을 특정 사용자 또는 특정 그룹의 구성원에게만 표시해야 할 상황이 있을 수 있습니다. 이 시나리오의 경우 Data Catalog를 사용하여 등록된 데이터 자산의 소유권을 가져오고 소유한 자산의 표시 유형을 제어할 수 있습니다.

> [!NOTE]
> 이 연습에서 설명하는 관리 기능은 Azure Data Catalog의 무료 버전에서 사용할 수 없으며 표준 버전에서만 사용할 수 있습니다.
> Azure Data Catalog에서 데이터 자산의 소유권을 가져오고, 공동 소유자를 데이터 자산에 추가하고, 데이터 자산의 표시 유형을 설정할 수 있습니다.

### <a name="take-ownership-of-data-assets-and-restrict-visibility"></a>데이터 자산 소유권 가져오기 및 표시 유형 제한

1. [Azure Data Catalog 홈 페이지](https://www.azuredatacatalog.com)로 이동합니다. **검색** 텍스트 상자에 `tags:cycles`를 입력하고 **ENTER**를 누릅니다.

2. 결과 목록에서 항목을 선택하고 도구 모음에서 **소유권 가져오기**를 선택합니다.

3. **속성** 패널의 **관리** 섹션에서 **소유권 가져오기**를 선택합니다.

    ![Azure Data Catalog - 소유권 가져오기](media/register-data-assets-tutorial/data-catalog-take-ownership.png)

4. 표시 유형을 제한하려면 **표시 유형** 섹션에서 **소유자 및 다음 사용자**를 선택하고 **추가**를 선택합니다. 텍스트 상자에 사용자 전자 메일 주소를 입력하고 **ENTER**를 누릅니다.

    ![Azure Data Catalog - 액세스 제한](media/register-data-assets-tutorial/data-catalog-ownership.png)

## <a name="remove-data-assets"></a>데이터 자산 제거

이 연습에서는 Azure Data Catalog 포털을 사용하여 등록된 데이터 자산에서 미리 보기 데이터를 제거하고 해당 카탈로그에서 데이터 자산을 삭제합니다.

Azure Data Catalog에서 개별 자산을 삭제하거나 여러 자산을 삭제할 수 있습니다.

1. [Azure Data Catalog 홈 페이지](https://www.azuredatacatalog.com)로 이동합니다.

2. **검색** 텍스트 상자에 `tags:cycles`를 입력하고 **ENTER**를 선택합니다.

3. 결과 목록에서 항목을 선택하고 다음 이미지처럼 도구 모음에서 **삭제** 를 선택합니다.

   ![Azure Data Catalog - 그리드 항목 삭제](media/register-data-assets-tutorial/data-catalog-delete-grid-item.png)

   목록 보기를 사용하는 경우 다음 이미지처럼 확인란은 항목의 왼쪽에 있습니다.

   ![Azure Data Catalog - 목록 항목 삭제](media/register-data-assets-tutorial/data-catalog-delete-list-item.png)

   다음 이미지처럼 여러 데이터 자산을 선택하고 삭제할 수도 있습니다.

   ![Azure Data Catalog - 여러 데이터 자산 삭제](media/register-data-assets-tutorial/data-catalog-delete-assets.png)

> [!NOTE]
> 카탈로그의 기본 동작을 통해 사용자는 모든 데이터 원본을 등록할 수 있으며 등록된 데이터 자산을 삭제할 수 있습니다. Azure Data Catalog의 표준 버전에 포함된 관리 기능은 자산의 소유권을 얻고, 자산을 검색할 수 있는 사람을 제한하며, 자산을 삭제할 수 있는 사람을 제한하는 추가 옵션을 제공합니다.

## <a name="clean-up-resources"></a>리소스 정리

[데이터 자산 제거](#remove-data-assets) 단계에 따라 이 자습서를 수행하는 동안 사용했을 수 있는 자산을 정리합니다.

## <a name="summary"></a>요약

이 자습서에서는 엔터프라이즈 데이터 자산 등록, 주석 추가, 검색, 관리를 비롯하여 Azure Data Catalog의 필수 기능을 살펴보았습니다. 이제 자습서를 완료했습니다. 시작할 시간입니다. 사용자 및 팀이 필요로 하는 데이터 원본을 등록하고, 카탈로그를 사용하도록 동료를 초대하여 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [지원되는 데이터 원본](data-catalog-dsr.md)
