---
title: Azure Portal을 사용하여 HDInsight의 Apache Hadoop 클러스터 관리
description: Azure Portal을 사용하여 HDInsight 클러스터를 만들고 관리하는 방법에 대해 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 003aeadba1f4683af40f390d40dd3bbe32e02a83
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096363"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Azure Portal을 사용하여 HDInsight의 Apache Hadoop 클러스터 관리

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

[Azure Portal][azure-portal]을 사용하여 Azure HDInsight에서 [Apache Hadoop](https://hadoop.apache.org/) 클러스터를 관리할 수 있습니다. 다른 도구를 사용하여 HDInsight에서 Hadoop 클러스터를 관리하는 방법에 대한 정보를 보려면 위쪽에 있는 탭 선택기를 사용하세요.

## <a name="prerequisites"></a>필수 조건
- Azure 구독. [Azure 평가판](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- HDInsight의 기존 Apache Hadoop 클러스터  [Azure 포털을 사용하여 HDInsight에서 Linux 기반 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-portal.md)를 참조하세요.

## <a name="getting-started"></a>시작하기
[https://portal.azure.com](https://portal.azure.com)에 로그인합니다.

## <a name="showClusters"></a> 클러스터 나열 및 표시
**HDInsight 클러스터** 페이지에는 기존 클러스터가 나열됩니다.  포털에서 다음을 수행합니다.
1. 왼쪽 메뉴에서 **모든 서비스**를 선택합니다.
2. **ANALYTICS**에서 **HDInsight 클러스터**를 선택합니다.

## <a name="homePage"></a> 클러스터 홈 페이지 
클러스터 이름을 선택 합니다 [ **HDInsight 클러스터** ](#showClusters) 페이지입니다.  그러면 다음 이미지와 비슷한 **개요** 보기가 열립니다.

![Azure Portal HDInsight 클러스터 요점](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**상단 메뉴:**  

| 항목| 설명 |
|---|---|
|이동|클러스터를 다른 리소스 그룹 또는 다른 구독으로 이동시킵니다.|
|삭제|클러스터를 삭제합니다. |
|새로 고침|보기를 새로 고칩니다.|

**왼쪽 메뉴:**  
  - **왼쪽 상단 메뉴**

    | 항목| 설명 |
    |---|---|
    |개요|클러스터에 대한 일반 정보를 제공합니다.|
    |활동 로그|활동 로그를 표시하고 쿼리합니다.|
    |액세스 제어(IAM)|역할 할당을 사용합니다.  [역할 할당을 사용하여 Azure 구독 리소스에 대한 액세스 관리](../role-based-access-control/role-assignments-portal.md)를 참조하세요.|
    |태그들|태그를 사용하면 클라우드 서비스의 사용자 지정 분류를 정의하기 위한 키/값 쌍을 설정할 수 있습니다. 예를 들어 **project**라는 키를 만든 다음 특정 프로젝트와 연결된 모든 서비스에 공통 값을 사용할 수 있습니다.|
    |문제 진단 및 해결|문제 해결 정보를 표시합니다.|
    |빠른 시작|HDInsight를 사용하여 시작하는 데 도움이 되는 정보를 표시합니다.|
    |도구|HDInsight 관련 도구에 대한 도움말 정보입니다.|

  - **설정 메뉴**  

    | 항목| 설명 |
    |---|---|
    |클러스터 크기|클러스터 작업자 노드의 수를 확인하고, 늘리거나 줄입니다. [클러스터 크기 조정](hdinsight-administer-use-portal-linux.md#scale-clusters)을 참조하세요.|
    |할당량 한도|구독에 사용된 코어 및 사용 가능한 코어를 표시합니다.|
    |SSH + 클러스터 로그인|SSH(보안 셸) 연결을 사용하여 클러스터에 연결하는 지침을 보여줍니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.|
    |Data Lake Storage Gen1|Data Lake Storage Gen1에 대한 액세스를 구성합니다.  [빠른 시작: HDInsight에서 클러스터 설정](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)을 참조하세요.|
    |Storage 계정|스토리지 계정 및 키를 봅니다. 저장소 계정은 클러스터를 만드는 과정에서 구성됩니다.|
    |애플리케이션|HDInsight 애플리케이션을 추가/제거합니다.  [사용자 지정 HDInsight 애플리케이션 설치](hdinsight-apps-install-custom-applications.md)를 참조하세요.|
    |스크립트 작업|클러스터에서 Bash 스크립트를 실행합니다. [스크립트 작업을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.|
    |외부 Metastore|[Apache Hive](https://hive.apache.org/) 및 [Apache Oozie](https://oozie.apache.org/) Metastore를 표시합니다. Metastore는 클러스터 생성 과정 중에만 구성될 수 있습니다.|
    |HDInsight 파트너|현재 HDInsight 파트너를 추가/제거합니다.|
    |properties|[클러스터 속성](#properties)을 봅니다.|
    |잠금|클러스터가 수정되거나 삭제되지 않도록 잠금을 추가합니다.|
    |Automation 스크립트|클러스터에 대한 Azure Resource Manager 템플릿을 표시하고 내보냅니다. 현재는 Azure Storage 계정만 내보낼 수 있습니다. [Azure Resource Manager 템플릿을 사용하여 HDInsight에서 Linux 기반 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-arm-templates.md)를 참조하세요.|

  - **모니터링 메뉴**

    | 항목| 설명 |
    |---|---|
    |경고|경고와 작업을 관리합니다.|
    |메트릭|Azure Monitor 로그에서 클러스터 메트릭을 모니터링 합니다.|
    |진단 설정|진단 메트릭을 저장하는 위치에 대한 설정입니다.|
    |Operations Management Suite|Azure Operations Management Suite (OMS) 및 Azure Monitor 로그에서 클러스터를 모니터링 합니다.|

  - **지원 + 문제 해결 메뉴**

    | 항목| 설명 |
    |---|---|
    |리소스 상태|[Azure 리소스 상태 개요](../service-health/resource-health-overview.md)를 참조하세요.|
    |새 지원 요청|Microsoft 지원에 지원 티켓을 만들 수 있습니다.|

## <a name="properties"></a> 클러스터 속성

[클러스터 홈 페이지](#homePage)에 있는 **설정**에서 **속성**을 선택합니다.

|항목 | 설명 |
|---|---|
|호스트 이름|클러스터 이름.|
|클러스터 URL|Ambari 웹 인터페이스에 대한 URL입니다.|
|SSH (보안 셸)|SSH를 통해 클러스터에 액세스할 때 사용할 사용자 이름과 호스트 이름입니다.|
|상태|다음 중 하나입니다. 중단됨, 수락됨, 클러스터 스토리지가 프로비전됨, Azure VM 구성, HDInsight 구성, 작동 가능, 실행 중, 오류, 삭제 중, 삭제됨, 시간이 초과됨, 삭제가 큐에 대기됨, 삭제 시간이 초과됨, 삭제 오류, 패치가 큐에 대기됨, 인증서 롤오버가 큐에 대기됨, 크기가 조정되어 큐에 대기됨 또는 클러스터 사용자 지정|
|지역|Azure 위치입니다. 지원되는 Azure 위치의 목록은 [HDInsight 가격 책정](https://azure.microsoft.com/pricing/details/hdinsight/)의 **지역** 드롭다운 목록 상자를 참조하세요.|
|날짜 생성됨|클러스터가 배포된 날짜입니다.|
|운영 체제|**Windows** 또는 **Linux**입니다.|
|Type|Hadoop, HBase, Storm, Spark입니다.|
|Version|[HDInsight 버전](hdinsight-component-versioning.md) 참조|
|구독|구독 이름입니다.|
|기본 데이터 원본|기본 클러스터 파일 시스템입니다.|
|작업자 노드 크기|선택한 작업자 노드의 VM 크기입니다.|
|헤드 노드 크기|선택한 헤드 노드의 VM 크기입니다.|
|가상 네트워크|배포 시 하나를 선택한 경우 클러스터가 배포 된 가상 네트워크의 이름입니다.|

## <a name="move-clusters"></a>클러스터 이동

다른 Azure 리소스 그룹 또는 다른 구독에 HDInsight 클러스터를 이동할 수 있습니다.

[클러스터 홈 페이지](#homePage)에서 다음을 수행합니다.

1. 상단 메뉴에서 **이동**을 선택합니다.
2. **다른 리소스 그룹으로 이동** 또는 **다른 구독으로 이동**을 선택합니다.
3. 새 페이지의 지침을 따릅니다.

## <a name="delete-clusters"></a>클러스터 삭제
클러스터를 삭제하더라도 기본 저장소 계정이나 연결된 저장소 계정은 삭제하지 않습니다. 동일한 저장소 계정과 동일한 Metastore를 사용하여 클러스터를 다시 만들 수 있습니다. 클러스터를 다시 만들 때 새 기본 Blob 컨테이너를 사용하는 것이 좋습니다.

[클러스터 홈 페이지](#homePage)에서 다음을 수행합니다.

1. 상단 메뉴에서 **삭제**를 선택합니다.
2. 새 페이지의 지침을 따릅니다.

참고 항목: [클러스터 일시 중지/종료](#pauseshut-down-clusters)

## <a name="add-additional-storage-accounts"></a>추가 저장소 계정 추가

클러스터가 생성된 후 Azure Storage 계정 및 Azure Data Lake Storage 계정을 더 추가할 수 있습니다. 자세한 내용은 [HDInsight에 추가 저장소 계정 추가](./hdinsight-hadoop-add-storage.md)를 참조하세요.

## <a name="scale-clusters"></a>클러스터 크기 조정
클러스터 크기 조정 기능을 사용하면 클러스터를 다시 만들지 않고 Azure HDInsight 클러스터에서 사용되는 작업자 노드 수를 변경할 수 있습니다.

> [!NOTE]  
> HDInsight 버전 3.1.3 이상을 사용하는 클러스터만 지원됩니다. 클러스터 버전을 알 수 없는 경우 속성 페이지를 확인할 수 있습니다.  클러스터 나열 및 표시를 참조하세요.

[클러스터 홈 페이지](#homePage)에서 다음을 수행합니다.

1. **설정**에서 **클러스터 크기**를 선택합니다.
2. 숫자 텍스트 상자에 **작업자 노드 수**를 입력합니다. 클러스터 노드 수에 대한 제한은 Azure 구독 간에 다릅니다. 제한을 늘리려면 청구 지원 팀에 문의할 수 있습니다.  비용 정보는 노드 수에 대한 변경 내용을 반영합니다.
3. **저장**을 선택합니다.

    ![HDinsight Hadoop Hbase Storm Spark 크기 조정](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster2.png)

데이터 노드 수 변경에 따른 영향은 다음과 같이 HDInsight에서 지원하는 각 클러스터 유형에 따라 다릅니다.

* Apache Hadoop

    모든 보류 중인 또는 실행 중인 작업에 영향을 주지 않고 실행되는 Hadoop 클러스터의 작업자 노드 수를 원활하게 늘릴 수 있습니다. 작업이 진행 중인 동안에 새 작업을 제출할 수 있습니다. 크기 조정 작업의 오류는 정상적으로 처리되므로 클러스터는 항상 기능 상태로 남아 있습니다.

    데이터 노드 수를 줄여 Hadoop 클러스터를 축소하면 클러스터의 서비스 중 일부가 다시 시작됩니다. 그러면 실행 중인 작업과 보류 중인 작업이 크기 조정 작업을 완료하지 못하고 실패합니다. 그러나 작업이 완료되면 작업을 다시 제출할 수 있습니다.
* Apache HBase

    HBase 클러스터가 실행 중인 동안 데이터 노드를 원활하게 추가하거나 제거할 수 있습니다. 지역 서버는 크기 조정 작업을 완료하는 몇 분 안에 자동으로 균형을 맞춥니다. 그러나 클러스터의 헤드 노드에 로그인한 다음 명령 프롬프트 창에서 다음 명령을 실행하여 자동으로 지역 서버의 균형을 맞출 수도 있습니다.

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    HBase 셸을 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Apache HBase 예제 시작](hbase/apache-hbase-tutorial-get-started-linux.md)을 참조하세요.

* Apache Storm

    실행 중인 동안 Storm 클러스터에 데이터 노드를 원활하게 추가하거나 제거할 수 있습니다. 하지만 크기 조정 작업이 성공적으로 완료되면 토폴로지 균형을 다시 조정해야 합니다.

    다음 두 가지 방법으로 사용하여 균형을 조정할 수 있습니다.

  * Storm 웹 UI
  * 명령줄 인터페이스(CLI) 도구

    자세한 내용은 [Apache Storm 설명서](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)를 참조하세요.

    Storm 웹 UI는 HDInsight 클러스터에서 제공됩니다.

    ![HDInsight Storm 규모 균형 재조정](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    다음은 Storm 토폴로지 균형을 다시 조정하는 CLI 명령의 예제입니다.

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```


## <a name="pauseshut-down-clusters"></a>클러스터 일시 중지/종료

대부분의 Hadoop 작업은 이따금 실행되는 일괄 처리 작업입니다. 대부분의 Hadoop 클러스터는 프로세스에 사용되지 않는 기간이 깁니다. HDInsight를 사용하면 데이터가 Azure Storage에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다.
HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 저장소에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다.

프로세스를 프로그래밍할 수 있는 방법은 다양합니다.

* 사용자 Azure 데이터 팩터리. 주문형 HDInsight 연결된 서비스 만들기는 [Azure Data Factory를 사용하여 HDInsight에서 주문형 Linux 기반 Apache Hadoop 클러스터 만들기](hdinsight-hadoop-create-linux-clusters-adf.md) 를 참조하세요.
* Azure PowerShell 사용  [비행 지연 데이터 분석](hdinsight-analyze-flight-delay-data-linux.md)을 참조하세요.
* Azure 클래식 CLI 사용 [Azure 클래식 CLI를 사용하여 HDInsight 클러스터 관리](hdinsight-administer-use-command-line.md)를 참조하세요.
* HDInsight .NET SDK 사용 [Apache Hadoop 작업 제출](hadoop/submit-apache-hadoop-jobs-programmatically.md)을 참조하세요.

가격 정보는 [HDInsight 가격](https://azure.microsoft.com/pricing/details/hdinsight/)을 참조하세요. 포털에서 클러스터를 삭제하려면 [클러스터 삭제](#delete-clusters)



## <a name="upgrade-clusters"></a>클러스터 업그레이드

[HDInsight 클러스터를 최신 버전으로 업그레이드](./hdinsight-upgrade-cluster.md)를 참조하세요.

## <a name="open-the-apache-ambari-web-ui"></a>Apache Ambari 웹 UI 열기

Ambari는 RESTful API에서 지원하는 직관적이고 사용하기 쉬운 Hadoop 관리 웹 UI를 제공합니다. Ambari를 사용하면 시스템 관리자가 Hadoop 클러스터를 관리하고 모니터링할 수 있습니다.

[클러스터 홈 페이지](#homePage)에서 다음을 수행합니다.

1. **클러스터 대시보드**를 선택합니다.

    ![HDInsight Hadoop 클러스터 메뉴](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. 새 페이지에서 **Ambari 홈**을 선택합니다.
2. 클러스터 사용자 이름 및 암호를 입력합니다.  기본 클러스터 사용자 이름은 _admin_입니다. Ambari 웹 UI는 다음과 같습니다.

자세한 내용은 [Apache Ambari 웹 UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

## <a name="change-passwords"></a>암호 변경
HDInsight 클러스터마다 두 개의 사용자 계정이 포함될 수 있습니다. HDInsight 클러스터 사용자 계정 (HTTP 사용자 계정) 및 SSH 사용자 계정 만들기 프로세스 중에 생성 됩니다. 포털을 사용하여 클러스터 사용자 계정 암호를 변경할 수 있으며, 스크립트 작업을 사용하여 SSH 사용자 계정을 변경할 수 있습니다.

### <a name="change-the-cluster-user-password"></a>클러스터 사용자 암호 변경

> [!NOTE]  
> 클러스터 사용자(관리자) 암호를 변경하면 이 클러스터에 대해 실행하는 스크립트 동작이 실패할 수 있습니다. 작업자 노드를 대상으로 하는 지속적인 스크립트 작업이 있는 경우 이러한 스크립트는 작업의 크기 조정을 통해 클러스터에 노드를 추가할 때 실패할 수 있습니다. 스크립트 작업에 대한 자세한 내용은 [스크립트 작업을 사용하여 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)을 참조하세요.

[클러스터 홈 페이지](#homePage)에서 다음을 수행합니다.
1. **설정**에서 **SSH + 클러스터 로그인**을 선택합니다.
2. **자격 증명 다시 설정**을 설정합니다.
3. 텍스트 상자에 새 암호를 입력하고 확인합니다.
4. **확인**을 선택합니다.

암호는 클러스터의 모든 노드에 대해 변경됩니다.

### <a name="change-the-ssh-user-password"></a>SSH 사용자 암호 변경
1. 텍스트 편집기를 사용하여 다음 텍스트를 **changepassword.sh**라는 파일로 저장합니다.

    > [!IMPORTANT]  
    > 줄 끝으로 LF를 사용하는 편집기를 사용해야 합니다. 편집기에서 CRLF를 사용하는 경우 스크립트가 작동하지 않습니다.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. HTTP 또는 HTTPS 주소를 사용하여 HDInsight에서 액세스할 수 있는 저장소 위치에 파일을 업로드합니다. 예를 들어 OneDrive 또는 Azure Blob Storage와 같은 공용 파일 스토리지입니다. 다음 단계에서 이 URI가 필요하므로 URI(HTTP 또는 HTTPS 주소)를 파일에 저장합니다.
3. [클러스터 홈 페이지](#homePage)를 선택 **스크립트 동작** 아래 **설정**.
4. **스크립트 동작** 블레이드에서 **새로운 항목 제출**을 선택합니다. 
5. **스크립트 동작 제출** 블레이드에서 다음 정보를 입력합니다.

   | 필드 | 값 |
   | --- | --- |
   | 스크립트 유형 | 드롭다운 목록에서 **- 사용자 지정**을 선택합니다.|
   | 이름 |“SSH 암호 변경” |
   | Bash 스크립트 URI |Changepassword.sh 파일에 대한 URI |
   | 노드 유형: (헤드, 작업자, Nimbus, 감독자, Zookeeper 등) |나열된 모든 노드 형식에 대한 ✓ |
   | 매개 변수 |SSH 사용자 이름 및 새 암호를 입력합니다. 사용자 이름과 암호 사이에 공백이 하나 있어야 합니다. |
   | 이 스크립트 작업을 유지... |이 필드는 선택 취소로 둡니다. |

6. **만들기**를 선택하여 스크립트를 적용합니다. 스크립트가 완료되면 새 암호와 함께 SSH를 사용하여 클러스터에 연결할 수 있습니다.

## <a name="grantrevoke-access"></a>액세스 권한 부여/해지
HDInsight 클러스터에는 다음과 같은 HTTP 웹 서비스가 있습니다(이러한 모든 서비스에 RESTful 엔드포인트가 있음).

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

이러한 서비스에는 기본적으로 액세스 권한이 부여됩니다. [Azure 클래식 CLI](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) 및 [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access)을 사용하여 액세스 권한을 해지/부여할 수 있습니다.

## <a name="find-the-subscription-id"></a>구독 ID 찾기
각 클러스터가 Azure 구독에 연결됩니다.  Azure 구독 ID가 [클러스터 홈 페이지](#homePage)에 표시됩니다.

## <a name="find-the-resource-group"></a>리소스 그룹 찾기
Azure Resource Manager 모드에서는 각각의 HDInsight 클러스터가 Azure Resource Manager 그룹과 함께 만들어집니다. Resource Manager 그룹이 [클러스터 홈 페이지](#homePage)에 표시됩니다.

## <a name="find-the-storage-accounts"></a>저장소 계정 찾기
HDInsight 클러스터는 Azure Storage 계정 또는 Azure Data Lake Storage를 사용하여 데이터를 저장합니다. 각 HDInsight 클러스터에는 하나의 기본 저장소 계정 및 여러 연결된 저장소 계정이 있을 수 있습니다. 스토리지 계정을 나열하려면 [클러스터 홈 페이지](#homePage)에 있는 **설정**에서 **스토리지 계정**을 선택합니다.


## <a name="monitor-jobs"></a>작업 모니터링
[Apache Ambari 웹 UI를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md#monitoring)를 참조하세요.


## <a name="monitor-cluster-usage"></a>클러스터 사용 현황 모니터링
HDInsight 클러스터 블레이드의 **사용량** 섹션에는 HDInsight에서 사용하기 위해 구독에서 사용할 수 있는 코어 수뿐만 아니라 이 클러스터에 할당된 코어 수 및 이 클러스터 내에서 노드에 할당된 방법에 대한 정보도 표시됩니다. 클러스터 나열 및 표시를 참조하세요.

> [!IMPORTANT]  
> HDInsight 클러스터에 의해 제공되는 서비스를 모니터링하려면 Ambari 웹 또는 Ambari REST API를 사용해야 합니다. Ambari 사용에 대한 자세한 내용은 [Apache Ambari를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)를 참조하세요.

## <a name="connect-to-a-cluster"></a>클러스터에 연결

* [HDInsight에서 Apache Hive 사용](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>다음 단계

이 문서에서는 몇 가지 기본 관리 함수에 대해 배웠습니다. 자세한 내용은 다음 문서를 참조하세요.

* [Azure PowerShell을 사용하여 HDInsight 관리](hdinsight-administer-use-powershell.md)
* [Azure 클래식 CLI를 사용하여 HDInsight 관리](hdinsight-administer-use-command-line.md)
* [HDInsight 클러스터 만들기](hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Ambari 웹 UI 사용에 대한 자세한 내용](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API 사용에 자세한 내용](hdinsight-hadoop-manage-ambari-rest-api.md)
* [HDInsight에서 Apache Hive 사용](hadoop/hdinsight-use-hive.md)
* [HDInsight에서 Apache Pig 사용](hadoop/hdinsight-use-pig.md)
* [HDInsight에서 Apache Sqoop 사용](hadoop/hdinsight-use-sqoop.md)
* [Azure HDInsight 시작](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Azure HDInsight에 포함된 Apache Hadoop 버전](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Hadoop 명령줄"
