---
title: Sisense를 사용 하 여 Azure 데이터 탐색기에서 데이터 시각화
description: 이 문서에서는 Sisense에 대한 Azure 데이터 탐색기 데이터 원본으로 설정 하 고 데이터를 시각화 하는 방법에 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: f0840b90e1036c23fa58d94515bfeb035299c07f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66358185"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Sisense의 Azure 데이터 탐색기에서 데이터 시각화

Sisense는 대화형 사용자 환경을 제공 하는 분석 앱을 빌드할 수 있도록 분석 비즈니스 인텔리전스 플랫폼입니다. 비즈니스 인텔리전스 및 소프트웨어를 보고 하는 대시보드를 액세스 하 고 몇 번의 클릭 만으로 데이터를 결합할 수 있습니다. 원본 구조적 및 비구조적 데이터에 연결 하, 최소한의 스크립팅 및 코딩을 사용 하 여 여러 원본에서 테이블을 조인, 대화형 웹 대시보드 및 보고서를 만들 수 있습니다. 이 문서에서는 Sisense에 대한 Azure 데이터 탐색기 데이터 원본으로 설정 하 고 샘플 클러스터에서 데이터를 시각화 하는 방법에 알아봅니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 완료 하려면 다음이 필요 합니다.

* [Sisense 앱을 설치 및 다운로드](https://documentation.sisense.com/latest/getting-started/download-install.htm) 

* 클러스터 및 StormEvents 샘플 데이터가 포함 된 데이터베이스를 만듭니다. 자세한 내용은 [빠른 시작: Azure Data Explorer 클러스터 및 데이터베이스 만들기](create-cluster-database-portal.md) 및 [Azure Data Explorer로 샘플 데이터 수집](ingest-sample-data.md)을 참조하세요.

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Azure 데이터 탐색기 JDBC 커넥터를 사용 하 여 Sisense 대시보드에 연결

1. 다음 jar 파일의 최신 버전 다운로드 *... \Sisense\DataConnectors\jdbcdrivers\adx* 

    * activation-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-annotations-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * mail-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar
    
1. 오픈 **Sisense** 앱.
1. 선택 **데이터** 탭을 선택한 **+ ElastiCube** 새 ElastiCube 모델을 만듭니다.
    
    ![ElastiCube 선택](media/sisense/data-select-elasticube.png)

1. **새 ElastiCube 모델 추가**, ElastiCube 모델 이름 및 **저장**합니다.
   
    ![새 ElastiCube 모델 추가](media/sisense/add-new-elasticube-model.png)

1. 선택 **+ 데이터**입니다.

    ![데이터 단추 선택](media/sisense/select-data.png)

1. **커넥터 선택** 탭을 선택 합니다 **제네릭 JDBC** 커넥터.

    ![JDBC 커넥터를 선택 합니다.](media/sisense/select-connector.png)

1. 에 **연결** 탭에서 다음과 같은 필드를 완료 합니다 **제네릭 JDBC** 커넥터를 선택 **다음**합니다.

    ![JDBC 커넥터 설정](media/sisense/jdbc-connector.png)

    |필드 |설명 |
    |---------|---------|
    |연결 문자열     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |JDBC Jar 폴더  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |드라이버의 클래스 이름    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |사용자 이름   |    AAD 사용자 이름     |
    |암호     |   AAD 사용자 암호      |

1. 에 **데이터 선택** 탭에서 검색 **데이터베이스 선택** 권한이 있는 관련 데이터베이스를 선택 합니다. 이 예제에서는 선택 *test1*합니다.

    ![데이터베이스 선택](media/sisense/select-database.png)

1. *테스트* (데이터베이스 이름) 창:
    1. 테이블을 미리 보고 테이블 열 이름을 참조 하는 테이블 이름을 선택 합니다. 불필요 한 열을 제거할 수 있습니다.
    1. 관련 테이블 선택 테이블의 확인란을 선택 합니다. 
    1. **완료**를 선택합니다.

    ![테이블 선택](media/sisense/select-table-see-columns.png)    

1. 선택 **빌드** 데이터 집합을 만들려고 합니다. 

    * 에 **빌드할** 창에서 **빌드**합니다.

      ![빌드 창](media/sisense/build-window.png)

    * 빌드 프로세스는 완전 하 고 선택 될 때까지 기다립니다 **빌드에 성공**합니다.

      ![빌드 성공](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Sisense 대시보드 만들기

1. **Analytics** 탭을 선택 **+**  >  **새 대시보드** 이 데이터 집합에서 대시보드를 만들려고 합니다.

    ![새 대시보드](media/sisense/new-dashboard.png)

1. 대시보드를 선택 하 고 선택 **만들기**합니다. 

    ![대시보드 만들기](media/sisense/create-dashboard.png)

1. 아래 **새로운 위젯을**를 선택 **+ 데이터 선택** 새로운 위젯을 만드는 합니다. 

    ![필드 StormEvents 대시보드에 추가](media/sisense/storm-dashboard-add-field.png)  

1. 선택 **+ 추가 데이터가 더** 그래프에 추가 열을 추가 합니다. 

    ![그래프에 데이터 추가](media/sisense/add-more-data.png)

1. 선택 **위젯 +** 다른 위젯에서 만들려고 합니다. 대시보드를 다시 정렬 하는 끌어서 놓기 위젯입니다.

    ![Storm 대시보드](media/sisense/final-dashboard.png)

이제 시각적 분석을 사용 하 여 데이터 탐색, 추가 대시보드를 작성를 비즈니스에 영향을 줄 수 있도록 실질적인 통찰력으로 데이터를 변환 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기용 쿼리 작성](write-queries.md)

