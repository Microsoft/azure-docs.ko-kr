---
title: Striim를 사용 하 여 Azure Cosmos DB Cassandra API 계정으로 데이터 마이그레이션
description: Striim를 사용 하 여 Oracle 데이터베이스에서 Azure Cosmos DB Cassandra API 계정으로 데이터를 마이그레이션하는 방법에 대해 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: af99f369245d6006d0d4784e572020b820e406a3
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266052"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>Striim를 사용 하 여 Azure Cosmos DB Cassandra API 계정으로 데이터 마이그레이션

Azure marketplace의 Striim 이미지는 데이터 웨어하우스 및 데이터베이스에서 Azure로의 지속적인 실시간 데이터 이동을 제공 합니다. 데이터를 이동 하는 동안 인라인 비 정규화, 데이터 변환을 수행 하 고 실시간 분석 및 데이터 보고 시나리오를 사용할 수 있습니다. Striim를 시작 하 여 계속 해 서 엔터프라이즈 데이터를 Azure Cosmos DB Cassandra API로 이동 하는 것이 쉽습니다. Azure는 Striim을 쉽게 배포 하 고 Azure Cosmos DB로 데이터를 마이그레이션할 수 있도록 해 주는 marketplace 제품을 제공 합니다. 

이 문서에서는 Striim를 사용 하 여 **Oracle 데이터베이스** 에서 **Azure Cosmos DB Cassandra API 계정**으로 데이터를 마이그레이션하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Azure 구독](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

* 일부 데이터가 포함 된 온-프레미스에서 실행 되는 Oracle 데이터베이스

## <a name="deploy-the-striim-marketplace-solution"></a>Striim marketplace 솔루션 배포

1. [Azure 포털](https://portal.azure.com/)할 수 있습니다.

1. **리소스 만들기** 를 선택 하 고 Azure Marketplace에서 **Striim** 를 검색 합니다. 첫 번째 옵션을 선택 하 고 **만들기**를 선택 합니다.

   ![Striim marketplace 항목 찾기](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. 그런 다음 Striim 인스턴스의 구성 속성을 입력 합니다. Striim 환경은 가상 컴퓨터에 배포 됩니다. **기본 사항** 창에서 vm **사용자 이름**, **vm 암호** 를 입력 합니다 .이 암호는 vm에 SSH를 사용 하는 데 사용 됩니다. Striim를 배포 하려는 **구독**, **리소스 그룹**및 **위치 세부 정보** 를 선택 합니다. 완료 되 면 **확인**을 선택 합니다.

   ![Striim에 대한 기본 설정 구성](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)


1. **Striim 클러스터 설정** 창에서 Striim 배포 유형과 가상 머신 크기를 선택 합니다.

   |설정 | 값 | 설명 |
   | ---| ---| ---|
   |Striim 배포 유형 |독립 실행형 | Striim는 **독립 실행형** 또는 **클러스터** 배포 유형에 서 실행할 수 있습니다. 독립 실행형 모드는 단일 가상 컴퓨터에 Striim 서버를 배포 하 고 데이터 볼륨에 따라 Vm의 크기를 선택할 수 있습니다. 클러스터 모드는 선택한 크기를 가진 둘 이상의 Vm에 Striim 서버를 배포 합니다. 노드가 3 개 이상인 클러스터 환경은 자동 고가용성 및 장애 조치 (failover)를 제공 합니다.</br></br> 이 자습서에서는 독립 실행형 옵션을 선택할 수 있습니다. 기본 "Standard_F4s" 크기 VM을 사용 합니다. | 
   | Striim 클러스터의 이름|    < Striim_cluster_Name >|  Striim 클러스터의 이름입니다.|
   | Striim 클러스터 암호|   < Striim_cluster_password >|  클러스터에 대한 암호입니다.|

   양식을 채운 후 **확인** 을 선택 하 여 계속 합니다.

1. **Striim access 설정** 창에서 Striim UI에 로그인 하는 데 사용 하려는 **공용 IP 주소** (기본값 선택), **Striim에 대한 도메인 이름**, **관리자 암호** 를 구성 합니다. VNET 및 서브넷을 구성 합니다 (기본값 선택). 세부 정보를 입력 한 후 **확인** 을 선택 하 여 계속 합니다.

   ![Striim access 설정](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Azure에서 배포의 유효성을 검사 하 고 모든 것이 양호한 지 확인 합니다. 유효성 검사를 완료 하는 데 몇 분이 걸립니다. 유효성 검사가 완료 되 면 **확인**을 선택 합니다.
  
1. 마지막으로 사용 약관을 검토 하 고 **만들기** 를 선택 하 여 Striim 인스턴스를 만듭니다. 

## <a name="configure-the-source-database"></a>원본 데이터베이스 구성

이 섹션에서는 Oracle 데이터베이스를 데이터 이동의 원본으로 구성 합니다.  Oracle에 연결 하려면 [ORACLE JDBC 드라이버가](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) 필요 합니다. 원본 Oracle 데이터베이스에서 변경 내용을 읽으려면 [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) 또는 [xstream api](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647)를 사용할 수 있습니다. Oracle 데이터베이스에서 데이터를 읽거나 쓰거나 유지 하려면 Oracle JDBC 드라이버가 Striim의 Java 클래스 경로에 있어야 합니다.

로컬 컴퓨터에 [ojdbc8](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) 드라이버를 다운로드 합니다. 나중에 Striim 클러스터에 설치 합니다.

## <a name="configure-target-database"></a>대상 데이터베이스 구성

이 섹션에서는 Azure Cosmos DB Cassandra API 계정을 데이터 이동에 대한 대상으로 구성 합니다.

1. Azure Portal를 사용 하 여 [Azure Cosmos DB Cassandra API 계정을](create-cassandra-dotnet.md#create-a-database-account) 만듭니다.

1. Azure Cosmos 계정의 **데이터 탐색기** 창으로 이동 합니다. 새 **테이블** 을 선택 하 여 새 컨테이너를 만듭니다. Oracle 데이터베이스에서 Azure Cosmos DB로 *제품* 및 *주문* 데이터를 마이그레이션하는 경우를 가정 합니다. Orders 컨테이너를 사용 하 여 **StriimDemo** 라는 새 Keyspace을 만듭니다. **1000 rus**를 사용 하 여 컨테이너를 프로 비전 합니다 .이 예제에서는 1000 rus를 사용 하지만 작업에 대해 **예상 되는** 처리량을 사용 해야 합니다. 이러한 값은 원본 데이터에 따라 달라 집니다. 

   ![Cassandra API 계정 만들기](./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Oracle을 구성 하 여 데이터 흐름 Azure Cosmos DB

1. 이제 Striim로 돌아가 보겠습니다. Striim와 상호 작용 하기 전에 이전에 다운로드 한 Oracle JDBC 드라이버를 설치 합니다.

1. Azure Portal에서 배포한 Striim 인스턴스로 이동 합니다. 상단 메뉴 모음에서 **연결** 단추를 선택 하 고 **SSH** 탭에서 **VM 로컬 계정을 사용 하 여 로그인** 에 URL을 복사 합니다. 필드를 선택 합니다.

   ![SSH URL 가져오기](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. 새 터미널 창을 열고 Azure Portal에서 복사한 SSH 명령을 실행 합니다. 이 문서에서는 MacOS에서 terminal를 사용 하 고, Windows 컴퓨터에서 PuTTY 또는 다른 SSH 클라이언트를 사용 하 여 유사한 지침을 따를 수 있습니다. 메시지가 표시 되 면 **예** 를 입력 하 여 계속 하 고 이전 단계에서 가상 머신에 대해 설정한 **암호** 를 입력 합니다.

   ![Striim VM에 연결](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. 이제 새 터미널 탭을 열어 이전에 다운로드 한 **ojdbc8** 파일을 복사 합니다. 다음 SCP 명령을 사용 하 여 로컬 컴퓨터에서 Azure에서 실행 되는 Striim 인스턴스의 tmp 폴더로 jar 파일을 복사 합니다.

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![위치 컴퓨터에서 Striim로 Jar 파일을 복사 합니다.](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. 그런 다음 Striim 인스턴스로 SSH를 수행 하 고 sudo로 로그인 하는 창으로 다시 이동 합니다. 다음 명령을 사용 하 여 **/tmp** 디렉터리에서 Striim 인스턴스의 **lib** 디렉터리로 **ojdbc8** 파일을 이동 합니다.

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![Jar 파일을 lib 폴더로 이동](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. 동일한 터미널 창에서 다음 명령을 실행 하 여 Striim 서버를 다시 시작 합니다.

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim을 시작 하는 데 1 분 정도 걸립니다. 상태를 보려면 다음 명령을 실행 합니다. 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. 이제 Azure로 다시 이동 하 여 Striim VM의 공용 IP 주소를 복사 합니다. 

   ![Striim VM IP 주소 복사](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. Striim의 웹 UI로 이동 하려면 브라우저에서 새 탭을 열고 다음을 수행 하 여 공용 IP를 복사 합니다. 9080. Azure Portal에서 지정한 관리자 암호와 함께 **관리자** 사용자 이름을 사용 하 여 로그인 합니다.

   ![Striim에 로그인](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. 이제 Striim의 홈 페이지에 도착 합니다. **대시보드**, **앱**및 **sourcepreview**의 세 가지 창이 있습니다. 대시보드 창에서는 데이터를 실시간으로 이동 하 고 시각화할 수 있습니다. 앱 창에는 스트리밍 데이터 파이프라인 또는 데이터 흐름이 포함 됩니다. 페이지의 오른쪽에는 데이터를 이동 하기 전에 미리 볼 수 있는 SourcePreview가 있습니다.

1. **앱** 창을 선택 하면 지금은이 창에 집중할 것입니다. Striim에 대해 학습 하는 데 사용할 수 있는 다양 한 샘플 앱이 있습니다. 그러나이 문서에서는 사용자가 직접 만듭니다. 오른쪽 위 모서리에서 **앱 추가** 단추를 선택 합니다.

   ![Striim 앱 추가](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. 몇 가지 방법으로 Striim 응용 프로그램을 만들 수 있습니다. 이 시나리오의 경우 **처음부터 시작** 을 선택 합니다.

   ![앱을 처음부터 시작](./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png)

1. 응용 프로그램에 대한 친숙 한 이름 (예: **oraToCosmosDB** )을 지정 하 고 **저장**을 선택 합니다.

   ![새 애플리케이션 만들기](./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png)

1. 스트리밍 응용 프로그램을 만들기 위해 box 커넥터에서 끌어서 놓을 수 있는 흐름 디자이너에 도달 하 게 됩니다. 검색 창에서 **oracle** 을 입력 하 고 **oracle CDC** 원본을 앱 캔버스로 끌어다 놓습니다.  

   ![Oracle CDC 원본](./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png)

1. Oracle 인스턴스의 원본 구성 속성을 입력 합니다. 원본 이름은 Striim 응용 프로그램에 대한 명명 규칙 일 뿐 이며 **src_onPremOracle**와 같은 이름을 사용할 수 있습니다. 또한 어댑터 유형, 연결 URL, 사용자 이름, 암호, 테이블 이름 등의 기타 세부 정보를 입력 합니다. **저장** 을 선택 하 여 계속 합니다.

   ![원본 매개 변수 구성](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png)

1. 이제 스트림의 웨이브 아이콘을 클릭 하 여 대상 Azure Cosmos DB 인스턴스를 연결 합니다. 

   ![대상에 연결](./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png)

1. 대상을 구성 하기 전에 [Baltimore 루트 인증서를 Striim의 Java 환경에](/azure/java/java-sdk-add-certificate-ca-store#to-add-a-root-certificate-to-the-cacerts-store)추가 했는지 확인 합니다.

1. 대상 Azure Cosmos DB 인스턴스의 구성 속성을 입력 하 고 **저장** 을 선택 하 여 계속 합니다. 유의 해야 할 주요 매개 변수는 다음과 같습니다.

   * **어댑터** - **databasewriter**를 사용 합니다. Azure Cosmos DB Cassandra API에 쓸 때 DatabaseWriter가 필요 합니다. Cassandra driver 3.6.0는 Striim와 함께 제공 됩니다. DatabaseWriter가 Azure Cosmos 컨테이너에서 프로 비전 된 RUs 수를 초과 하면 응용 프로그램의 작동이 중단 됩니다.

   * **연결 url** -Azure Cosmos DB JDBC 연결 url을 지정 합니다. URL의 형식은`jdbc:cassandra://<contactpoint>:10350/<databaseName>?SSL=true`

   * **사용자 이름** -Azure Cosmos 계정 이름을 지정 합니다.
   
   * **암호** -Azure Cosmos 계정의 기본 키를 지정 합니다.

   * **테이블** -대상 테이블에는 기본 키가 있어야 하며 기본 키를 업데이트할 수 없습니다.

   ![대상 속성 구성](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png)

   ![대상 속성 구성](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png)

1. 이제 계속 해 서 Striim 응용 프로그램을 실행 합니다. 상단 메뉴 모음에서 **만든**를 선택 하 고 **앱 배포**를 선택 합니다. 배포 창에서 배포 토폴로지의 특정 부분에 대해 응용 프로그램의 특정 부분을 실행할지 여부를 지정할 수 있습니다. Azure를 통해 간단한 배포 토폴로지에서를 실행 하 고 있으므로 기본 옵션을 사용 합니다.

   ![앱 배포](./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png)


1. 이제 Striim을 통해 흐르는 데이터를 확인 하기 위해 스트림을 미리 봅니다. 웨이브 아이콘을 클릭 하 고 옆의 눈 모양 아이콘을 클릭 합니다. 을 배포한 후 스트림을 미리 보고 이동 하 여 데이터 흐름을 확인할 수 있습니다. **웨이브** 아이콘 및 옆의 **eyeball** 를 선택 합니다. 상단 메뉴 모음에서 **배포** 됨 단추를 선택 하 고 **앱 시작**을 선택 합니다.

   ![앱 시작](./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png)

1. Striim는 **CDC (변경 데이터 캡처)** 판독기를 사용 하 여 데이터베이스에 대한 새 변경 내용만 선택 합니다. 원본 테이블을 통해 흐르는 데이터가 있는 경우 해당 데이터를 볼 수 있습니다. 그러나이는 샘플 테이블 이므로 응용 프로그램에 연결 되지 않은 소스입니다. 샘플 데이터 생성기를 사용 하는 경우 Oracle 데이터베이스에 이벤트 체인을 삽입할 수 있습니다.

1. Striim 플랫폼을 통해 흐르는 데이터를 볼 수 있습니다. Striim는 테이블과 연결 된 모든 메타 데이터를 선택 합니다. 데이터를 모니터링 하 고 데이터가 올바른 대상에 있는지 확인 하는 데 도움이 됩니다.

   ![CDC 파이프라인 설정](./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png)

1. 마지막으로 Azure에 로그인 하 여 Azure Cosmos 계정으로 이동 하겠습니다. 데이터 탐색기를 새로 고치면 데이터가 도착 한 것을 볼 수 있습니다. 

Azure에서 Striim 솔루션을 사용 하 여 Oracle, Cassandra, MongoDB 및 기타 다양 한 원본에서 Azure Cosmos DB 하는 Azure Cosmos DB으로 데이터를 지속적으로 마이그레이션할 수 있습니다. 자세한 내용은 [Striim 웹 사이트](https://www.striim.com/)를 방문 하 여 [Striim의 30 일 무료 평가판을 다운로드](https://go2.striim.com/download-free-trial)하 고, Striim를 사용 하 여 마이그레이션 경로를 설정할 때 발생 하는 모든 문제에 대해 [지원 요청](https://go2.striim.com/request-support-striim) 을 파일에 추가 하세요.

## <a name="next-steps"></a>다음 단계

* Azure Cosmos DB SQL API로 데이터를 마이그레이션하는 경우 Striim를 [사용 하 여 Cassandra API 계정으로 데이터를 마이그레이션하는 방법](cosmosdb-sql-api-migrate-data-striim.md) 을 참조 하세요.

* [Azure Cosmos DB 메트릭을 사용 하 여 데이터 모니터링 및 디버그](use-metrics.md)
