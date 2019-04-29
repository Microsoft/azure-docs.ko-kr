---
title: Java에서 Azure Search 시작 - Azure Search
description: Java를 프로그래밍 언어로 사용하여 Azure에서 호스트된 클라우드 검색 애플리케이션을 빌드하는 방법입니다.
services: search
author: jj09
manager: jlembicz
ms.service: search
ms.topic: conceptual
ms.date: 08/26/2018
ms.author: jjed
ms.custom: seodec2018
ms.openlocfilehash: d16f20e3c2dfa3d670006e44f0072a3871d41c3f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61289878"
---
# <a name="get-started-with-azure-search-in-java"></a>Java에서 Azure Search 시작
> [!div class="op_single_selector"]
> * [포털](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

검색 환경에 Azure Search를 사용하는 사용자 지정 Java 검색 애플리케이션을 빌드하는 방법에 대해 알아봅니다. 이 자습서에서는 [Azure Search 서비스 REST API](https://msdn.microsoft.com/library/dn798935.aspx)를 사용하여 이 연습에서 사용되는 개체 및 작업을 생성합니다.

이 샘플을 실행하려면 [Azure Portal](https://portal.azure.com)에서 등록할 수 있는 Azure Search 서비스가 있어야 합니다. 단계별 지침은 [포털에서 Azure Search 서비스 만들기](search-create-service-portal.md)를 참조하세요.

이 샘플을 빌드 및 테스트하는 데 사용된 소프트웨어는 다음과 같습니다.

* [Eclipse IDE for Java EE Developers](https://www.eclipse.org/downloads/packages/release/photon/r/eclipse-ide-java-ee-developers). EE 버전을 다운로드해야 합니다. 확인 단계 중 하나에 이 버전에만 있는 기능이 필요합니다.
* [JDK 8u181](https://aka.ms/azure-jdks)
* [Apache Tomcat 8.5.33](https://tomcat.apache.org/download-80.cgi#8.5.33)

## <a name="about-the-data"></a>데이터 정보
이 샘플 애플리케이션에서는 데이터 집합 크기를 줄이기 위해 Rhode Island 주에 대해 필터링된 [USGS(United States Geological Services)](https://geonames.usgs.gov/domestic/download_data.htm)의 데이터를 사용합니다. 이 데이터를 사용하여 병원 및 학교와 같은 랜드마크 빌딩뿐만 아니라 강, 호수, 산 등의 지질학적 특징을 반환하는 검색 애플리케이션을 빌드합니다.

이 애플리케이션에서 **SearchServlet.java** 프로그램은 [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx) 구문을 사용하여 인덱스를 빌드 및 로드하며, 이를 통해 Azure SQL Database에서 필터링된 USGS 데이터 세트를 검색합니다. 온라인 데이터 원본에 대한 미리 정의된 자격 증명 및 연결 정보는 프로그램 코드에서 제공됩니다. 데이터 액세스 측면에서 추가 구성은 필요하지 않습니다.

> [!NOTE]
> 무료 가격 책정 계층의 문서 제한(10,000개) 미만으로 유지하기 위해 이 데이터 세트에 필터를 적용했습니다. 표준 계층을 사용하는 경우에는 이 제한이 적용되지 않으며 이 코드를 수정하여 더 큰 데이터 세트를 사용할 수 있습니다. 각 가격 책정 계층의 용량에 대한 자세한 내용은 [제한 및 제약 조건](search-limits-quotas-capacity.md)을 참조하세요.
> 
> 

## <a name="about-the-program-files"></a>프로그램 파일 정보
다음 목록에서는 이 샘플과 관련된 파일을 설명합니다.

* Search.jsp: 사용자 인터페이스를 제공합니다.
* SearchServlet.java: 메서드(MVC의 컨트롤러와 유사)를 제공합니다.
* SearchServiceClient.java: HTTP 요청을 처리합니다.
* SearchServiceHelper.java: 정적 메서드를 제공하는 도우미 클래스입니다.
* Document.java: 데이터 모델을 제공합니다.
* config.properties: Search 서비스 URL 및 api-key를 설정합니다.
* pom.xml: Maven 종속성입니다.

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Azure Search 서비스의 서비스 이름 및 api-key 찾기
Azure Search에 대한 모든 REST API 호출에는 서비스 URL 및 api-key를 제공해야 합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 점프 모음에서 **Search 서비스**를 클릭하여 구독에 프로비전된 Azure Search 서비스를 모두 나열합니다.
3. 사용하려는 서비스를 선택합니다.
4. 서비스 대시보드에 필수 정보에 대한 타일 및 관리 키에 액세스할 수 있는 키 아이콘이 표시됩니다.
   
      ![][3]
5. 서비스 URL 및 관리 키를 복사합니다. 나중에 **config.properties** 파일에 추가할 때 필요합니다.

## <a name="download-the-sample-files"></a>샘플 파일 다운로드
1. GitHub에서 [search-java-indexer-demo](https://github.com/Azure-Samples/search-java-indexer-demo)로 이동합니다.
2. **Download ZIP**을 클릭하고 .zip 파일을 디스크에 저장한 다음 포함된 모든 파일을 추출합니다. 나중에 프로젝트를 쉽게 찾을 수 있도록 Java 작업 영역에 파일을 추출하는 것이 좋습니다.
3. 샘플 파일은 읽기 전용입니다. 폴더 속성을 마우스 오른쪽 단추로 클릭하고 읽기 전용 특성을 지웁니다.

이후의 모든 파일 수정 및 실행 문은 이 폴더의 파일에 대해 수행됩니다.  

## <a name="import-project"></a>프로젝트 가져오기
1. Eclipse에서 **File** > **Import** > **General** > **Existing Projects into Workspace**를 선택합니다.
   
    ![][4]
2. **Select root directory**에서 샘플 파일이 들어 있는 폴더를 찾습니다. .project 폴더가 포함된 폴더를 선택합니다. **Projects** 목록에 선택한 항목으로 프로젝트가 표시됩니다.
   
    ![][12]
3. **Finish**를 클릭합니다.
4. **Project Explorer** 를 사용하여 파일을 보고 편집합니다. 아직 열지 않은 경우 **Window** > **Show View** > **Project Explorer**를 클릭하거나 바로 가기를 사용하여 엽니다.

## <a name="configure-the-service-url-and-api-key"></a>서비스 URL 및 api-key 구성
1. **Project Explorer**에서 **config.properties**를 두 번 클릭하여 서버 이름 및 api-key가 포함된 구성 설정을 편집합니다.
2. 이 문서의 이전 단계를 참조하여 [config.properties](https://portal.azure.com)에 입력할 값을 가져오도록 **Azure Portal**에서 서비스 URL 및 api-key를 찾습니다.
3. **config.properties**에서 "Api Key"를 서비스의 api-key로 바꿉니다. 다음으로 서비스 이름(URL https://servicename.search.windows.net) 의 첫 번째 구성 요소)이 같은 파일의 “서비스 이름"을 대체합니다.
   
    ![][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>프로젝트, 빌드 및 런타임 환경 구성
1. Eclipse의 Project Explorer에서 프로젝트 > **Properties** > **Project Facets**를 마우스 오른쪽 단추로 클릭합니다.
2. **Dynamic Web Module**, **Java** 및 **JavaScript**를 선택합니다.
   
    ![][6]
3. **적용**을 클릭합니다.
4. **Window** > **Preferences** > **Server** > **Runtime Environments** > **Add..** 를 선택합니다.
5. Apache를 확장하고 이전에 설치한 Apache Tomcat 서버의 버전을 선택합니다. 예제 시스템에는 버전 8이 설치되어 있습니다.
   
    ![][7]
6. 다음 페이지에서 Tomcat 설치 디렉터리를 지정합니다. Windows 컴퓨터의 경우 일반적으로 C:\Program Files\Apache Software Foundation\Tomcat *버전*입니다.
7. **Finish**를 클릭합니다.
8. **Window** > **Preferences** > **Java** > **Installed JREs** > **Add**를 선택합니다.
9. **Add JRE**에서 **Standard VM**을 선택합니다.
10. **다음**을 클릭합니다.
11. JRE 정의의 JRE 홈에서 **Directory**를 클릭합니다.
12. **Program Files** > **Java**로 이동하여 이전에 설치한 JDK를 선택합니다. JDK를 JRE로 선택해야 합니다.
13. Installed JREs에서 **JDK**를 선택합니다. 설정은 다음 스크린샷과 유사해야 합니다.
    
    ![][9]
14. 필요에 따라 **Window** > **Web Browser** > **Internet Explorer**를 선택하여 외부 브라우저 창에서 애플리케이션을 엽니다. 외부 브라우저를 사용하면 웹 애플리케이션 환경이 향상됩니다.
    
    ![][8]

이제 구성 작업을 완료했습니다. 다음으로, 프로젝트를 빌드 및 실행합니다.

## <a name="build-the-project"></a>프로젝트 빌드
1. Project Explorer에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭하고 **Run As** > **Maven build...** 를 선택하여 프로젝트를 구성합니다.
   
    ![][10]
2. Edit Configuration에서 Goals에 "clean install"을 입력하고 **Run**을 클릭합니다.

콘솔 창에 상태 메시지가 출력됩니다. 오류 없이 빌드에 성공했음을 나타내는 BUILD SUCCESS가 표시되어야 합니다.

## <a name="run-the-app"></a>앱 실행
이 마지막 단계에서는 로컬 서버 런타임 환경에서 애플리케이션을 실행합니다.

Eclipse에서 서버 런타임 환경을 아직 지정하지 않은 경우 이 작업을 먼저 수행해야 합니다.

1. Project Explorer에서 **WebContent**를 확장합니다.
2. **Search.jsp** > **Run As** > **Run on Server**를 마우스 오른쪽 단추로 클릭합니다. Apache Tomcat 서버를 선택하고 **Run**을 클릭합니다.

> [!TIP]
> 기본이 아닌 작업 영역을 사용하여 프로젝트를 저장한 경우 서버 시작 오류를 방지하기 위해 프로젝트 위치를 가리키도록 **Run Configuration** 을 수정해야 합니다. Project Explorer에서 **Search.jsp** > **Run As** > **Run Configurations**를 마우스 오른쪽 단추로 클릭합니다. Apache Tomcat 서버를 선택합니다. **Arguments**를 클릭합니다. **Workspace** 또는 **File System**을 클릭하여 프로젝트가 포함된 폴더를 설정합니다.
> 
> 

애플리케이션을 실행하면 용어를 입력할 수 있는 검색 상자를 제공하는 브라우저 창이 표시됩니다.

1분 정도 기다렸다가 **Search** 를 클릭하여 인덱스를 만들고 로드할 서비스 이름을 제공합니다. HTTP 404 오류가 발생하는 경우 좀 더 기다렸다가 다시 시도해야 합니다.

## <a name="search-on-usgs-data"></a>USGS 데이터 검색
USGS 데이터 집합에는 Rhode Island 주와 관련된 레코드가 포함되어 있습니다. 빈 검색 상자에서 **검색** 을 클릭하면 기본적으로 상위 50개 항목을 가져옵니다.

검색 용어를 입력하면 검색 엔진이 실행됩니다. 지역 이름을 입력해 봅니다. “Roger Williams”는 Rhode Island의 최초 주지사였습니다. 유명한 공원, 빌딩 및 학교가 그의 이름을 따라 이름을 지었습니다.

![][11]

다음과 같은 용어를 입력해 볼 수도 있습니다.

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>다음 단계
이것은 Java 및 USGS 데이터 세트를 기반으로 하는 첫 번째 Azure Search 자습서입니다. 앞으로 이 자습서를 확장하여 사용자 지정 솔루션에서 사용할 수 있는 추가 검색 기능을 보여 드릴 예정입니다.

Azure Search에 대한 약간의 배경 지식이 있는 경우 [검색 페이지](search-pagination-page-layout.md)를 보강하거나 [패싯 탐색](search-faceted-navigation.md)을 구현하는 등 이 샘플을 기반으로 추가 실험을 수행할 수 있습니다. 또한 사용자가 결과 페이지를 차례로 탐색할 수 있도록 개수를 추가하고 문서를 일괄 처리하여 검색 결과 페이지를 개선할 수 있습니다.

Azure Search를 처음 사용하세요? 다른 자습서를 통해 만들 수 있는 항목에 대한 이해를 높여 보세요. 더 많은 리소스를 보려면 [설명서 페이지](https://azure.microsoft.com/documentation/services/search/) 를 방문하세요. 

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
